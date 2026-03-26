# Changelog

All notable changes to the LearnMD specification are documented here.

Format follows [Keep a Changelog](https://keepachangelog.com/en/1.0.0/).
Versioning follows [Semantic Versioning](https://semver.org/).

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
