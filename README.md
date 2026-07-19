# Demo questions for QuiQui - the completely free and open live audience response tool

Sample question files for [QuiQui](https://github.com/th-nuernberg/quiqui), a live audience response tool for lectures, meetings, and talks.

Each `.yaml` file in this repo represents one set of questions. Point QuiQui at this repo URL and select a file to load its questions. Fork this repo to build your own question deck.  
See the [Quickstart Guide](https://github.com/th-nuernberg/quiqui/blob/main/QUICKSTART.md) for reference.

> **Requirements:** the repo must be public and hosted on GitHub. QuiQui checks the repository size via the GitHub API before cloning — repos larger than **1 MB** are rejected. Individual question files larger than **100 KB** are rejected when loaded. Each question may have at most **6 answer options** (A–F). YAML files are validated on load — format errors are shown as a clear error message in the host view. A typical lecture file is well under 50 KB.

---

## Question format

Each question is a YAML list item with the following fields:

| Field | Required | Description |
|---|---|---|
| `question` | yes | Question text — plain text, Markdown, or LaTeX |
| `type` | yes | `single` (one answer) or `multiple` (one or more) |
| `answers` | yes | List of answer options |
| `correct` | no | Correct answer letter(s) — bare letter for single (`B`), block sequence for multiple; host-only, never shown to participants. Omit for unscored/generic questions. |
| `explanation` | no | Optional explanation shown only to the host |
| `shuffle` | no | Set `false` to keep this question's answers in a fixed order when `shuffle: true` is set in `config.yaml` (see [Optional: `shuffle`](#optional-shuffle)) |

The `correct` field uses answer letters (`A`, `B`, `C`, …) — upper or lower case both work. It is used by the host's **✓ Reveal** button to highlight the correct options for the whole room. When `correct` is omitted, the Reveal button is hidden — useful when you keep the question text in your slides and only use QuiQui to collect votes.

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

### Images

Include an image in a question (or answer) with standard Markdown image syntax, `![alt text](source)`. There are two ways to provide the source:

**1. An external URL** — link to an image hosted elsewhere (Wikimedia, your website, a CDN):

```yaml
- question: |
    Where is this landmark?

    ![Eiffel Tower](https://upload.wikimedia.org/wikipedia/commons/thumb/b/b7/Paris_-_The_Eiffel_Tower_in_spring_-_2307.jpg/500px-Paris_-_The_Eiffel_Tower_in_spring_-_2307.jpg)
  type: single
  answers: ["London", "Paris", "Berlin"]
  correct: B
```

The image is fetched from that URL at display time, so it only works while that host keeps it online.

**2. A file shipped in this repo** — commit the image alongside your YAML and reference it with a **repo-relative path**. QuiQui serves it from the pulled clone, so the question repo is fully self-contained — nothing depends on external hosting:

```yaml
- question: |
    Which lamp is lit?

    ![Traffic light](images/traffic-light.svg)
  type: single
  answers: ["Red", "Yellow", "Green"]
  correct: C
```

Here `images/traffic-light.svg` is a file in this repo (see the `images/` folder). Any relative path resolves against the repo root — put images wherever you like (`images/`, `figures/…`) and reference them accordingly. A leading `./` is fine too.

- **Supported formats:** `.png`, `.jpg`/`.jpeg`, `.gif`, `.webp`, `.avif`, `.svg`.
- **Size:** each image must be under **512 KB**, and the whole repo (images included) under **1 MB** — keep figures at slide resolution, not full-camera size.
- Only image files are served this way; other files in the repo are not web-accessible.

**Which to use — mind copyright.** This repo is public. Committing an image to it publishes and redistributes that file, which is a different (and legally heavier) act than merely linking to where someone else already hosts it.

- Use a **local file (option 2)** only for material you made yourself or that's under a license permitting redistribution (CC0, CC-BY, public domain, etc.) — note the license/source in a comment near the image or question, as `images/traffic-light.svg` does.
- Use an **external URL (option 1)** for anything you don't hold the rights to — book figures, photos, diagrams pulled from a slide deck, stock images. Linking to the original host (e.g. Wikimedia Commons) is not redistribution; committing that same file to this repo would be.
- When in doubt, link, don't commit.

### More formatting examples

`formatting-examples.yaml` has runnable examples of the formatting options above plus a few more: naked URLs, Markdown-style links, images (both an external URL and a local `images/traffic-light.svg` from this repo), and raw HTML such as colored text, highlights, and sub/superscript. It doesn't cover every possible case — tables and blockquotes, for instance, also render fine (Markdown via `marked`, sanitised by DOMPurify) but aren't demonstrated yet. Feel free to extend the file with more examples as new formatting needs come up.

### Generic / unscored questions

Omit `correct` when the question text lives in your slides and you only need QuiQui to collect votes. The Reveal button is hidden automatically. Each answer option is labelled with a letter badge (A, B, C, …) — participants pick the letter shown on the slide.

`generic.yaml` contains ready-to-use templates. Examples:

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

## Generate questions with an AI assistant

Copy the prompt below into ChatGPT, Claude, or any other LLM and fill in the bracketed parts. It fully describes the YAML format QuiQui expects, so the assistant can produce a file you can drop straight into this repo.

````text
You are helping me write a question file for QuiQui, a live audience-response
tool for lectures, meetings, and talks. Produce ONE valid YAML file following the exact
format described below. Output only the YAML, inside a single code block, with
no commentary before or after.

WHAT I WANT:
- Topic: [FILL IN — e.g. "Python basics: variables, loops, data types"]
- Number of questions: [FILL IN — e.g. 8]
- Language: [FILL IN — e.g. English / Deutsch]
- Difficulty / audience: [FILL IN — e.g. first-year undergraduates]
- Scored or unscored: [FILL IN — "scored" (include the correct answer) or
  "unscored" (omit the correct answer; my question text is on my slides)]

YAML FORMAT (follow exactly):
- The file is a YAML list. Each list item is one question.
- Each question has these fields:
  - question: (REQUIRED) the question text. Plain text, Markdown, or LaTeX.
  - type: (REQUIRED) either "single" (exactly one correct answer) or
    "multiple" (one or more correct answers).
  - answers: (REQUIRED) a YAML list of 2 to 6 answer options. Never more than 6.
  - correct: (REQUIRED only for scored questions; OMIT entirely for unscored)
    answer letters A, B, C, … matching the answer order (A = first answer).
    * For type "single": a single bare letter, e.g.  correct: B
    * For type "multiple": a YAML list of letters, e.g.
        correct:
          - A
          - C
  - explanation: (OPTIONAL) a one- or two-sentence explanation. Shown only to
    the host, never to participants. Include it for scored questions.

FORMATTING RULES:
- Markdown is allowed in question text and answers: use backticks for `code`,
  **bold**, etc.
- Images use Markdown syntax ![alt](source). The source can be an external URL,
  or a path to an image file shipped in the repo, e.g. ![](images/figure.svg).
  Only add an image if I ask for one — do not invent image files that don't exist.
- LaTeX maths is allowed: $...$ for inline, $$...$$ for a centred display block.
- For multi-line question text (fenced code blocks, display maths), use a YAML
  block scalar with the pipe character:
      question: |
        First line.

        ```python
        print("hello")
        ```
- ESCAPING (important): inside double-quoted strings, every backslash must be
  DOUBLED — write "$\\frac{a}{b}$", "$\\sum_{i=1}^{n}$". Inside a block scalar
  (the | form) a single backslash is fine — write $\frac{a}{b}$.
- Keep each question to at most 6 answers.

EXAMPLE of a valid scored question:
- question: "What is the result of `7 // 2` in Python?"
  type: single
  answers:
    - "`3.5`"
    - "`3`"
    - "`4`"
    - "`2`"
  correct: B
  explanation: "// is floor division: 7 / 2 = 3.5, floored to 3."

Now generate the file for the topic and settings above.
````

Save the assistant's output as `your-lesson-name.yaml`, commit it to your fork of this repo, and load it in QuiQui by selecting the file after pulling the repo. If QuiQui reports a validation error in the host view, paste the error back to the assistant and ask it to fix the file.

---

## config.yaml

An optional `config.yaml` at the root of the repo configures the session:

```yaml
# Stable URL segment for the participant join link: /join/<session_url>
# Participants can bookmark this — it stays the same across all questions
# for a given question repository.
# If omitted, a random short ID is generated each time a question is activated.
session_url: demo

# Optional: a custom shortlink participants can type instead of the long /join/ URL.
# (QuiQui also still reads the older key `student_shortlink` as a fallback.)
# host_shortlink: https://t.ly/your-code

# Optional: randomise each question's answer order (default false). See below.
# shuffle: true

# Display name shown in the header and browser tab as "QuiQui: <title>"
# Appears on both host and participant views.
title: Demo Quiz
```

> **`session_url` must be unique across all users of the same QuiQui instance.** See [Choosing a `session_url`](#choosing-a-session_url) below — getting this wrong silently merges two lecturers' sessions.

> **Allowed characters:** letters, digits, hyphen (`-`) and underscore (`_`), 1–64 characters. No spaces, slashes, or dots — the `session_url` becomes part of the join URL and the QR code, so an invalid value is rejected when you pull the repo (and would otherwise break the projector's QR code).

### Choosing a `session_url`

A session is identified by its `session_url`, **not by the lecturer**. Everyone running with the same `session_url` on the same server at the same time shares **one** live session — the same active question, the same vote tally, the same results. Two lecturers who collide will silently overwrite each other's active question and mix their participants' votes together. (The session only fails with an explicit conflict error in one narrow case: two *different* repos declaring the same `session_url` at the same time. Sharing the *same* repo gives no error at all — everyone just lands in one shared session.)

This is easy to get wrong, because the natural choice is a generic abbreviation of the lecture — `databases`, `programming`, `nlp`. **These are exactly the names most likely to collide**, because another lecturer teaching the same subject will reach for the same word. The same trap applies if you and colleagues share one question repo: you all inherit its single `session_url`.

**Best practice — prefix with abbreviations that no one else would use.** Combine institution + course + lecturer (or any subset that makes it unmistakably yours):

| ❌ Too generic (will collide) | ✅ Unique |
|---|---|
| `databases` | `thn-db-alb` (TH Nürnberg · Databases · Albrecht) |
| `programming` | `tum-prog1-mueller` |
| `nlp` | `lmu-nlp-ws25` |
| `demo` | `ki-zentrum-demo` |

The `session_url` only needs to be unique among sessions running **at the same time** on the same server — but since you can't know what colleagues picked, a personal prefix is the safe default. Pick it once, put the QR code in your slides, and it stays stable for the whole semester.

> If you and colleagues want to run the **same demo** simultaneously, each of you must fork this repo and give your fork a unique `session_url`.

### Optional: `host_shortlink`

The participant join URL (`/join/<session_url>`) can be long and awkward to type. If you set up a shortlink on any URL shortener (e.g. [t.ly](https://t.ly), bit.ly, or your institution's own service) and point it at your join URL, add it as `host_shortlink` and QuiQui will show it in the host view (below the participant link) and on the projector view in place of the long join URL — handy to read out or put on a slide. The QR code still encodes the real join URL, so scanning always works. (QuiQui also still reads the older key `student_shortlink` as a fallback, for repos written before this field was renamed.)

QuiQui treats it as display-only: it does **not** create the shortlink or check where it points, so make sure your shortener actually redirects to `<your-quiqui-host>/join/<session_url>`.

### Optional: `shuffle`

Set `shuffle: true` to randomise each question's answer order (default `false`). A single question can opt out with `shuffle: false` on that question. See [`about-quiqui.yaml`](about-quiqui.yaml) for a live demo.

---

## Files in this repo

| File | Topic |
|---|---|
| `config.yaml` | configures title and url slug |
| `about-quiqui.yaml` | Questions about QuiQui itself — also a live demo of the `shuffle` option |
| `lesson1-python-basics.yaml` | Python basics — variables, types, loops |
| `lesson2-python-maths.yaml` | Python for maths — NumPy, float precision, recursion |
| `lesson3-maths.yaml` | Linear algebra and statistics — eigenvectors, normal distribution, correlation |
| `lesson4-economics.yaml` | Microeconomics — demand, market equilibrium, elasticity (English) |
| `lesson5-sozialwissenschaften.yaml` | Sozialwissenschaften — Schichtung, Bürokratie, Forschungsmethoden (Deutsch) |
| `lesson6-geschichte.yaml` | Geschichte — Weltkriege, Weimarer Republik, Französische Revolution (Deutsch) |
| `generic.yaml` | Generic answer templates — A/B/C/D, Yes/No, True/False; use when your question text is in your slides |
| `formatting-examples.yaml` | Showcase of supported text formatting — Markdown, raw HTML (color, highlight, sub/superscript), naked URLs, Markdown links, code, LaTeX |
