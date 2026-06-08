# quiqui-questions

Sample question files for [QuiQui](https://github.com/albrechtje/quiqui), a live audience response tool for university lectures.

Each `.yaml` file in this repo represents one lecture's questions. Point QuiQui at this repo URL and select a file to load its questions.

> **Requirements:** the repo must be public and hosted on GitHub. QuiQui checks the repository size via the GitHub API before cloning — repos larger than **1 MB** are rejected. Individual question files larger than **100 KB** are rejected when loaded. Each question may have at most **6 answer options** (A–F). YAML files are validated on load — format errors are shown as a clear error message in the teacher view. A typical lecture file is well under 50 KB.

---

## Question format

Each question is a YAML list item with the following fields:

| Field | Required | Description |
|---|---|---|
| `question` | yes | Question text — plain text, Markdown, or LaTeX |
| `type` | yes | `single` (one answer) or `multiple` (one or more) |
| `answers` | yes | List of answer options |
| `correct` | no | Correct answer letter(s) — bare letter for single (`B`), block sequence for multiple; teacher-only, never shown to students. Omit for unscored/generic questions. |
| `explanation` | no | Optional explanation shown only to the teacher |

The `correct` field uses answer letters (`A`, `B`, `C`, …) — upper or lower case both work. It is used by the teacher's **✓ Reveal** button to highlight the correct options for the whole room. When `correct` is omitted, the Reveal button is hidden — useful when you keep the question text in your slides and only use QuiQui to collect votes.

For a single correct answer, use a bare letter. For multiple correct answers, use a block sequence.

### Single choice

```yaml
- question: "What is the result of `7 // 2` in Python?"
  type: single
  answers:
    - "`3.5`"
    - "`3`"
    - "`4`"
    - "`2`"
  correct: B
  explanation: "// is floor division. 7 / 2 = 3.5, floored to 3."
```

### Multiple choice

```yaml
- question: "Which of these are valid Python data types?"
  type: multiple
  answers:
    - "`int`"
    - "`float`"
    - "`char`"
    - "`str`"
    - "`bool`"
  correct:
    - A
    - B
    - D
    - E
  explanation: "Python has no char type; single characters are strings of length 1."
```

### Code blocks

Use a YAML block scalar (`|`) for multi-line question text with fenced code blocks:

```yaml
- question: |
    What does the following code print?

    ```python
    for i in range(3):
        print(i)
    ```
  type: single
  answers:
    - "`1 2 3`"
    - "`0 1 2`"
    - "`0 1 2 3`"
  correct: B
  explanation: "range(3) produces 0, 1, 2."
```

### LaTeX mathematics

Use `$...$` for inline math and `$$...$$` for a centred display block. Both work in question text and answer options.

```yaml
- question: "Which is the correct expansion of $(a+b)^2$?"
  type: single
  answers:
    - "$a^2 + b^2$"
    - "$a^2 + 2ab + b^2$"
  correct: B
  explanation: "$(a+b)^2 = a^2 + 2ab + b^2$."
```

```yaml
- question: |
    What is the closed-form result of this sum?

    $$\sum_{i=1}^{n} i$$
  type: single
  answers:
    - "$\dfrac{n(n+1)}{2}$"
    - "$n^2$"
  correct: A
  explanation: "Gauss's formula."
```

Code and math can be combined freely in the same question.

### Generic / unscored questions

Omit `correct` when the question text lives in your slides and you only need QuiQui to collect votes. The Reveal button is hidden automatically. Each answer option is labelled with a letter badge (A, B, C, …) — students pick the letter shown on the slide.

`lecture7-generic.yaml` contains ready-to-use templates. Examples:

```yaml
# A–D single choice
- question: "Choose your answer! (A, B, C, D)"
  type: single
  answers:
    - "... is correct"
    - "... is correct"
    - "... is correct"
    - "... is correct"

# Yes / No
- question: "What do you think? (Yes / No)"
  type: single
  answers:
    - "Yes"
    - "No"

# True / False
- question: "Is the statement true or false?"
  type: single
  answers:
    - "True"
    - "False"

# 5-point agreement scale
- question: "What is your rating?"
  type: single
  answers:
    - "Strongly agree"
    - "Agree"
    - "Neutral"
    - "Disagree"
    - "Strongly disagree"
```

> **YAML escaping:** inside quoted strings (`"..."`) backslashes must be doubled: `\\frac`, `\\sum`. Inside block scalars (`|`) a single backslash is fine: `\frac`, `\sum`.

---

## config.yaml

An optional `config.yaml` at the root of the repo configures the session:

```yaml
# Stable URL segment for the student join link: /join/<session_url>
# Students can bookmark this — it stays the same across all questions in a lecture.
# If omitted, a random short ID is generated each time a question is activated.
session_url: demo

# Optional: a custom shortlink students can type instead of the long /join/ URL.
# student_shortlink: https://t.ly/your-code

# Display name shown in the header and browser tab as "QuiQui: <title>"
# Appears on both teacher and student views.
title: Demo Quiz
```

> **`session_url` must be unique across all users of the same QuiQui instance.** If two lecturers use the same name (e.g. `demo` or `python101`) at the same time, the second pull will fail with a conflict error. Prefix your URL with an organisation or course abbreviation to avoid clashes — for example `tum-python101` or `ki-zentrum-demo`.

### Optional: `student_shortlink`

The student join URL (`/join/<session_url>`) can be long and awkward to type. If you set up a shortlink on any URL shortener (e.g. [t.ly](https://t.ly), bit.ly, or your institution's own service) and point it at your join URL, add it as `student_shortlink` and QuiQui will show it in the teacher view (below the student link) and on the projector view in place of the long join URL — handy to read out or put on a slide. The QR code still encodes the real join URL, so scanning always works.

QuiQui treats it as display-only: it does **not** create the shortlink or check where it points, so make sure your shortener actually redirects to `<your-quiqui-host>/join/<session_url>`.

---

## Files in this repo

| File | Topic |
|---|---|
| `config.yaml` | configures title and url slug |
| `lecture1-python-basics.yaml` | Python basics — variables, types, loops |
| `lecture2-python-maths.yaml` | Python for maths — NumPy, float precision, recursion |
| `lecture3-maths.yaml` | Linear algebra and statistics — eigenvectors, normal distribution, correlation |
| `lecture4-economics.yaml` | Microeconomics — demand, market equilibrium, elasticity (English) |
| `lecture5-sozialwissenschaften.yaml` | Sozialwissenschaften — Schichtung, Bürokratie, Forschungsmethoden (Deutsch) |
| `lecture6-geschichte.yaml` | Geschichte — Weltkriege, Weimarer Republik, Französische Revolution (Deutsch) |
| `lecture7-generic.yaml` | Generic answer templates — A/B/C/D, Yes/No, True/False; use when your question text is in your slides |
