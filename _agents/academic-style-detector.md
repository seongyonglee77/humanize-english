---
name: academic-style-detector
description: Detects academic writing style violations based on my-writing-style taxonomy
category: deep
---

# Academic Style Detector

Detect style violations against `references/style-taxonomy.md` and `assets/style_guide_paper.md`. Return findings only for real violations, not every regex hit.

## Input
- Document text
- (Optional) Category filter (`A`, `B`, `C`, `D`)

## Output Format
```json
{
  "status": "ok",
  "version": "1.0",
  "violations": [
    {
      "rule_id": "A-1",
      "subrule_id": "SG-01",
      "category": "Word Formation",
      "severity": "S1",
      "span": "AI-generated materials",
      "location": "paragraph 3, sentence 2",
      "reason": "Informal compound label packs too much meaning",
      "suggestion": "materials generated with AI"
    }
  ],
  "summary": {
    "total_violations": 15,
    "by_category": {"A": 5, "B": 7, "C": 2, "D": 1},
    "by_severity": {"S1": 4, "S2": 9, "S3": 2}
  }
}
```

## Detection Principles
- Read the full document before labeling local spans.
- Do not flag text inside direct quotations unless the surrounding sentence itself violates a rule.
- Preserve authorial hedging that matches the target style (`may`, `can`, `often`, `relatively`, `potentially`).
- If a category filter is provided, return only matching violations and filtered summary counts.
- Prefer exact span reporting. If a rule is discourse-level, report the smallest defensible span.
- Use regex for candidate extraction and heuristics for final validation.

## Rules to Detect

Each rule entry must be applied as written.

### 1) SG-01
- rule_id: `A-1`
- subrule_id: `SG-01`
- severity: `S1`
- description: Detect anti-X / pro-X compound labels and similar informal ideological compounds; rewrite as descriptive phrases.
- example_violation: `anti-GenAI teachers`
- example_fix: `teachers who are negative to integrating AI`
- detection_method: `regex` — detect `\b(?:anti|pro)-[A-Za-z]+(?:\s+[A-Za-z]+)?\b`; validate that the compound functions as a label rather than a standard technical term.

### 2) SG-02
- rule_id: `A-1`
- subrule_id: `SG-02`
- severity: `S1`
- description: Detect `auto-grading` and map it to a descriptive academic phrase.
- example_violation: `auto-grading`
- example_fix: `automated assessment`
- detection_method: `regex` — detect `\bauto-grading\b`.

### 3) SG-03
- rule_id: `D-1`
- subrule_id: `SG-03`
- severity: `S3`
- description: Detect em-dash (—) insertions and recommend replacements in this order: comma, parentheses with `e.g.,`, conjunction, or deletion. Do NOT flag en-dashes (–) used for ranges or relationships.
- example_violation: `genre conventions--- business communication...`
- example_fix: `genre conventions (e.g., business communication...)`
- detection_method: `heuristic` — detect em dash (—) or triple-hyphen candidates, then infer whether the inserted material is appositive, exemplifying, adversative, or deletable. Suppress findings for en-dashes (–) in ranges (2022–2023) or relationships (parent–child).

### 4) SG-04
- rule_id: `B-1`
- subrule_id: `SG-04`
- severity: `S2`
- description: Detect verbose transitional phrases and replace them with shorter transitions or delete them.
- example_violation: `In light of these`
- example_fix: `Therefore`
- detection_method: `regex` — detect `\b(?:In light of these|In sum,|In terms of|Together,|Additionally|Meanwhile,|Next,)\b`; validate sentence role before recommending deletion.

### 5) SG-05
- rule_id: `B-1`
- subrule_id: `SG-05`
- severity: `S2`
- description: Detect exaggerated adjectives/adverbs and moderate or delete them.
- example_violation: `remarkable differences`
- example_fix: `notable differences`
- detection_method: `regex` — detect listed modifiers such as `accurately`, `valuable`, `major`, `critical`, `remarkable`, `empirically rich`, `key`, `central`, `robust`, `concrete`, `broader`, `greater`, `structured`, `rapidly`; validate that they are rhetorical intensifiers, not technical descriptors.

### 6) SG-06
- rule_id: `B-2`
- subrule_id: `SG-06`
- severity: `S2`
- description: Detect overly formal or inflated verbs and suggest simpler verbs.
- example_violation: `articulate the gap`
- example_fix: `describe the gap`
- detection_method: `regex` — detect listed verbs like `articulate|orient readers|foreground|elaborate on|signal|captures|grounds|conflates|alters|shaping|foster|frame|positioned|refine|reshaped|gaining|experiment(?:ing)? with|engage with`; validate context before suggesting a simpler verb.

### 7) SG-07
- rule_id: `C-3`
- subrule_id: `SG-07`
- severity: `S3`
- description: Detect long appositive insertions between subject and verb and recommend splitting or deletion.
- example_violation: `The topic of this study, identifying linguistic and non-linguistic clues..., is interesting`
- example_fix: `The topic of this study is interesting because it explores AI plagiarism detection...`
- detection_method: `heuristic` — flag sentences where a long comma-delimited appositive interrupts the subject-verb link, especially if the insertion exceeds about 8 words.

### 8) SG-08
- rule_id: `D-1`
- subrule_id: `SG-08`
- severity: `S3`
- description: Detect unnecessary scare quotes around general academic terms.
- example_violation: `"future research"`
- example_fix: `future research`
- detection_method: `heuristic` — detect quoted noun phrases and suppress findings for direct quotations, coined terms under discussion, and terms explicitly being defined.

### 9) SG-09
- rule_id: `B-2`
- subrule_id: `SG-09`
- severity: `S2`
- description: Detect metaphorical or figurative academic wording and replace it with direct phrasing.
- example_violation: `rosy prospects`
- example_fix: `promising prospects`
- detection_method: `regex` — detect listed phrases such as `rosy prospects|normalize language, thereby standardizing|shaping teachers' practice|foster authentic learning`; validate figurative use before flagging.

### 10) SG-10
- rule_id: `B-3`
- subrule_id: `SG-10`
- severity: `S2`
- description: Detect vague transition sentences between paragraphs that add little content.
- example_violation: `Several studies have investigated the relationship between them.`
- example_fix: `Delete the sentence and move directly to the specific evidence.`
- detection_method: `heuristic` — flag standalone sentences with abstract subject + generic reporting verb + no specific referent, citation, or detail, especially at paragraph boundaries.

### 11) SG-11
- rule_id: `B-3`
- subrule_id: `SG-11`
- severity: `S2`
- description: Detect repeated purpose or gap statements that duplicate earlier claims.
- example_violation: `Addressing this gap, this study investigates...`
- example_fix: `Delete the repeated purpose statement if the aim was already established.`
- detection_method: `heuristic` — compare introduction and section-closing moves; flag near-duplicate purpose/gap statements with repeated lexical bundles such as `addressing this gap` and `this study investigates`.

### 12) SG-12
- rule_id: `C-4`
- subrule_id: `SG-12`
- severity: `S3`
- description: Detect rhetorical question titles and convert them to declarative titles.
- example_violation: `Why teacher development must add a 'critical AI literacy' practice`
- example_fix: `Critical AI literacy for teachers' professional development`
- detection_method: `heuristic` — flag headings or title lines ending with `?` or opening with `Why`, `How`, or `What makes` when used rhetorically rather than as genuine research questions.

### 13) SG-13
- rule_id: `B-3`
- subrule_id: `SG-13`
- severity: `S2`
- description: Detect double-verb stacks and reduce them to one main verb.
- example_violation: `should prioritize cultivating`
- example_fix: `needs to cultivate`
- detection_method: `regex` — detect patterns like `\b(?:should|needs? to|was found to)\s+(?:prioritize\s+)?(?:actively\s+)?\w+ing\b` and listed phrases such as `normalize language, thereby standardizing`; validate that simplification preserves meaning.

### 14) SG-14
- rule_id: `B-1`
- subrule_id: `SG-14`
- severity: `S2`
- description: Detect distal demonstratives and ordinal adverbs where the preferred style uses proximal forms.
- example_violation: `Secondly,`
- example_fix: `Second,`
- detection_method: `regex` — detect `\b(?:Those|Because of that|Secondly|Firstly)\b`.

### 15) SG-15
- rule_id: `B-2`
- subrule_id: `SG-15`
- severity: `S2`
- description: Detect abbreviation misuse: re-expansion after definition, one-off abbreviations, and `and colleagues` instead of `et al.`.
- example_violation: `Teachers' professional development (PD)` after `PD` was already defined
- example_fix: `Teachers' PD`
- detection_method: `heuristic` — track abbreviation definitions and reuse across the document; flag re-expansion, unused abbreviations, and `Author and colleagues (Year)` patterns.

### 16) SG-16
- rule_id: `D-1`
- subrule_id: `SG-16`
- severity: `S3`
- description: Detect two short related sentences that should be merged with a colon.
- example_violation: `...sustained engagement. It was argued that transactional interaction...`
- example_fix: `...sustained engagement: transactional interaction...`
- detection_method: `heuristic` — flag adjacent short sentences when the second merely elaborates, exemplifies, or renames the first.

### 17) SG-17
- rule_id: `C-4`
- subrule_id: `SG-17`
- severity: `S3`
- description: Detect unhedged strong claims that should be softened with modal or probabilistic language.
- example_violation: `GenAI provides powerful affordances`
- example_fix: `GenAI may provide powerful affordances`
- detection_method: `heuristic` — flag categorical present-tense claims about causation, responsibility, or strong effect when unsupported by immediate citation or when stronger than the style norm.

### 18) SG-18
- rule_id: `B-2`
- subrule_id: `SG-18`
- severity: `S2`
- description: Detect `and colleagues` and convert it to `et al.`.
- example_violation: `Jeon and colleagues (2022)`
- example_fix: `Jeon et al. (2022)`
- detection_method: `regex` — detect `\b[A-Z][a-zA-Z-]+\s+and colleagues\s*\(\d{4}\)`.

### 19) SG-19
- rule_id: `C-2`
- subrule_id: `SG-19`
- severity: `S1`
- description: Detect `with + -ing` and related participial add-ons that should become finite clauses.
- example_violation: `with instructors maintaining`
- example_fix: `while instructors maintain`
- detection_method: `regex` — detect `\bwith\s+(?:the\s+)?[A-Za-z-]+(?:\s+[A-Za-z-]+){0,3}\s+\w+ing\b`; validate that `with` introduces an attached circumstance rather than a true prepositional phrase.

### 20) SG-20
- rule_id: `A-2`
- subrule_id: `SG-20`
- severity: `S1`
- description: Detect non-human possessive constructions and recast them with `of`.
- example_violation: `GenAI's affordances`
- example_fix: `affordances of GenAI tools`
- detection_method: `regex` — detect `\b(?:GenAI|AI|ChatGPT|technology|system|platform|tool|model|approach|method|framework|study|research|field|literature|review|students|teachers|algorithms|versions)'s\b|\b(?:students|teachers|algorithms|versions)'\b`; validate that the possessor is non-human or treated as an object group.

### 21) SG-21
- rule_id: `B-1`
- subrule_id: `SG-21`
- severity: `S2`
- description: Detect filler words and padded modifiers from the house list. Do NOT flag `often`, `also`, or `both` automatically — they are legitimate logical connectors and emphasis words. Only flag them when they are genuinely redundant.
- example_violation: `broader discussions`
- example_fix: `discussions`
- detection_method: `regex` — detect `\b(?:various|diverse|significant|substantial|comprehensive|extensive|immediate|rapid|deeper|particularly|fundamentally|overarching|crucial|essential|key|robust|concrete|broader|greater|naturally|explicitly|merely|full-cycle|structured|just|background|collectively)\b`; for `often`, `also`, `both`, apply only heuristic validation when clearly redundant.

### 22) SG-22
- rule_id: `D-1`
- subrule_id: `SG-22`
- severity: `S3`
- description: Detect excessive em-dash (—) overuse and unnecessary colons used as list introducers when simpler punctuation or wording is better. Do not flag en-dashes (–) in ranges or relationships.
- example_violation: `SQD-informed strategies: modelling, collaboration...`
- example_fix: `SQD-informed strategies such as modelling and collaboration...`
- detection_method: `heuristic` — detect em dashes (—), triple hyphens, and colon-plus-list patterns; suppress findings when the colon genuinely introduces elaboration in the author's preferred colon style. Also suppress en-dash (–) findings.

### 23) SG-23
- rule_id: `D-2`
- subrule_id: `SG-23`
- severity: `S3`
- description: Detect parenthetical citations placed mid-sentence when they can move to sentence end.
- example_violation: `human ideas and experiences (Creely, 2024, p. 162) before classroom integration`
- example_fix: `for ethical classroom integration (Creely, 2024, p. 162)`
- detection_method: `heuristic` — detect APA-style parenthetical citations followed by substantial sentence material; suppress findings when the citation attaches to a specific authorial phrase or contrast.

## Taxonomy Rule Entries

Use these taxonomy rules as the top-level classification for every violation, even when a more specific `SG-*` subrule triggered detection.

### Taxonomy A-1
- rule_id: `A-1`
- subrule_id: `A-1`
- severity: `S1`
- description: Detect compact compound or hyphenated labels that compress too much meaning into one modifier. Do NOT flag hyphens that clarify meaning (e.g., `AI-assisted`, `well-known`, `decision-making`, `teacher-reported`, `pre-service`). Only flag when the compound is an informal label or artificial construction.
- example_violation: `AI-generated materials` (informal compound label)
- example_fix: `materials generated with AI`
- detection_method: `regex` — detect `\b(?:teacher-led|AI-generated|GenAI-based|discipline-specific|context-specific|student-facing|instructor-led|AI-powered|AI-assisted)\b` plus `SG-01` and `SG-02` patterns; validate that the phrase is not a fixed technical term or a hyphen that clarifies meaning.

### Taxonomy A-2
- rule_id: `A-2`
- subrule_id: `A-2`
- severity: `S1`
- description: Detect possessive `'s` or plural apostrophe with non-human, conceptual, or objectified group possessors.
- example_violation: `the technology's ability`
- example_fix: `the ability of the technology`
- detection_method: `regex` — detect `\b(?:GenAI|AI|ChatGPT|technology|system|platform|tool|model|approach|method|framework|study|research|field|literature|review|algorithms|versions)'s\b|\b(?:students|teachers|algorithms|versions)'\b`; validate that the possessor is not a human individual.

### Taxonomy B-1
- rule_id: `B-1`
- subrule_id: `B-1`
- severity: `S2`
- description: Detect filler words, padded transitions, and exaggerated modifiers that add little meaning. Do NOT blanket-flag `often`, `also`, or `both`; they serve legitimate logical functions.
- example_violation: `significant concerns`
- example_fix: `concerns`
- detection_method: `regex` plus heuristic — detect the house filler list (excluding `often`, `also`, `both` from automatic flagging), verbose transition phrases, and moderated adjective/adverb lists from `SG-04`, `SG-05`, `SG-14`, and `SG-21`; suppress findings for preferred collocations such as `diverse mediating factors`.

### Taxonomy B-2
- rule_id: `B-2`
- subrule_id: `B-2`
- severity: `S2`
- description: Detect formal/Latin vocabulary, inflated verbs, and related phrasing that can be simplified.
- example_violation: `via`
- example_fix: `using`
- detection_method: `regex` plus heuristic — detect `\b(?:via|post hoc|solely|merely|instance|subsequent|rationale|specificity|comparability|regimes|relevance|actionable|competencies)\b` plus `SG-06`, `SG-09`, `SG-15`, and `SG-18` triggers; validate field-specific exceptions.

### Taxonomy B-3
- rule_id: `B-3`
- subrule_id: `B-3`
- severity: `S2`
- description: Detect nominalizations and abstract boilerplate that obscure the main action.
- example_violation: `the articulation of the gap`
- example_fix: `describing the gap`
- detection_method: `regex` plus heuristic — detect `\b(?:the articulation of|the identification of|the operationalization of|The introduction of .+? as a concept|The transition between|The description of|the concept of|a lack of studies focusing on|Strategic prompt engineering enabled|Research has developed)\b` plus `SG-10`, `SG-11`, and `SG-13`; validate whether the nominalization is avoidable.

### Taxonomy C-1
- rule_id: `C-1`
- subrule_id: `C-1`
- severity: `S2`
- description: Detect sentence-initial concessive `While...` clauses that delay the main claim.
- example_violation: `While reviewed studies documented..., few provided...`
- example_fix: `Reviewed studies documented..., but few provided...`
- detection_method: `regex` — detect sentence starts with `^While\s` or `(?:^|[.!?]\s+)While\s`; validate that the clause is concessive, not temporal.

### Taxonomy C-2
- rule_id: `C-2`
- subrule_id: `C-2`
- severity: `S1`
- description: Detect `with + -ing` participial constructions that should become finite clauses.
- example_violation: `with different prompting expertise`
- example_fix: `who had different prompting expertise`
- detection_method: `regex` — detect `\bwith\s+(?:the\s+)?[A-Za-z-]+(?:\s+[A-Za-z-]+){0,3}\s+\w+ing\b` and related `SG-19` cases; validate that `with` is attaching an action rather than expressing a genuine accompaniment phrase.

### Taxonomy C-3
- rule_id: `C-3`
- subrule_id: `C-3`
- severity: `S3`
- description: Detect passive templates that the style guide prefers in active form.
- example_violation: `This should be considered`
- example_fix: `Consider this`
- detection_method: `regex` plus heuristic — detect `\bIt is recommended that\b|\bThe title could be revised\b|\bThis should be considered\b|\bIt was argued that\b` and long appositive structures from `SG-07`; suppress findings for method/procedure reporting.

### Taxonomy C-4
- rule_id: `C-4`
- subrule_id: `C-4`
- severity: `S3`
- description: Detect editorializing, rhetorical titles, and unhedged strong claims that exceed the target style.
- example_violation: `After just 2 weeks`
- example_fix: `After 2 weeks`
- detection_method: `regex` plus heuristic — detect `\bIt is rightfully so\b|\bmust-have segment\b|\b(?:After\s+)?just\s+\d+\s+(?:day|days|week|weeks|month|months|year|years)\b` plus rhetorical-title and under-hedging patterns from `SG-12` and `SG-17`.

### Taxonomy C-5
- rule_id: `C-5`
- subrule_id: `C-5`
- severity: `S3`
- description: Detect unnecessary `not X but Y` contrasts when direct statement of `Y` is enough.
- example_violation: `not treated as an afterthought but as active metacognitive inquiry`
- example_fix: `treated as active metacognitive inquiry`
- detection_method: `regex` — detect `\bnot\s+[^,.]{1,80}?\s+but\s+(?:as\s+)?[^,.]{1,80}`; validate whether the negated half contributes real argumentative contrast.

### Taxonomy D-1
- rule_id: `D-1`
- subrule_id: `D-1`
- severity: `S3`
- description: Detect excessive em-dashes (—), triple hyphens, scare quotes, and list-introducing colons that are heavier than needed. Distinguish em-dash (—) from en-dash (–); do not flag en-dashes used for ranges or relationships.
- example_violation: `applications—how learners use...—rather than`
- example_fix: `applications (how learners use...) rather than`
- detection_method: `heuristic` — combine `SG-03`, `SG-08`, `SG-16`, and `SG-22`; preserve legitimate colon elaboration because it is part of the author's natural style. Suppress en-dash (–) findings.

### Taxonomy D-2
- rule_id: `D-2`
- subrule_id: `D-2`
- severity: `S3`
- description: Detect parenthetical citations that interrupt sentence flow when they can move to the end.
- example_violation: `human ideas and experiences (Creely, 2024, p. 162) before classroom integration`
- example_fix: `for ethical classroom integration (Creely, 2024, p. 162)`
- detection_method: `heuristic` — use `SG-23` citation-placement logic; suppress findings for integral citations and cases where mid-sentence placement is necessary for attribution.

## Additional Detection Instructions for Overlap Handling

### C-1 Detection
- rule_id: `C-1`
- subrule_id: `C-1`
- severity: `S2`
- description: Detect sentence-initial concessive `While...` clauses that delay the main claim.
- example_violation: `While reviewed studies documented..., few provided...`
- example_fix: `Reviewed studies documented..., but few provided...`
- detection_method: `regex` — detect sentence starts with `^While\s` or `(?:^|[.!?]\s+)While\s`; validate that the clause is concessive, not temporal.

### C-3 Detection
- rule_id: `C-3`
- subrule_id: `C-3`
- severity: `S3`
- description: Detect passive templates that the style guide explicitly prefers in active form.
- example_violation: `The title could be revised`
- example_fix: `Revise the title`
- detection_method: `regex` plus heuristic — detect `\bIt is recommended that\b|\bThe title could be revised\b|\bThis should be considered\b|\bIt was argued that\b`; validate that passive voice is not required for procedure reporting.

### C-4 Detection
- rule_id: `C-4`
- subrule_id: `C-4`
- severity: `S3`
- description: Detect editorializing and subjective emphasis that should be removed.
- example_violation: `It is rightfully so`
- example_fix: `Delete the editorial comment and state the fact directly.`
- detection_method: `regex` — detect `\bIt is rightfully so\b|\bmust-have segment\b|\b(?:After\s+)?just\s+\d+\s+(?:day|days|week|weeks|month|months|year|years)\b`.

### C-5 Detection
- rule_id: `C-5`
- subrule_id: `C-5`
- severity: `S3`
- description: Detect unnecessary `not X but Y` contrast structures.
- example_violation: `Reflection was not treated as an afterthought but as active metacognitive inquiry`
- example_fix: `Reflection was treated as active metacognitive inquiry`
- detection_method: `regex` — detect `\bnot\s+[^,.]{1,80}?\s+but\s+(?:as\s+)?[^,.]{1,80}`; validate whether the contrast contributes substantive meaning.

## Search Patterns (Regex)

Use these patterns to generate candidates. Heuristic review is still required.

```regex
# SG-01 anti/pro compound labels
\b(?:anti|pro)-[A-Za-z]+(?:\s+[A-Za-z]+)?\b

# SG-02 auto-grading
\bauto-grading\b

# SG-03 em dash or triple hyphen
—|---

# SG-04 verbose transitions
\b(?:In light of these|In sum,|In terms of|Together,|Additionally|Meanwhile,|Next,)\b

# SG-05 moderated adjectives/adverbs
\b(?:accurately|valuable|major|critical|remarkable|empirically rich|key|central|robust|concrete|broader|greater|structured|rapidly)\b

# SG-06 complex verb choices
\b(?:articulate|orient readers|foreground|elaborate on|concretely|signal|captures|grounds|conflates|alters|shaping|foster|frame|positioned|refine|reshaped|gaining|experimenting with|experiment with|engage with)\b

# SG-07 possible long appositive (candidate only)
^[A-Z][^.?!]{0,80},\s[^.?!]{8,120},\s(?:is|are|was|were|has|have)\b

# SG-08 scare quotes (candidate only)
"[^"]{1,40}"|'[^']{1,40}'

# SG-09 metaphorical expressions
\b(?:rosy prospects|normalize language, thereby standardizing|shaping teachers' practice|foster authentic learning)\b

# SG-10 vague transition sentence candidates
\b(?:Encouraging practices have been carried out|Several studies have investigated the relationship between them|School norms or policies have profound implications)\b

# SG-11 duplicate purpose statement cues
\b(?:Addressing this gap|this study investigates|this study explores|this study aims to)\b

# SG-12 rhetorical question title cues
^(?:Why|How|What makes)\b|\?$

# SG-13 double verb stacks
\b(?:should|needs? to|was found to)\s+(?:prioritize\s+)?(?:actively\s+)?\w+ing\b

# SG-14 demonstrative/ordinal preferences
\b(?:Those|Because of that|Secondly|Firstly)\b

# SG-15 abbreviation misuse cues
\b[A-Za-z][A-Za-z' -]+\s\([A-Z]{2,}s?\)

# SG-16 sentence merge candidates
\.[ ]+It was argued that\b|\.[ ]+[A-Z][a-z]+, specifically,

# SG-17 unhedged strong claim cues
\b(?:GenAI provides|is shaped by|is responsible for|fundamentally alters|It is compounded by)\b

# SG-18 and colleagues
\b[A-Z][a-zA-Z-]+\s+and colleagues\s*\(\d{4}\)

# SG-19 with + ing
\bwith\s+(?:the\s+)?[A-Za-z-]+(?:\s+[A-Za-z-]+){0,3}\s+\w+ing\b

# SG-20 non-human possessive
\b(?:GenAI|AI|ChatGPT|technology|system|platform|tool|model|approach|method|framework|study|research|field|literature|review|algorithms|versions)'s\b|\b(?:students|teachers|algorithms|versions)'\b

# SG-21 filler words (often/also/both require heuristic validation, not auto-flag)
\b(?:various|diverse|significant|substantial|comprehensive|extensive|immediate|rapid|deeper|particularly|fundamentally|overarching|crucial|essential|key|robust|concrete|broader|greater|naturally|explicitly|merely|full-cycle|structured|just|background|collectively)\b

# SG-22 colon list introducer candidates
:[ ]+(?:[a-zA-Z][^.!?]{3,120}(?:,|;))

# SG-23 mid-sentence citation
\([A-Z][A-Za-z&.,\s-]+,\s\d{4}(?:,\sp\.\s\d+)?\)\s+[a-z]

# A-1 core compound-word list
\b(?:teacher-led|AI-generated|GenAI-based|discipline-specific|context-specific|student-facing|instructor-led|AI-powered|AI-assisted)\b

# B-2 formal/Latin vocabulary
\b(?:via|post hoc|solely|merely|instance|subsequent|rationale|specificity|comparability|regimes|relevance|actionable|competencies)\b

# B-3 nominalization patterns
\b(?:the articulation of|the identification of|the operationalization of|The introduction of .+? as a concept|The transition between|The description of|the concept of|a lack of studies focusing on|Strategic prompt engineering enabled|Research has developed)\b

# C-1 sentence-initial While
(?:^|[.!?]\s+)While\s

# C-3 passive templates
\b(?:It is recommended that|The title could be revised|This should be considered|It was argued that)\b

# C-4 editorializing
\b(?:It is rightfully so|must-have segment|After just \d+\s+(?:day|days|week|weeks|month|months|year|years)|just \d+\s+(?:day|days|week|weeks|month|months|year|years))\b

# C-5 not X but Y
\bnot\s+[^,.]{1,80}?\s+but\s+(?:as\s+)?[^,.]{1,80}
```

## Context
- The author's natural style uses `Drawing on...`, `Given...`, concessive framing, `However, [gap]. Thus, this study...`, colon elaboration, and former/latter comparison.
- The author prefers hedging: `may`, `can`, `often`, `relatively`, `potentially`.
- Average sentence length is about 25-45 words.
- Preserve key collocations: `noticing and utilizing`, `activate their agency`, `enact agency`, `exert agency`, `emerging affordances`, `pedagogical affordances`, `ecological perspective`, `diverse mediating factors`.

## Decision Rules
- Do not penalize colon use when it performs genuine elaboration in the author's preferred style.
- Do not flag `While...` merely because it exists; flag it when it creates an avoidable concessive lead that conflicts with the detector's editing taxonomy.
- Do not flag passives used for methods or standard procedure reporting.
- Do not flag `diverse mediating factors`; it is a preferred collocation in this style.
- Do not flag hedging; under-hedging is more important than over-hedging.
- **Do not flag en-dashes (–)** used for number ranges (2022–2023) or relationships (parent–child interaction); they are correct and distinct from em-dashes (—).
- **Do not auto-flag `often`, `also`, or `both`** as filler words; they serve legitimate logical and emphatic functions. Only flag when clearly redundant.

## Summary Statistics Requirements
- Always return `status` and `version`.
- Always include `summary.total_violations`.
- Always include `summary.by_category` for `A`, `B`, `C`, `D`.
- Always include `summary.by_severity` for `S1`, `S2`, `S3`.
- If no violations are found, return an empty `violations` array and zero counts.

## Final Check Before Returning
1. Confirm every violation has `rule_id`, `subrule_id`, `category`, `severity`, `span`, `location`, `reason`, and `suggestion`.
2. Confirm the assigned `rule_id` matches the taxonomy.
3. Confirm overlapping hits are deduplicated to the most informative rule.
4. Confirm direct quotes and legitimate citations are not falsely flagged.
5. Confirm output is valid JSON.
