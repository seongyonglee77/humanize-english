# humanize-english

An academic writing style skill for improving clarity, conciseness, and avoiding AI-generated writing patterns in English manuscripts.

> **Originally created with [opencode](https://github.com/opencode-ai/opencode)** — also installable on Claude Code and OpenAI Codex CLI.

---

## What This Skill Does

This skill helps you refine academic manuscripts by removing common AI-generated writing patterns while preserving your original meaning and voice. It focuses on:

- **Compound & hyphenated words** — e.g., `AI-generated materials` → `materials generated with AI`
- **Non-human possessives** — e.g., `GenAI's role` → `the role of GenAI`
- **Filler words** — e.g., `significant concerns` → `concerns`
- **Sentence structure** — e.g., `While reviewed studies documented..., few provided...` → `Reviewed studies documented..., but few provided...`
- **Formal/Latin vocabulary** — e.g., `via` → `using`
- **Punctuation** — em-dash overuse, citation placement

---

## Installation

### opencode (Original)

1. Clone this repository into your opencode skills directory:

```bash
git clone https://github.com/seongyonglee77/humanize-english.git ~/.opencode/skills/humanize-english
```

2. Restart opencode. The skill will be loaded automatically.

### Claude Code

1. Clone this repository:

```bash
git clone https://github.com/seongyonglee77/humanize-english.git
```

2. Copy the skill files into Claude Code's skill directory:

```bash
cp -r humanize-english ~/.claude/skills/humanize-english
```

3. Restart Claude Code. The skill will be available.

### OpenAI Codex CLI

1. Clone this repository:

```bash
git clone https://github.com/seongyonglee77/humanize-english.git
```

2. Copy the skill files into Codex's skill directory:

```bash
cp -r humanize-english ~/.codex/skills/humanize-english
```

3. Restart Codex CLI. The skill will be available.

---

## Usage

Trigger phrases:

- "apply my writing style"
- "edit in my style"
- "academic style check"
- "remove AI patterns"
- "style guide"
- "make it sound like me"
- "my academic voice"

---

## Architecture

### Direct Mode (Option A)

For short texts, the skill applies rules directly in priority order:

1. **High Priority** — Compound words, possessive 's, "with ~ing"
2. **Medium Priority** — Filler words, "While" clauses, vocabulary
3. **Low Priority** — Em-dashes, citation placement

### Agent Mode (Option B)

For complex documents or when quality verification is needed:

```
Document → [Detector] → Findings → [Rewriter] → Rewrite → [Validator] → Result
```

- **Detector** (`_agents/academic-style-detector.md`): Scans for violations and returns structured JSON
- **Rewriter** (`_agents/academic-style-rewriter.md`): Applies fixes in priority order with conflict resolution
- **Validator** (`_agents/academic-style-validator.md`): Compares original vs rewritten and returns quality assessment

---

## File Structure

```
.
├── SKILL.md                              # Main skill definition
├── LICENSE.txt                           # License
├── _agents/
│   ├── academic-style-detector.md        # Style violation detector
│   ├── academic-style-rewriter.md        # Automated rewriter
│   └── academic-style-validator.md       # Quality validator
├── references/
│   ├── style-taxonomy.md                 # Complete rule taxonomy
│   ├── positive-examples.md              # Author's natural style patterns
│   └── task.md                           # Development task list
└── assets/
    ├── style_guide_paper.md              # Original style guide
    ├── sample1.md                        # Published paper samples
    ├── sample2.md
    ├── sample3.md
    ├── AI_work1.md
    ├── AI_work2.md
    ├── editing_before-after.docx
    └── sample_track_change.docx
```

---

## Hard Invariants

These rules apply regardless of mode:

1. **Numbers preserved** — All numerical values, dates, percentages, statistics unchanged
2. **Citations preserved** — All (Author, Year) citations remain
3. **Direct quotes preserved** — Text inside quotation marks never modified
4. **No new facts** — Do not add claims, citations, or data not in original
5. **Academic register maintained** — Do not make text casual or informal
6. **Abbreviations respected** — Once-defined abbreviations remain abbreviated

---

## License

MIT License — see `LICENSE.txt` for details.

---

## Contributing

If you find new AI-generated patterns or have regression cases, please open an issue. Include the original text and expected rewrite so the taxonomy can be updated.

---

Built with [opencode](https://github.com/opencode-ai/opencode) and adapted for Claude Code / Codex CLI.
