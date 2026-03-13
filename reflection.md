# 💭 Reflection: Game Glitch Investigator

Answer each question in 3 to 5 sentences. Be specific and honest about what actually happened while you worked. This is about your process, not trying to sound perfect.

## 1. What was broken when you started?

When I first ran the game, it looked like a normal number guessing game — a text input, a Submit button, and a difficulty selector. But as soon as I started playing, things felt off. Even when I typed the exact secret number shown in the Developer Debug Info panel, the game would not let me win on certain attempts. The hints were also pointing me in the wrong direction, making the game more confusing with every guess.

Here are the concrete bugs I noticed:

- **Hints were backwards:** When my guess was too high, the hint said "📈 Go HIGHER!" and when it was too low it said "📉 Go LOWER!" — the exact opposite of what I needed. This made it impossible to converge on the right answer by following the hints.

- **Can't win on even-numbered attempts:** On every second attempt (attempt 2, 4, 6...), the game secretly converted the number to a string before comparing. This caused the wrong result — for example, comparing `int(50)` to `"50"` would fail, and string comparisons like `"9" > "50"` are evaluated alphabetically, not numerically. So even guessing the correct number on an even attempt would not register as a win.

- **Attempts counter starts at 1 instead of 0:** The `attempts` variable was initialized to `1`, so on the very first guess it jumped to `2`. This meant the "Attempts left" display was always one short, and the score penalty kicked in earlier than expected.

---

## 2. How did you use AI as a teammate?

I used **Claude Code** (an AI assistant inside VS Code) as my primary AI tool throughout this project. I gave it the full codebase as context and asked it to identify all bugs, explain the logic behind each one, and help me fix them step by step.

**Correct suggestion:** Claude correctly identified that the hints were backwards — when `guess > secret`, the original code returned "Go HIGHER!" instead of "Go LOWER!". I verified this by playing the game myself with the Developer Debug Info panel open, confirming that following the hints led me further from the answer. I then ran `pytest` and the new `test_hint_message_too_high` test confirmed the fix.

**Incorrect or misleading suggestion:** Claude initially labelled the "Hard" difficulty range (`1–50`) as a bug that needed to be changed to `1–500`. While the range was arguably wrong in spirit (Hard should be harder), the original starter code may have intended a smaller range for a different reason. I accepted the change since it made logical sense, but I should have verified with the assignment spec first rather than just trusting the AI's interpretation.**Refactoring with AI:** Claude also used Agent mode to move all four logic functions out of `app.py` and into `logic_utils.py` in one step. I reviewed the diff carefully before accepting it to make sure nothing was accidentally removed or broken.

---

## 3. Debugging and testing your fixes

I decided a bug was truly fixed only when two conditions were both met: the `pytest` tests passed, and I could manually reproduce the expected behaviour in the running Streamlit app. Passing tests alone were not enough because the tests only cover logic — the UI wiring could still be broken.

**Test I ran:** I ran `pytest tests/test_game_logic.py -v` after fixing the hints. Before the fix, `test_hint_message_too_high` failed with `AssertionError: assert "HIGHER" not in "📈 Go HIGHER!"`. After swapping the messages in `check_guess`, all 5 tests passed. This confirmed the fix was applied in the right place (`logic_utils.py`) and that `app.py` was correctly importing from it.

**AI help with tests:** Yes — Claude pointed out that the original test file had a bug too. All three starter tests were comparing `check_guess(...)` directly to a string like `"Win"`, but `check_guess` returns a tuple `(outcome, message)`. Claude suggested unpacking the result with `outcome, message = check_guess(...)` and asserting on `outcome` only. I verified this made sense by reading the function signature myself before accepting the change.

---

## 4. What did you learn about Streamlit and state?

Streamlit works differently from a normal Python script — every time the user interacts with the app (clicks a button, types in a box), the entire script runs from top to bottom again. I'd explain it to a friend like this: imagine every button click refreshes the whole page from scratch, like reloading a website. That means any variable you create normally just resets to its original value on every click.

To keep data alive across these reruns, Streamlit provides `st.session_state` — a dictionary that persists between reruns. Think of it like a sticky note that survives the page refresh. That's why the secret number and attempt counter are stored in `st.session_state` rather than as plain variables. The bug in this project was that `attempts` was initialised to `1` inside an `if "attempts" not in st.session_state` block — this ran correctly on the first load, but once it was set, it stayed at whatever value it had, even if that starting value was wrong.

---

## 5. Looking ahead: your developer habits

**Habit to reuse:** Writing targeted pytest tests immediately after fixing a bug — not just to check the fix works, but to lock in the correct behaviour so it can't silently break again later. In this project, adding `test_hint_message_too_high` and `test_hint_message_too_low` meant the hint logic was now protected by an automated check, not just a mental note.

**What I'd do differently:** Next time I work with AI on a coding task, I would describe the bug I'm seeing before asking for a fix — giving the AI the symptom, not just the code. In this project, Claude sometimes jumped to fixing things I hadn't fully confirmed were wrong (like the Hard difficulty range). If I had described what I observed in the running app first, the AI's suggestions would have been more targeted and I would have had an easier time verifying them.

**How this changed my thinking:** This project showed me that AI-generated code can have subtle, compounding bugs that look plausible at first glance — like a scoring function that rewards wrong guesses on even turns. I'll never assume AI-generated code is correct just because it runs without errors; I'll always play through the logic manually and back it up with tests.
