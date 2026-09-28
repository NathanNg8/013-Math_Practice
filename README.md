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
- **Max digits, top right** — the widest number allowed in a question, from `1` to `7`.
  `1` means 0–9, `2` means up to 99, `3` up to 999, and so on. Defaults to `3`.
  Changing it deals a new question immediately.
- **Type the answer — it checks itself.** Stop typing for 0.7s and the answer is
  graded automatically; there is no button and Enter is optional. Grading on every
  keystroke would mark `1` wrong halfway through typing `12`, so the pause is what
  marks "I'm done". An empty field does nothing, so there's no way to skip a question.
- **Correct** turns the equation green and loads the next one.
- **Wrong** turns it red, clears the field, and keeps the same equation up until you
  get it. The answer is never revealed.

## Question counter and stats

Top left is the current question number. Top right holds the max-digits setting and
these stats:

| Stat | Behaviour |
| --- | --- |
| Score | `10 × min(streak, 5)` per correct answer, so up to 50 points while hot |
| Streak | Consecutive correct answers; resets on any wrong answer |
| Accuracy | `correct ÷ answered` across every attempt, including retries |

## How questions are generated

One operator per question. Each operand picks its **own** digit count from `1` to your
max-digits setting, but about 70% of the time both operands are given the *same* digit
count, so questions usually look like `293405 + 656779` rather than
`1 + 1231242`. The rest mix sizes freely, which keeps the odd wide-gap question in the
mix. No leading zeros: a 1-digit operand is 1–9, otherwise it's the full
`10^(d-1)` to `10^d - 1` range.

| Operator | Range |
| --- | --- |
| `+` | Two independently sized operands, e.g. `7 + 4`, `91 + 10038` |
| `−` | Same, operands swapped when needed so the result is never negative |
| `×` | Two independently sized operands (e.g. `5299 * 5`) |
| `÷` | Always even, no remainders. Usually the dividend and divisor share a digit count: a divisor is picked, then a multiple of it in the same range. Otherwise a quotient within the cap is picked and multiplied by a divisor small enough to stay under it |

The digit cap is 7 rather than higher because `9999999 * 9999999` overflows the
exact-integer range JavaScript can compare reliably.

## Project layout

```
index.html   markup, styles, and all the game logic in one file
```

The whole app is a single `index.html`: inline CSS in `<style>`, vanilla JS in
`<script>`, no modules or external requests. To change how numbers are picked, edit
`randOperand()` (which picks a random digit count up to `maxDigits` and then a number
of that size) and the `switch (op)` block in `newQuestion()` — each case only needs to
set `a`, `b`, and `result`.

State is kept in module-scope variables (`score`, `streak`, `answered`, `correct`,
`number`, `maxDigits`) and reset on page reload; nothing is persisted or sent anywhere.
`locked` guards the 0.6s gap after a correct answer so a keystroke landing mid-transition
can't be graded against the old question.
