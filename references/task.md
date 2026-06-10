# My Writing Style Skill - Development Task List

## 📋 Overview

This document tracks the development of `my-writing-style` skill from basic (Option A) to advanced (Option B/C).

---

## ✅ COMPLETED: Today (2026-05-25) - Option A Implementation

### Phase 1: Structure & Organization ✅

**Status:** COMPLETE

**Completed Tasks:**
- [x] Created `references/` folder
- [x] Updated `SKILL.md` with category structure (A, B, C, D)
- [x] Added priority levels (🔴 HIGH, 🟡 MEDIUM, 🟢 LOW) to all rules
- [x] Reorganized rules into logical categories
- [x] Created `style-taxonomy.md` with complete taxonomy matrix
- [x] Added severity levels (S1, S2, S3)
- [x] Added application order guidelines
- [x] Added validation checklist

**What Changed:**
- Before: Flat list of 7 rules
- After: 4 categories, 11 rules, priority-based application

**Files Created/Modified:**
- `SKILL.md` - Major update with categories and priorities
- `references/style-taxonomy.md` - New taxonomy documentation

---

## 📅 TOMORROW (2026-05-26) - Option A Enhancement

### Phase 2: Example Enrichment 📝

**Goal:** Add more real-world examples to make the skill more practical

**Prerequisites:**
- [ ] Locate track changes document (with revision history)
- [ ] Locate old published paper (for style reference)

**Tasks:**

#### Task 2.1: Extract Examples from Track Changes
**Time:** 30 minutes
**Priority:** HIGH

**Steps:**
1. Open track changes document
2. For each rule, find 1-2 real examples:
   - Copy "Before" text (original)
   - Copy "After" text (revised)
   - Note which rule applies
3. Add extracted examples to SKILL.md

**Target:**
- A-1 (Compound words): 2 examples
- A-2 (Possessive): 2 examples
- B-1 (Filler words): 3 examples
- C-1 ("While" clauses): 2 examples
- C-2 ("with ~ing"): 2 examples

#### Task 2.2: Analyze Old Paper for Patterns
**Time:** 30 minutes
**Priority:** MEDIUM

**Steps:**
1. Read old published paper
2. Identify patterns that are "naturally" in your style:
   - What filler words do you NOT use?
   - What structures do you avoid?
   - What is your typical sentence length?
3. Note these as "positive examples" (what TO do)

**Output:**
Add to `references/positive-examples.md` (create new file):
```markdown
# Positive Style Patterns (What TO Do)

Based on analysis of [paper title/year]:

## Sentence Length
- Average: X words
- Range: Y-Z words
- Pattern: [mix of short/medium/long]

## Preferred Structures
- [pattern 1]
- [pattern 2]

## Absent Patterns (confirmed not in your style)
- "various" - never used
- "significant" - rarely used
- etc.
```

#### Task 2.3: Create Exception Documentation
**Time:** 15 minutes
**Priority:** LOW

**Steps:**
1. Review existing exceptions in SKILL.md
2. Add any additional exceptions discovered
3. Document field-specific exceptions

**Output:**
Update `references/style-taxonomy.md` Exceptions section

---

## 🎯 Option B Development Plan

### When to Start Option B?
**Trigger:** After completing Tomorrow's tasks AND testing the current skill on 2-3 real documents

### Option B: Agent-Based Architecture

**What is Option B?**
Transform the static rule list into an agent pipeline:
- **Detector Agent:** Finds violations
- **Rewriter Agent:** Applies fixes based on findings
- **Validator Agent:** Checks quality and consistency

**Why Option B?**
- Handles rule conflicts automatically
- Provides quality feedback
- Can do partial rewrites ("fix only Category A")
- More reliable for long documents

---

## 🔧 Option B Implementation Tasks

### Phase 3: Create Agent Definitions ✅ COMPLETE

#### Task 3.1: Create academic-style-detector.md ✅
**Status:** COMPLETE
**File:** `_agents/academic-style-detector.md`
**Content:** 530 lines, 23 SG subrules + 11 taxonomy rules, regex patterns, JSON output schema

**Content Outline:**
```markdown
---
name: academic-style-detector
description: Detects academic writing style violations based on my-writing-style taxonomy
---

# Academic Style Detector

## Input
- Document text
- (Optional) Category filter (A, B, C, D)

## Output Format
```json
{
  "violations": [
    {
      "rule_id": "A-1",
      "category": "Word Formation",
      "severity": "S1",
      "span": "AI-generated materials",
      "location": "paragraph 3, sentence 2",
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

## Rules to Detect
[List all rules with regex patterns]
```

#### Task 3.2: Create academic-style-rewriter.md ✅
**Status:** COMPLETE
**File:** `_agents/academic-style-rewriter.md`
**Content:** 215 lines, hard invariants, conflict resolution, change log format, style preservation guidelines

#### Task 3.3: Create academic-style-validator.md ✅
**Status:** COMPLETE
**File:** `_agents/academic-style-validator.md`
**Content:** 187 lines, hard/soft checks separation, 4 recommendation levels, JSON output schema

---

### Phase 4: Update SKILL.md for Agent Mode ✅ COMPLETE

#### Task 4.1: Add Agent Mode Section ✅
**Status:** COMPLETE
**File:** `SKILL.md`
**Content:** Added "Agent Mode (Option B)" section with mode selection matrix, 3-agent pipeline description, hard invariants, agent file references

---

## 📊 Decision Matrix

| Scenario | Use Option A | Use Option B |
|----------|--------------|--------------|
| Quick email/short text | ✅ Yes | ❌ Overkill |
| < 1000 words | ✅ Yes | Optional |
| 1000-3000 words | ✅ Yes | Recommended |
| > 3000 words | ⚠️ OK | ✅ Better |
| Journal submission | ⚠️ OK | ✅ Recommended |
| Need change log | ❌ No | ✅ Yes |
| Rule conflicts expected | ❌ No | ✅ Yes |

---

## 🎓 Learning Path

### Week 1 (This Week)
- [x] Day 1: Option A structure ✅
- [ ] Day 2: Example enrichment (tomorrow)
- [ ] Day 3-4: Test on 2-3 real documents
- [ ] Day 5: Evaluate if Option B needed

### Week 2 (Next Week - if Option B needed)
- [ ] Create detector agent
- [ ] Create rewriter agent
- [ ] Create validator agent
- [ ] Test agent pipeline
- [ ] Document differences

---

## 📝 Testing Checklist

After each phase, test with:

### Test Document 1: Short Academic Text (500 words)
- [ ] All S1 violations caught
- [ ] Changes improve readability
- [ ] No data changed

### Test Document 2: Medium Paper Section (1500 words)
- [ ] Consistent application across paragraphs
- [ ] No conflicting changes
- [ ] Academic tone maintained

### Test Document 3: Full Abstract + Introduction (2500 words)
- [ ] Abstract handled appropriately
- [ ] Introduction flows well
- [ ] Citation placement improved

---

## 🚀 Success Metrics

### Option A Success
- [ ] Can process any academic text
- [ ] Changes are consistent
- [ ] Quality improves (subjective assessment)
- [ ] No errors introduced

### Option B Success
- [ ] Agent pipeline works end-to-end
- [ ] Change log generated automatically
- [ ] Quality validation catches issues
- [ ] Can handle partial rewrites

---

## 📚 Resources

### Reference Files
- `SKILL.md` - Main skill file
- `references/style-taxonomy.md` - Complete rule taxonomy
- `references/positive-examples.md` - What TO do (create tomorrow)
- `references/task.md` - This file

### Inspiration
- `../humanize-korean/` - Example of Option B implementation

---

## 💡 Notes & Ideas

### Future Enhancements (Option C)
- [ ] Field-specific variants (e.g., "my-education-style", "my-linguistics-style")
- [ ] Voice profile (learn from more papers)
- [ ] Statistical analysis of my patterns
- [ ] Integration with citation manager

### Known Limitations
- Current: Rules are prescriptive (tells what to do)
- Future: Could be descriptive (learns from examples)

---

**Last Updated:** 2026-05-28
**Current Status:** Option B Complete - Refined rules based on updated style_guide_paper.md
**Next Review:** Test agent pipeline on 2-3 real documents

### Files Created/Modified Today (2026-05-28)
- [x] `SKILL.md` - Refined A-1 (hyphen preservation), B-1 (filler word nuance), D-1 (em-dash vs en-dash)
- [x] `_agents/academic-style-detector.md` - Updated SG-21 regex, Taxonomy A-1/B-1/D-1, en-dash exceptions
- [x] `_agents/academic-style-rewriter.md` - Updated Category A/B/D rewrite guidance
- [x] `references/style-taxonomy.md` - Updated A-1, B-1, D-1 principles and exceptions

### Key Rule Refinements (2026-05-28)
1. **A-1 Compound Words**: Relaxed to preserve hyphens that clarify meaning (`AI-assisted`, `well-known`, `pre-service`). Only flag artificial compound labels.
2. **B-1 Filler Words**: Removed `often`, `also`, `both` from auto-deletion list. They are legitimate logical connectors.
3. **D-1 Punctuation**: Added explicit distinction between em-dash (—) to avoid and en-dash (–) which is correct for ranges/relationships.
4. **Example fix**: `significant to` → `significant for` in style_guide_paper.md example

### Files Created/Modified Today (2026-05-26)
- [x] `_agents/academic-style-detector.md` - 530 lines, 23 SG subrules + 11 taxonomy rules
- [x] `_agents/academic-style-rewriter.md` - 215 lines, hard invariants + conflict resolution
- [x] `_agents/academic-style-validator.md` - 187 lines, 4-level recommendation system
- [x] `references/positive-examples.md` - 385 lines, evidence-based style analysis from 3 published papers
- [x] `SKILL.md` - Added Agent Mode (Option B) section with pipeline description
