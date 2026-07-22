# Demo questions for QuiQui - the completely free and open live audience response tool

Sample question files for [QuiQui](https://github.com/th-nuernberg/quiqui), a live audience response tool for lectures, meetings, and talks.

Each `.yaml` file is one set of questions. Load one **directly from your computer** (see below) or **point QuiQui at a public GitHub repo** like this one and select a file. Either way, the question format is identical — this README is the reference.  
New here? Start with the [Quickstart Guide](https://github.com/th-nuernberg/quiqui/blob/main/QUICKSTART.md).

> **Limits:** each question file must be under **100 KB**, with at most **6 answer options** (A–F) per question. YAML is validated on load — format errors show as a clear message in the host view. A typical lecture file is well under 50 KB. For the GitHub route, the repo must be **public** and under **1 MB** total (checked via the GitHub API before cloning).

---

## Files in this repo

| File | Purpose |
|---|---|
| `lesson*.yaml` | A guided tour of QuiQui — load each one and run it. The questions themselves teach what QuiQui is, how to load and format questions, images, unscored templates, self-hosting, and a German mixed-topic set. |
| `self-contained-example.yaml` | A self-contained file (own `config:` + `questions:`) — download it, edit it, load it via **From file**, no GitHub needed. Your starting template. |
| `config.yaml` | Session config for this repo — `session_url`, `title`, `shuffle`. Documented inline. |
| `images/` | Images referenced by repo-relative paths (see `lesson4-images.yaml`). |

The `lesson*.yaml` files double as this repo's documentation: they walk through every feature by demonstrating it. Start with `lesson1-about-quiqui.yaml`.

---

## No GitHub? Use a self-contained file

You don't need a GitHub account or this repo to try QuiQui: [`self-contained-example.yaml`](self-contained-example.yaml) is a single file with both the questions **and** its own `config:` section. Load it on the host page via "From file" — a session starts straight from your computer.

**To download it:** right-click **[this direct link](https://raw.githubusercontent.com/th-nuernberg/quiqui-questions/main/self-contained-example.yaml)** → *Save link as…*. (Opening the file [in GitHub's normal view](self-contained-example.yaml) shows it but doesn't download it — you'd have to click through to the raw/download button there.)

The same file also works the normal way: drop it into a repo like this one and select it from the file dropdown after pulling. In that case its `config:` section is ignored — the repo's own `config.yaml` leads — only the `questions:` are read.

```yaml
config:
  session_url: example
  title: "QuiQui Example (self-contained)"

questions:
  - question: "Which of these is the capital of France?"
    type: single
    answers: ["Lyon", "Marseille", "Paris", "Nice"]
    correct: C
```

Local uploads can't resolve repo-relative images (there's no repo behind them) — use an external `https://` image URL if you need one.

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
| `shuffle` | no | Override answer shuffling for this one question: `true` forces its answers to shuffle, `false` keeps them fixed — either way overriding the repo-wide `shuffle` in [`config.yaml`](config.yaml) |

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

Here with answers randomly shuffled.

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
  shuffle: true
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

    ![Imperial Castle](https://upload.wikimedia.org/wikipedia/commons/thumb/d/da/Burg_N%C3%BCrnberg_02.jpg/500px-Burg_N%C3%BCrnberg_02.jpg)
  type: single
  answers: ["Munich", "Nuremberg", "Berlin"]
  correct: B
```

The image is fetched from that URL at display time, so it only works while that host keeps it online.

Linking to the original source (e.g. Wikimedia Commons) is the best option **for anything you don't hold the rights to**.

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
- **Mind copyright:** Publish only images produced by yourself or with a permissive license this way.

### More formatting examples

`lesson3-formatting.yaml` and `lesson4-images.yaml` have runnable examples of the formatting options above — Markdown, LaTeX, code blocks, raw HTML (colour, highlight, sub/superscript), and the two ways to include images. Load and run them to see each feature rendered. They don't cover every case — tables and blockquotes, for instance, also render fine (Markdown via `marked`, sanitised by DOMPurify) — so extend them as new needs come up.

### Generic / unscored questions

Omit `correct` when the question text lives in your slides and you only need QuiQui to collect votes. The Reveal button is hidden automatically. Each answer option is labelled with a letter badge (A, B, C, …) — participants pick the letter shown on the slide.

`lesson5-generic-templates.yaml` contains ready-to-use templates. Examples:

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

Save the assistant's output as a `.yaml` file and load it in QuiQui — either directly from your computer via **From file**, or by committing it to your repo and selecting it after pulling. If QuiQui reports a validation error in the host view, paste the error back to the assistant and ask it to fix the file.

> **For a self-contained file** (loaded via **From file**, no repo): the prompt above produces a bare list of questions. Wrap it in a `config:` + `questions:` map — copy the `config:` block from [`self-contained-example.yaml`](self-contained-example.yaml), set your `session_url`, and paste the generated questions under `questions:`.

---

## config.yaml

An optional `config.yaml` at the repo root configures the session — `session_url`, `title`, and the optional `host_shortlink` and `shuffle`. **Every field is documented inline in [`config.yaml`](config.yaml) itself** — open it and read the comments there; they're the authoritative reference.

The one field worth getting right is **`session_url`**, the participant join address (`/join/<session_url>`). A session is identified by its `session_url`, *not* by the lecturer: everyone running the same value on the same server at the same time lands in **one** shared session — same question, votes mixed together. QuiQui warns you before taking over a session that's already **live**, but the real fix is a value that's unmistakably yours. **Best practice — prefix with abbreviations that no one else would use.** Combine institution + course + lecturer (or any subset that makes it unmistakably yours), e.g. `thn-db-alb` rather than `databases`.

