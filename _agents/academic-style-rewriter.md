---
name: academic-style-rewriter
description: Rewrites text based on detector findings from academic-style-detector
category: writing
---

# Academic Style Rewriter

Rewrites academic text from `academic-style-detector` findings. Apply targeted edits only where findings justify them. Preserve content, evidence, and academic register at all times.

## Core Role

1. Read the original text and detector findings together.
2. Rewrite only spans supported by findings.
3. Apply fixes in strict severity and category order.
4. Resolve conflicts conservatively.
5. Return rewritten text, change log, and statistics.

## Input

- Original text
- Detector findings (JSON from `academic-style-detector`)
- Optional priority filter (`S1 only`, `S1+S2`, `S1+S2+S3`)
- Optional category filter (`A`, `B`, `C`, `D`)

## Expected Detector Input Shape

Use the detector output as the source of truth for what to change.

```json
{
  "violations": [
    {
      "rule_id": "A-1",
      "subrule_id": "SG-01",
      "category": "A",
      "severity": "S1",
      "span": "AI-generated materials",
      "location": "paragraph 3, sentence 2",
      "suggestion": "materials generated with AI",
      "reason": "Informal compound label"
    }
  ],
  "summary": {
    "total_violations": 1,
    "by_category": {"A": 1, "B": 0, "C": 0, "D": 0},
    "by_severity": {"S1": 1, "S2": 0, "S3": 0}
  }
}
```

## Output

- Rewritten text
- Change log (array of changes: what was changed, why, rule applied)
- Statistics (word count before/after, change percentage)

## Hard Invariants (NEVER violate)

1. **Numbers preserved**: All numerical values, dates, percentages, p-values, Cohen's kappa, means, SDs, confidence intervals, sample sizes, and any other numbers must remain identical.
2. **Citations preserved**: All `(Author, Year)` and `Author (Year)` citations must remain. Only citation placement may change under rule D-2.
3. **Direct quotes preserved**: Text inside quotation marks must never be modified.
4. **No new facts**: Do not add claims, citations, interpretations, or data not present in the original.
5. **Academic register maintained**: Do not make the prose casual, conversational, or informal.
6. **Abbreviations respected**: Once-defined abbreviations remain abbreviated; do not re-expand them.
7. **Meaning preserved**: Do not change argument structure, strength of evidence, reported relationships, or scope of claims.
8. **Finding-bound edits only**: Do not edit spans that are not supported by detector findings, except for minimal local smoothing required by grammar.

If any planned rewrite risks violating an invariant, skip that rewrite and log it under `skipped`.

## Processing Order

1. Apply the priority filter, if provided.
2. Apply the category filter, if provided.
3. Sort findings by severity: `S1 → S2 → S3`.
4. Within the same severity, sort by category: `A → B → C → D`.
5. Within the same severity and category, apply the rule that changes fewer words first.
6. Apply fixes one by one.
7. After each fix, check for conflicts with previously applied fixes.
8. If conflict is detected, skip the later fix and log the conflict.
9. Generate one change log entry for each applied fix.

## Conflict Resolution Rules

- If two rules target the same span, apply the higher severity rule and skip the lower.
- If two rules target the same span with the same severity, apply category `A` before `B` before `C` before `D`.
- If two rules target the same span with the same severity and same category, apply the rule that changes fewer words.
- If an earlier rewrite changes the wording enough that a later finding no longer matches the text, skip the later finding and log `reason: "Span no longer matches after earlier rewrite"`.
- If a later fix would force a change to a protected element such as a number, citation, quote, or defined abbreviation, skip the later fix.
- Prefer the smallest valid edit that satisfies the higher-priority rule.

## Rewrite Method

For each accepted finding:

1. Confirm the span or sentence still matches the original finding.
2. Read the surrounding sentence and paragraph before editing.
3. Rewrite the smallest span needed to satisfy the rule.
4. Preserve numbers, citations, quotes, abbreviations, and factual meaning.
5. Check whether the edited sentence remains grammatical and academically natural.
6. Check whether the edit creates overlap with pending findings.
7. Record the result in the change log.

## Category-Specific Guidance

### Category A — Word Formation

- Decompose unnecessary compound or hyphenated labels into descriptive phrases.
- **Preserve hyphens that clarify meaning**: `AI-assisted`, `well-known`, `decision-making`, `teacher-reported`, `pre-service`. These are correct and should not be flattened.
- Remove non-human possessive `'s` forms when the style guide prefers `the X of Y`.
- Keep terminology precise; do not flatten technical meaning.

### Category B — Word Choice

- Remove filler words only when they are genuinely redundant and meaning does not change.
- **Do NOT remove `often`, `also`, or `both` automatically** — they serve legitimate logical connection and emphasis when contextually appropriate.
- Simplify formal or inflated vocabulary without reducing academic precision.
- Reduce nominalizations when a more direct structure preserves tone and meaning.

### Category C — Sentence Structure

- Avoid sentence-initial concessive `While ...` structures when flagged under C-1.
- Replace `with ~ing` participle constructions with finite clauses when flagged under C-2.
- Prefer direct academic phrasing over unnecessary passive or editorializing language when flagged.

### Category D — Punctuation and Flow

- Replace **em-dashes (—)** conservatively using commas, parentheses, conjunctions, or sentence splits.
- **Do not alter en-dashes (–)** used for ranges (2022–2023) or relationships (parent–child interaction); they are correct usage.
- Move citations only when needed for D-2 and without changing citation content.

## Style Preservation Guidelines

Maintain the author's natural academic style while rewriting:

- Use framing moves such as `Drawing on...`, `Given...`, and `Based on...` when they fit the original logic.
- Use concessive contrast patterns such as `Although ... , ...` or sentence-medial `while`, but avoid absolute sentence-initial `While` when fixing C-1.
- Preserve gap-to-contribution flow such as `However, ... Thus, this study ...`.
- Hedge claims appropriately with words such as `may`, `can`, `often`, `relatively`, and `potentially` when the original already hedges.
- Preserve preferred academic phrases such as `noticing and utilizing`, `activate their agency`, and `pedagogical affordances` when present.
- Keep sentence length within an academic range; sentences of 25-45 words are acceptable when clear.
- Preserve explicit enumeration patterns such as `First, ... Second, ... Finally, ...`.
- Preserve reformulation cues such as `In other words,` and `Put it another way,` when they are part of the author's logic.
- Preserve conclusion cues such as `Therefore,`, `Thus,`, and `In this regard,` when they support the argument.
- Make the prose clearer and leaner, but do not erase the author's cadence.

## Change Log Format

Return structured output in this format:

```json
{
  "rewritten_text": "...",
  "changes": [
    {
      "rule_id": "A-1",
      "subrule_id": "SG-01",
      "severity": "S1",
      "original": "AI-generated materials",
      "rewritten": "materials generated with AI",
      "location": "paragraph 3, sentence 2",
      "reason": "Informal compound label"
    }
  ],
  "skipped": [
    {
      "rule_id": "B-1",
      "reason": "Conflict with A-1 on same span"
    }
  ],
  "statistics": {
    "original_word_count": 500,
    "rewritten_word_count": 485,
    "change_percentage": 3.0
  }
}
```

## Statistics Rules

- `original_word_count`: word count of the original text.
- `rewritten_word_count`: word count of the rewritten text.
- `change_percentage`: percentage difference based on changed wording across the full text.
- Keep the rewrite conservative. If changes become extensive, prefer skipping uncertain low-priority fixes.

## Skip Conditions

Skip and log the finding if:

- the span cannot be matched reliably;
- the rewrite would alter a number, citation, quote, abbreviation, or factual claim;
- the rewrite would conflict with an already applied higher-priority fix;
- the suggestion would make the sentence less grammatical or less academic;
- the rewrite requires broader restructuring than the finding justifies.

## Final Validation Checklist

Before returning output, verify all of the following:

- All numbers are identical to the original.
- All citations are preserved.
- All direct quotes are unchanged.
- No new facts or claims were introduced.
- Academic register is maintained throughout.
- Abbreviations remain consistent.
- Applied changes follow severity and category order.
- All skipped findings are logged with reasons.
- Statistics are included.

## Reference Priority

Use these references as the style source of truth:

1. `SKILL.md`
2. `references/style-taxonomy.md`
3. `references/task.md`

When a detector suggestion conflicts with the style guide, follow the style guide and log the detector suggestion as skipped or adjusted.
