# Deliberation Protocol — Multi-Reviewer Result Synthesis

## Purpose

Define how to compare, classify, and present results from
multiple reviewers (R1–R4) who independently reviewed the same text.

---

## Process

### Step 1: Collect Results

All reviewers run in **parallel** (multiple Agent tool calls in one response).
Wait for all to complete. Each returns:

```
ISSUES: [
    { id, description, location, severity, confidence }
]
SUGGESTIONS: [
    { id, original_text, revised_text, rationale, evidence_source }
]
CONFIDENCE: HIGH | MEDIUM | LOW (per issue)
```

### Step 2: Match and Classify

Compare issues across reviewers by **location** (same sentence/paragraph)
and **type** (same kind of problem).

Two issues "match" when they:
- Point to the same text span (same sentence or overlapping words)
- Identify the same category of problem (even if worded differently)

### Step 3: Classify into Three Categories

#### Category 1: Consensus (2+ reviewers agree)

Two or more reviewers flagged the same issue.

**Presentation:**
```markdown
#### [합의] — Issue title
**지적:** R1, R3 동의 | R2 동의하나 다른 수정안 | R4 미지적

[문제 설명 — 한국어]

**수정안 A:** `[R1's suggestion]`
**수정안 B:** `[R3's suggestion]` (if different)
**근거:** [evidence source, if any]
```

**User action:** Choose between suggestions or modify.

#### Category 2: Unique Finding (1 reviewer only, with evidence)

Only one reviewer flagged it, but provides a rationale.

**Presentation:**
```markdown
#### [R1 발견] — Issue title
[문제 설명 — 한국어]

**수정안:** `[suggestion]`
**근거:** [what knowledge/rule informed this finding]
```

**User action:** Accept, reject, or request more detail.

#### Category 3: Conflict (reviewers disagree)

Reviewers propose contradictory changes to the same text.

**Presentation:**
```markdown
#### [의견 충돌] — Issue title
[충돌 설명 — 한국어]

**R1 의견:** `[R1's view + suggestion]`
  근거: [R1's rationale]
**R4 의견:** `[R4's view + suggestion]`
  근거: [R4's rationale]

[왜 이 충돌이 발생했는지 설명]
```

**User action:** Choose one or provide own resolution.

---

## Presentation Order

1. **Consensus issues** first (most reliable)
2. **Unique findings** next (may catch things consensus missed)
3. **Conflicts** last (require user judgment)

Within each category, order by severity (HIGH > MEDIUM > LOW).

---

## No-Issue Consensus

If all reviewers agree there are no issues with the current text:

```markdown
### [전원 동의] — 문제 없음
R1, R2, R3, R4 모두 이 [문장/단락/섹션]에 수정이 필요하지 않다고 판단했습니다.

---
*"다음" / "그래도 자세히 봐줘"*
```

---

## Confidence-Driven Actions

After deliberation, assess overall confidence:

```
All issues HIGH confidence → Present results, expect quick decision
Any MEDIUM confidence → Present with detailed explanation
Any LOW confidence → Flag to user:
    "이 부분에 대해 리뷰어들의 확신이 낮습니다.
     추가 검색을 할까요? (웹에서 유사 논문의 표현을 찾아봅니다)"
    
    If user agrees → run web search for comparable expressions
    If user declines → proceed with available suggestions
```

---

## Mode-Specific Deliberation

### Mode 1: Paper — Structural deliberation

Focus on: cross-section consistency, argument arc, coverage gaps.
Each reviewer provides a structure assessment.
Deliberation compares: which structural issues overlap?

Output: priority section list (consensus-ranked).

### Mode 2: Section — Paragraph arrangement deliberation

Focus on: paragraph order, move structure, missing/redundant paragraphs.
Deliberation compares: which paragraphs need attention?

Output: paragraph-level issue map.

### Mode 3: Paragraph — Intent and sentence deliberation

**Paragraph phase:**
Focus on: does the paragraph deliver the confirmed intent?
Deliberation: do reviewers agree on whether intent is delivered?

**Sentence phase:**
Focus on: logic, style, hedging, terminology, factual accuracy.
Deliberation: standard 3-category classification per sentence.

---

## Deliberation Statistics (Session Summary)

Track across the session:

```
deliberation_stats = {
    total_units_reviewed: int,       // sentences, paragraphs, or sections
    consensus_issues: int,           // 2+ reviewers agreed
    unique_findings: int,            // 1 reviewer only
    conflicts: int,                  // reviewers disagreed
    no_issue_consensus: int,         // all agreed no problem
    user_accepted_consensus: int,    // user followed consensus
    user_accepted_unique: int,       // user accepted unique finding
    user_resolved_conflict: int,     // user resolved a conflict
    user_rejected: int               // user rejected a suggestion
}
```
