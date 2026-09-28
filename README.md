# Math Practice

A single-page mental arithmetic trainer. It deals you one random equation, you type
the answer, and it flashes green or red. Wrong answers don't advance — you have to
solve the question before moving on.

## Running it

No build step, no dependencies. Just open the file:

```
index.html
```

Double-click it in Explorer, or from PowerShell:

```powershell
Start-Process index.html
```

Works in any modern browser. The page is mobile-friendly too, and `inputmode="numeric"`
brings up the number keypad on phones and tablets.

## Using it

- **Checkboxes at the top** — tick or untick `+ − × ÷` to choose which operators appear.
  All four are on by default. The last ticked operator can't be unticked, so there's
  always something to generate. Changing a checkbox deals a new question immediately.
- **Type the answer and press Enter.** There are no buttons, and an empty field does
  nothing — there's no way to skip a question.
- **Correct** turns the equation green and loads the next one.
- **Wrong** turns it red, clears the field, and keeps the same equation up until you
  get it. The answer is never revealed.

## Question counter and stats

Top left is the current question number. Top right tracks:

| Stat | Behaviour |
| --- | --- |
| Score | `10 × min(streak, 5)` per correct answer, so up to 50 points while hot |
| Streak | Consecutive correct answers; resets on any wrong answer |
| Accuracy | `correct ÷ answered` across every attempt, including retries |

## How questions are generated

One operator per question, numbers chosen to stay mentally tractable:

| Operator | Range |
| --- | --- |
| `+` | Two numbers from 2–99, or two from 100–999 about 35% of the time |
| `−` | Same ranges, larger number always first so the result is never negative |
| `×` | Two numbers from 11–149 (e.g. `124 * 143`) |
| `÷` | Divisor 2–12, built by picking the result first (3–150) then multiplying, so it always divides evenly — no remainders |

## Project layout

```
index.html, markup, styles, and all the game logic in one file
```

The whole app is a single `index.html`: inline CSS in `<style>`, vanilla JS in
`<script>`, no modules or external requests. To change how numbers are picked, edit
the `switch (op)` block in `newQuestion()` — each case only needs to set `a`, `b`,
and `result`.

State is kept in module-scope variables (`score`, `streak`, `answered`, `correct`,
`number`) and reset on page reload; nothing is persisted or sent anywhere.
