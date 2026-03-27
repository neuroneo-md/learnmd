# Changelog

All notable changes to the LearnMD specification are documented here.

Format follows [Keep a Changelog](https://keepachangelog.com/en/1.0.0/).
Versioning follows [Semantic Versioning](https://semver.org/).

---

## [0.2.2] — 2026-03-27

### Changed
- ` ```example ` and ` ```summary ` special blocks removed — replaced by GFM callouts `> [!example]` and `> [!summary]`
- ` ```example ` with code content should use a language-fenced block (` ```python `, ` ```js `, etc.) instead
- Added `> [!caution]`, `> [!summary]`, `> [!example]` to the callouts reference table
- Note: `[!summary]` and `[!example]` degrade gracefully on GitHub (plain blockquote); render with full styling on Obsidian and neuroneo.md

---

## [0.2.1] — 2026-03-27

### Changed
- `!quiz` directive removed — `!import` now auto-detects file type from extension:
  `.learn.md` → lesson content, `.quiz.md` → interactive quiz checkpoint

---

## [0.2.0] — 2026-03-26

### Added
- Inline ` ```quiz ` block — one question per block, all QuizMD types, `scored: false` by default
- `scored: true | false` flag on ` ```quiz ` blocks
- ` ```example ` block with optional `title` attribute
- ` ```summary ` block for key takeaways
- Math auto-detection — `$...$` and `$$...$$` trigger KaTeX rendering automatically

### Changed
- `type:` frontmatter field removed — document type inferred from file extension (`.learn.md`)
- `level:` frontmatter field removed
- `domain:` frontmatter field removed
- `prereqs:` frontmatter field removed
- `math:` frontmatter field removed (auto-detected)
- Title inferred from first `# H1` heading; `title:` frontmatter optional override

### Removed
- `exercise` block type (replaced by inline `quiz` block)
- `concept` block type (deferred to future version)
- `type:`, `level:`, `domain:`, `prereqs:`, `math:` frontmatter fields

---

## [0.1.0] — 2025-09-01

### Added
- Initial specification draft
- Three progressive levels (0, 1, 2)
- YAML frontmatter: `title`, `lang`, `type`, `level`, `domain`, `tags`, `estimated_time`, `prereqs`, `author`, `math`
- GFM callouts: `[!note]`, `[!tip]`, `[!warning]`, `[!important]`
- `exercise` and `concept` special blocks
- `!import` and `!quiz` directives
- `validate_learn` strict and lenient modes
