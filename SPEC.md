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
| `spec_version` | No | string | LearnMD spec version this file targets (e.g. `"0.2"`) |

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
| `> [!objectives]` | Learning Objectives | What the learner will be able to do after this lesson — place at the top |

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

### Fenced callout blocks

Fenced callout blocks are **Level 2 alternatives to GFM callouts** (`> [!...]`). They support richer content (multi-paragraph, nested lists, syntax-highlighted code) and are rendered with a visual header by compatible players. Non-compatible renderers display the raw content as a plain code block.

**Supported block types:**

| Language | Icon | Label | Typical use |
|----------|------|-------|-------------|
| ` ```note ` | 📝 | Note | Supplementary information |
| ` ```tip ` | 💡 | Tip | Best practice or helpful advice |
| ` ```warning ` | ⚠️ | Warning | Common pitfall, frequent mistake |
| ` ```important ` | ❗ | Important | Critical point to remember |
| ` ```caution ` | 🔴 | Caution | Risk of error or data loss |
| ` ```summary ` | ✅ | Summary | Key takeaways at the end of a lesson |
| ` ```example ` | 🔍 | Example | Illustrative example |
| ` ```objectives ` | 🎯 | Learning Objectives | What the learner will achieve — place at top |

**Basic syntax:**

`````markdown
```summary
- Variables associate a **name** with a **value**
- Python infers types dynamically — no declaration needed
- Names are case-sensitive: `Age` ≠ `age`
```
`````

**Optional title attribute** (`title:"..."`):

`````markdown
```example title:"Token prediction"
Context: "The capital of France is"
Most likely token: " Paris"
Less likely token: " Lyon"
```
`````

**Optional code language** — place the language identifier before `title:` to render the body as a syntax-highlighted code block:

`````markdown
```example python title:"Assigning and reassigning a variable"
score = 0
print(score)   # → 0

score = 42
print(score)   # → 42
```
`````

When a code language is present, the block body is rendered as a syntax-highlighted code block inside the callout. When absent, the body is rendered as Markdown prose.

**Fenced vs GFM callouts:**

| Feature | GFM callout (`> [!...]`) | Fenced callout (` ```type `) |
|---------|--------------------------|-------------------------------|
| GitHub rendering | ✅ Native | ⚠️ Degrades to code block |
| Multi-paragraph content | Limited | ✅ Full Markdown |
| Syntax-highlighted code body | ❌ | ✅ via `lang` token |
| Optional title | Limited | ✅ via `title:"..."` |
| neuroneo.md rendering | ✅ | ✅ |

### Composition directives

#### `!import <path>`

Includes content from another file at the current position. The file type is detected from the extension:

- **`.learn.md`** — lesson content is inserted inline (frontmatter ignored)
- **`.quiz.md`** — renders as an interactive QuizMD checkpoint

```markdown
!import ./03-conditions.learn.md
!import ./check-variables.quiz.md
```

> [!important]
> **`!import` is the only supported directive for embedding quiz files.** There is no `!quiz` directive. Do not write `!quiz ./file.quiz.md` — it will not be recognized. Always use `!import ./file.quiz.md` regardless of whether the embedded file is a `.learn.md` or a `.quiz.md`.

Behavior:
- The imported file's content is inserted at the position of the directive.
- For `.learn.md`: the file's content is **rendered inline** at the insertion point — the section title appears as a visual divider and in the table of contents. Frontmatter (metadata) is ignored.
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

## ABC music notation

LearnMD supports **ABC notation** for embedding sheet music inline in lesson content. Compatible renderers (e.g. neuroneo.md) use [abcjs](https://www.abcjs.net/) to produce SVG output directly in the browser — no server required.

### Syntax

Use a fenced code block with the language identifier `abc`, optionally followed by space-separated flags:

```
```abc [play] [cursor] [colors]
```

| Flag | Description |
|------|-------------|
| *(none)* | Static SVG score only |
| `play` | Adds audio controls (play, restart, progress bar) |
| `cursor` | Highlights the current note during playback (requires `play`) |
| `colors` | Colors each note by pitch class on the chromatic wheel (requires `play`) |

Flags are combinable in any order: ` ```abc play cursor colors `

````markdown
```abc
X:1
T:Scale of C major — static
M:4/4
L:1/4
K:C
CDEF|GABC|
```

```abc play
X:1
T:Scale of C major — with audio
M:4/4
L:1/4
K:C
CDEF|GABC|
```

```abc play cursor colors
X:1
T:Scale of C major — fully interactive
M:4/4
L:1/4
K:C
CDEF|GABC|
```
````

### Rendering

- Compatible renderers replace the `abc` block with an inline SVG score.
- Non-compatible renderers (e.g. GitHub) display the raw ABC text as a code block — the format degrades gracefully.
- The `play` flag uses the Web Audio API via [abcjs synth](https://www.abcjs.net/api-synth.html); it is loaded lazily only when ABC blocks are present in the document.
- ABC notation may appear anywhere in lesson prose: body text, callouts, and inside ` ```note ` or ` ```concept ` blocks.

### Example

````markdown
### Lesson 1 — The major scale

The C major scale uses only natural notes:

```abc play cursor
X:1
T:C major scale
M:4/4
L:1/4
K:C
CDEF|GABC|
```

Notice that the intervals follow the pattern: W W H W W W H.
````

---

## Mermaid diagrams

LearnMD supports **Mermaid** text-based diagrams via [Mermaid.js](https://mermaid.js.org/). Diagrams can appear anywhere in a lesson document.

> **Note:** Static image embeds (`![](url)`) are **not supported** for security reasons. Mermaid diagrams are defined as text and rendered client-side.

### Syntax

Use a fenced code block with the `mermaid` language identifier:

````markdown
```mermaid
flowchart LR
    A[Start] --> B{Decision}
    B -- Yes --> C[Action]
    B -- No --> D[End]
```
````

Optional block attributes can follow the language identifier:

````markdown
```mermaid caption:"System architecture" width:80%
graph TD
    Client --> Server
    Server --> DB
```
````

| Attribute | Type | Description |
|-----------|------|-------------|
| `caption` | string | Caption displayed below the diagram |
| `width` | CSS value | Diagram width (e.g. `100%`, `600px`) |

### Supported diagram types

| Type | Keyword | Example use |
|------|---------|-------------|
| Flowchart | `flowchart` / `graph` | Process flows, decision trees |
| Sequence | `sequenceDiagram` | API call flows, interactions |
| Class | `classDiagram` | OOP relationships, data models |
| Entity-Relationship | `erDiagram` | Database schemas |
| Gantt | `gantt` | Project timelines |
| Mindmap | `mindmap` | Topic hierarchies |
| Timeline | `timeline` | Historical sequences |

### AI authoring

Mermaid diagram syntax is text-based and AI-generatable. When using MCP tools or Claude, you can ask for diagrams inline:

> "Add a sequence diagram showing how a JWT token is validated."

Claude will emit a valid `` ```mermaid `` block directly in the `.learn.md` file.

When generating Mermaid diagrams:

- Prefer LR orientation for sequential flows with more than 5 steps
- Keep node labels under 40 characters; use `\n` to break longer labels
- Avoid linear chains longer than 6 nodes; introduce subgraphs or branches
- Limit vertical depth to 7 levels maximum in TD orientation
- Use TD only when the diagram has meaningful hierarchy (not just a sequence)

### Constraints

- Mermaid is **auto-detected**: the Mermaid runtime is loaded only when a `` ```mermaid `` block is present.
- Not all Mermaid diagram types are guaranteed in all player environments — `flowchart`, `sequence`, and `classDiagram` have the widest support.
- Full language reference: [mermaid.js.org](https://mermaid.js.org/)

---

---

## TikZ diagrams

LearnMD supports **TikZ** (LaTeX-based vector graphics) via `` ```tikz `` fenced blocks. Diagrams are rendered **server-side** through the Kroki proxy and returned as static SVG — no JavaScript required in the browser.

TikZ is ideal for mathematical diagrams, scientific illustrations, and circuit schematics. Add the `circuitikz` flag for electronic circuits.

### Syntax — Standard TikZ

````markdown
```tikz caption:"Ohm's Law triangle" width:45%
\begin{tikzpicture}[scale=1.4]
  \draw[thick] (0,0) -- (3,0) -- (1.5,2.6) -- cycle;
  \node[font=\Large\bfseries] at (1.5,1.7) {$V$};
  \draw[thick] (0.75,0) -- (0.75,1.3);
  \node[font=\Large\bfseries] at (0.38,0.6) {$I$};
  \node[font=\Large\bfseries] at (2.1,0.6) {$R$};
\end{tikzpicture}
```
````

### Syntax — CircuitTikZ

Add the `circuitikz` flag to enable circuit drawing components:

````markdown
```tikz circuitikz caption:"Battery + resistor circuit"
\begin{circuitikz}[american, scale=1.2]
  \draw (0,0) to[battery1, l=$V_s$] (0,3)
              to[short] (3,3)
              to[R, l=$R$] (3,0)
              to[short] (0,0);
\end{circuitikz}
```
````

### Attributes

| Attribute | Description | Example |
|---|---|---|
| `circuitikz` | Enable CircuitTikZ mode for circuit diagrams | `tikz circuitikz` |
| `caption:"text"` | Caption displayed below the diagram | `caption:"Force diagram"` |
| `width:value` | CSS width of the rendered SVG | `width:50%` |

### When to use TikZ

| Domain | Examples |
|---|---|
| Mathematics | Geometric proofs, function plots, coordinate systems |
| Physics | Free-body diagrams, wave patterns, optics |
| Electronics | Circuit schematics (use `circuitikz`) |
| Chemistry | Molecular structures, reaction diagrams |
| Engineering | Mechanical components, flow diagrams |

### Guidelines for authors

- Always declare `\begin{tikzpicture}` … `\end{tikzpicture}` (or `\begin{circuitikz}` … `\end{circuitikz}`).
- Do not use `\usepackage{}` — packages are pre-loaded server-side.
- Use `scale=` on the environment for size control rather than `width:` attribute alone.
- TikZ is verbose — use Mermaid for simple flowcharts and sequence diagrams.
- Full reference: [tikz.dev](https://tikz.dev/) · CircuitTikZ: [circuitikz.github.io](https://circuitikz.github.io/circuitikz/)

---


## Vega-Lite charts

LearnMD supports **Vega-Lite** declarative JSON charts via `` ```vega-lite `` fenced blocks. Charts render client-side using [vega-embed](https://github.com/vega/vega-embed).

Vega-Lite is the recommended renderer for statistical data visualization: bar charts, line charts, scatter plots, histograms, heatmaps, and faceted small multiples.

### Syntax

````markdown
```vega-lite
{
  "$schema": "https://vega.github.io/vega-lite/v6.json",
  "width": "container",
  "mark": {"type": "bar"},
  "data": {
    "values": [
      {"category": "A", "value": 30},
      {"category": "B", "value": 80},
      {"category": "C", "value": 55}
    ]
  },
  "encoding": {
    "x": {"field": "category", "type": "ordinal"},
    "y": {"field": "value", "type": "quantitative"}
  }
}
```
````

With attributes:

````markdown
```vega-lite caption:"Temperature anomalies 1880–2020" height:350 theme:vox
{...JSON spec...}
```
````

### Attributes

| Attribute | Description | Example |
|---|---|---|
| `caption:"text"` | Caption below the chart | `caption:"Sales by year"` |
| `height:value` | Chart height in pixels (integer) | `height:400` |
| `theme:name` | Vega theme | `theme:dark` |

### Supported themes

`excel`, `dark`, `fivethirtyeight`, `ggplot2`, `googlecharts`, `latimes`, `powerbi`, `quartz`, `urbaninstitute`, `vox`

### Guidelines for authors

- Use `"width": "container"` in the JSON spec for responsive charts.
- The block content must be valid JSON — no trailing commas, no comments.
- Use Vega-Lite for: bar charts, line charts, scatter plots, histograms, heatmaps, faceted small multiples.
- Use D3 when you need custom interaction, animated transitions, or a layout Vega-Lite cannot express.
- Full reference: [vega.github.io/vega-lite](https://vega.github.io/vega-lite/)

---

## Chess diagrams

LearnMD supports **chess board diagrams** via `` ```chess `` fenced blocks. Positions are specified in FEN notation; games can be replayed from PGN.

### Syntax — FEN (position)

````markdown
```chess
rnbqkbnr/pppppppp/8/8/8/8/PPPPPPPP/RNBQKBNR w KQkq - 0 1
```
````

With attributes:

````markdown
```chess orientation:black size:480
r1bqkb1r/pppp1ppp/2n2n2/4p3/2B1P3/5N2/PPPP1PPP/RNBQK2R w KQkq - 4 4
```
````

### Syntax — PGN (game replay)

````markdown
```chess pgn
[Event "Example"]
[White "Kasparov"]
[Black "Deep Blue"]

1. e4 e5 2. Nf3 Nc6 3. Bc4 Bc5 4. b4 Bxb4 5. c3 Ba5
```
````

### Attributes

| Attribute | Description | Default | Example |
|---|---|---|---|
| `orientation:white\|black` | Board orientation | `white` | `orientation:black` |
| `size:value` | Board width in pixels | `360` | `size:480` |
| `pgn` | Force PGN parsing mode | auto-detect | `chess pgn` |

### Guidelines for authors

- Use FEN for static position diagrams — opening positions, tactical puzzles, endgame studies.
- Use PGN with the `pgn` flag for interactive game replays with move navigation.
- `orientation:black` is useful when explaining positions from Black's perspective.
- FEN reference: [chessprogramming.org/FEN](https://www.chessprogramming.org/Forsyth-Edwards_Notation)


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
| Objectives callout | `> [!objectives]` | 1 |
| Inline quiz question | ` ```quiz ` | 2 |
| Scored inline quiz | ` ```quiz scored:true ` | 2 |
| Note callout (fenced) | ` ```note ` | 2 |
| Tip callout (fenced) | ` ```tip ` | 2 |
| Warning callout (fenced) | ` ```warning ` | 2 |
| Summary callout (fenced) | ` ```summary ` | 2 |
| Example callout (fenced) | ` ```example [lang] [title:"..."] ` | 2 |
| Objectives callout (fenced) | ` ```objectives ` | 2 |
| Concept block | ` ```concept [id:slug] ` | 2 |
| ABC (static) | ` ```abc ` ABC text ` ``` ` | 0 |
| ABC (interactive) | ` ```abc play cursor colors ` ABC text ` ``` ` | 0 |
| Mermaid diagram | ` ```mermaid ` diagram text ` ``` ` | 0 |
| Mermaid (with caption) | ` ```mermaid caption:"..." width:80% ` | 0 |
| TikZ diagram | ` ```tikz ` LaTeX code ` ``` ` | 0 |
| TikZ (circuitikz) | ` ```tikz circuitikz caption:"..." ` | 0 |
| Vega-Lite chart | ` ```vega-lite ` JSON spec ` ``` ` | 0 |
| Vega-Lite (with attrs) | ` ```vega-lite caption:"..." height:400 theme:vox ` | 0 |
| Chess position (FEN) | ` ```chess ` FEN string ` ``` ` | 0 |
| Chess game (PGN) | ` ```chess pgn ` PGN text ` ``` ` | 0 |

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

