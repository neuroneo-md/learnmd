# LearnMD — Format Specification v0.2

## Core principle: Markdown first

LearnMD is the **companion format to QuizMD**: where QuizMD covers assessment (testing what you know), LearnMD covers instruction (explaining what to know). Together they form a complete **teach → assess** stack, all in portable plain-text files.

**A complete learning path — chapters, lessons, exercises, quizzes — can live in a single valid `.learn.md` file.** The `!import` directive is an optimization tool for reusability, not a prerequisite.

| Principle | Description |
|-----------|-------------|
| **Markdown-first** | A `.learn.md` file is valid Markdown — readable in any editor |
| **Git-native** | Versionable, diffable, and mergeable like code |
| **AI-native** | Generatable and consumable by LLMs without special tooling |
| **Progressively enriched** | Plain text (Level 0) up through special fenced blocks (Level 2) |
| **QuizMD-interoperable** | Inline ` ```quiz ` blocks and `!import` directive to embed checkpoints |

---

## Format levels

| Level | Mechanism | Purpose |
|-------|-----------|---------|
| 0 | Plain `.learn.md`, pure Markdown | Minimal learning content, human-readable |
| 1 | YAML frontmatter + GFM callouts | Metadata, estimated time, language |
| 2 | Special fenced blocks + directives | Examples, summaries, inline quizzes, imports |

Each level is a strict superset of the previous one. A Level 0 file is valid at Level 1 and 2.

---

## Document architecture

### Three-tier hierarchy

```
path (.learn.md, minimal or no frontmatter)
└── module (## heading)
    └── lesson (### heading or file imported via !import)
```

- Structure is **strictly linear** in v0.2 (no branching)
- ` ```quiz ` blocks and `!import` directives are usable at any level
- External content is referenced via native Markdown links `[text](url)` and `![alt](url)`

---

## Level 0 — Plain Markdown

### Conventions

| Syntax | Meaning |
|--------|---------|
| `# Title` | Document title (inferred by the parser if absent from frontmatter) |
| `## Module title` | Main section heading |
| `### Lesson title` | Sub-section heading |
| `> text` | Generic blockquote or note |
| `!import ./file.learn.md` | Include another lesson file |
| `!import ./file.quiz.md` | Embed a QuizMD checkpoint from an external file |
| `$...$` | Inline LaTeX math formula |
| `$$...$$` | Block (display) LaTeX math formula |

### Minimal example

```markdown
# Introduction to Python

## Module 1 — Variables

A variable is a named reference to a value in memory.

```python
age = 25
```

## Module 2 — Conditions

An `if` statement runs code only when a condition is true.

```python
if age >= 18:
    print("Adult")
```
```

---

## Level 1 — YAML frontmatter

A YAML block at the top of the `.learn.md` file, between two `---` lines.

```yaml
---
title: Python Variables       # optional — inferred from the first # H1 if absent
lang: en                      # REQUIRED — BCP-47 code (en, fr, en-US, …)
estimated_time: 15min         # optional — free-form duration string
tags: [python, variables]     # optional — list of strings
author: Jane Smith            # optional — string or {name, email, url}
---
```

### Frontmatter field reference

| Field | Required | Type | Description |
|-------|----------|------|-------------|
| `title` | No | string | Overrides the first `# H1`. Inferred from H1 if absent. |
| `lang` | **Yes** | BCP-47 | Language code: `en`, `fr`, `en-US`, etc. |
| `estimated_time` | No | string | Free-form estimated reading/study time: `15min`, `1h30`, `2h` |
| `tags` | No | string[] | Thematic tags |
| `author` | No | string or object | Author name, or `{name, email, url}` |

`lang` is the only required field. All other fields are optional.

### GFM callouts

Callouts use GitHub Flavored Markdown syntax and are rendered with visual emphasis by compatible players.

**Supported everywhere** (GitHub, Obsidian, neuroneo.md):

| Syntax | Semantic | Typical use |
|--------|----------|-------------|
| `> [!note]` | Note | Supplementary information |
| `> [!tip]` | Tip | Best practice, shortcut, helpful advice |
| `> [!warning]` | Warning | Common pitfall, frequent mistake |
| `> [!important]` | Important | Critical point to remember |
| `> [!caution]` | Caution | Risk of error or data loss |

**Supported on Obsidian and neuroneo.md** (degrade gracefully to a blockquote on GitHub):

| Syntax | Semantic | Typical use |
|--------|----------|-------------|
| `> [!summary]` | Summary | Key takeaways at the end of a lesson |
| `> [!example]` | Example | Non-code illustrative example |

```markdown
> [!warning]
> In Python, variable names are case-sensitive:
> `Age` and `age` are two different variables.
```

```markdown
> [!tip]
> Use descriptive names: `student_count` is clearer than `n`.
```

```markdown
> [!summary]
> - Variables associate a name with a value
> - Python infers types dynamically
> - Names are case-sensitive: `Age` ≠ `age`
```

```markdown
> [!example] Tokenising a sentence
> Input: "Hello world!"
> Tokens: ["Hello", " world", "!"]  → 3 tokens
```

---

## Level 2 — Special fenced blocks and directives

Special blocks follow the same fenced syntax as QuizMD (` ```type `). They add structured, semantically meaningful containers to the lesson content.

### ` ```quiz ` — Inline checkpoint

Embeds a **single question** using QuizMD syntax directly in the lesson. All QuizMD question types are supported: `mcq`, `multi`, `open`, `tf`, `match`, `order`.

**Syntax:**

`````markdown
```quiz
? What operator assigns a value in Python?
- [x] =
- [ ] ==
- [ ] :=
```
`````

The question starts with `?` followed by the question text. Answer choices use `- [x]` (correct) and `- [ ]` (incorrect), identical to QuizMD Level 0.

**Attributes** (appended after the word `quiz` on the opening line):

| Attribute | Default | Description |
|-----------|---------|-------------|
| `scored:false` | Yes (default) | Practice mode — immediate feedback, no score recorded |
| `scored:true` | — | Scored checkpoint — contributes to lesson score |

```markdown
```quiz scored:true
? What is the type of `42` in Python?
- [x] int
- [ ] float
- [ ] str
```
```

**Inline quiz vs external file:**

| Need | Syntax |
|------|--------|
| Single simple question | Inline ` ```quiz ` block |
| Multiple questions, advanced scoring, or shared config | `!import ./file.quiz.md` directive |

### Composition directives

#### `!import <path>`

Includes content from another file at the current position. The file type is detected from the extension:

- **`.learn.md`** — lesson content is inserted inline (frontmatter ignored)
- **`.quiz.md`** — renders as an interactive QuizMD checkpoint

```markdown
!import ./03-conditions.learn.md
!import ./check-variables.quiz.md
```

Behavior:
- The imported file's content is inserted at the position of the directive.
- For `.learn.md`: frontmatter is ignored — only the content is included.
- Imports are recursive: an imported file may itself contain `!import` directives.
- Circular imports are silently skipped.

---

## Math support

LearnMD uses the same math syntax as QuizMD. LaTeX formulas are rendered via KaTeX and are **auto-detected**: no frontmatter flag is required.

| Form | Syntax | Rendering |
|------|--------|-----------|
| Inline | `$E = mc^2$` | Embedded in the line of text |
| Block (display) | `$$\int_0^\infty e^{-x}\,dx = 1$$` | Centered on its own line |

Math may appear in any part of the document: body text, callouts, and inline quiz questions.

```markdown
The derivative is defined as:

$$f'(x) = \lim_{h \to 0} \frac{f(x+h) - f(x)}{h}$$

Applying this to $f(x) = x^2$, we get $f'(x) = 2x$.
```

The supported subset is **KaTeX** (see [katex.org/docs/support_table](https://katex.org/docs/support_table.html)).

---

## Syntax reference table

| Element | Syntax | Level |
|---------|--------|-------|
| Document title | `# Title` | 0 |
| Module heading | `## Module title` | 0 |
| Lesson heading | `### Lesson title` | 0 |
| Generic blockquote | `> text` | 0 |
| Import lesson | `!import ./file.learn.md` | 0 |
| Embed quiz checkpoint | `!import ./file.quiz.md` | 0 |
| Inline math | `$formula$` | 0 |
| Block math | `$$formula$$` | 0 |
| Frontmatter | `---` YAML `---` | 1 |
| Note callout | `> [!note]` | 1 |
| Tip callout | `> [!tip]` | 1 |
| Warning callout | `> [!warning]` | 1 |
| Important callout | `> [!important]` | 1 |
| Caution callout | `> [!caution]` | 1 |
| Summary callout | `> [!summary]` | 1 |
| Example callout | `> [!example]` | 1 |
| Inline quiz question | ` ```quiz ` | 2 |
| Scored inline quiz | ` ```quiz scored:true ` | 2 |

---

## Validation

### Lenient mode (default)

| Condition | Level |
|-----------|-------|
| `lang` absent from frontmatter | Warning |
| Title absent (no H1 and no frontmatter `title`) | Warning |
| Unclosed fenced block | Error |
| Inline ` ```quiz ` block with no `?` line | Error |
| `!import` pointing to a `.quiz.md` with no valid questions | Warning |
| `!import` pointing to a missing file | Warning |

### Strict mode (`--strict`)

| Condition | Level |
|-----------|-------|
| `lang` absent | Error |
| Title absent | Error |
| All lenient-mode errors | Error |

Strict mode is recommended for CI pipelines and production publishing. Lenient mode is appropriate during authoring.

---

## Complete example — single-file path

`````markdown
---
title: Introduction to Python
lang: en
estimated_time: 2h
tags: [python, programming, beginner]
---

# Introduction to Python

A guided tour of Python basics, from variables to list manipulation.

## Module 1 — Variables

### Lesson 1 — Declaring a variable

A variable is a named reference to a value in memory.

```python
age = 25
name = "Alice"
```

> [!tip]
> Use descriptive names: `student_count` is clearer than `n`.

> [!warning]
> Variable names are case-sensitive: `Age` and `age` are two different variables.

```quiz
? Which syntax is valid Python?
- [x] age = 25
- [ ] int age = 25
- [ ] var age = 25
```

### Lesson 2 — Basic types

Python infers types automatically — no type declarations needed.

```python
age = 25        # int
pi = 3.14       # float
name = "Alice"  # str
active = True   # bool
```

```python
# Inspecting types
print(type(25))       # <class 'int'>
print(type(3.14))     # <class 'float'>
print(type("hello"))  # <class 'str'>
```

```quiz scored:true
? What is the type of `42` in Python?
- [x] int
- [ ] float
- [ ] str
```

> [!summary]
> - A variable associates a name with a value
> - Python infers types dynamically — no declaration required
> - Use `type()` to inspect the type of any object
> - Names are case-sensitive: `Age` ≠ `age`

## Module 2 — Conditions

!import ./03-conditions.learn.md

!import ./check-conditions.quiz.md
`````

---

## Relationship with QuizMD

LearnMD and QuizMD are complementary formats designed to work together:

| Dimension | LearnMD | QuizMD |
|-----------|---------|--------|
| Primary purpose | Teach | Assess |
| File extension | `.learn.md` | `.quiz.md` |
| Core unit | Section / Module | Question |
| Scoring | Delegated to ` ```quiz ` blocks | Native (points, passing_score) |
| Sequencing | Linear, modules and lessons | Sequential or all-at-once |
| Inline other format | ` ```quiz ` + `!import ./file.quiz.md` | `!import` for sub-quizzes |
| Standalone use | Yes | Yes |

---

## MCP integration

The neuroneo.md MCP server exposes LearnMD to compatible AI assistants.

### Exposed tools

| Tool | Input | Output |
|------|-------|--------|
| `parse_learn` | `content: string` | JSON structure (title, sections, blocks, directives) |
| `validate_learn` | `content: string`, `strict?: bool` | List of errors and warnings |
| `upload_learn` | `content, api_key, title` | Hosted lesson URL on neuroneo.md |

