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

- Which AI tools did you use on this project (for example: ChatGPT, Gemini, Copilot)?
- Give one example of an AI suggestion that was correct (including what the AI suggested and how you verified the result).
- Give one example of an AI suggestion that was incorrect or misleading (including what the AI suggested and how you verified the result).

---

## 3. Debugging and testing your fixes

- How did you decide whether a bug was really fixed?
- Describe at least one test you ran (manual or using pytest)  
  and what it showed you about your code.
- Did AI help you design or understand any tests? How?

---

## 4. What did you learn about Streamlit and state?

- How would you explain Streamlit "reruns" and session state to a friend who has never used Streamlit?

---

## 5. Looking ahead: your developer habits

- What is one habit or strategy from this project that you want to reuse in future labs or projects?
  - This could be a testing habit, a prompting strategy, or a way you used Git.
- What is one thing you would do differently next time you work with AI on a coding task?
- In one or two sentences, describe how this project changed the way you think about AI generated code.
