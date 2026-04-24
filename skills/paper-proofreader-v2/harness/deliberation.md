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
    { id, criterion, description, location, severity, confidence }
]
SUGGESTIONS: [
    {
        id, issue_id, original,
        alternatives: [
            { label, revised, tone, rationale }  // 1-3 items (see agent_reviewer.md Rule 11)
        ],
        evidence_source
    }
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

**수정안 (R1-A, concise):** `[R1 alternative A text]`
**수정안 (R1-B, formal-precise):** `[R1 alternative B text]`
**수정안 (R3-A, readable):** `[R3 alternative A text]`
**근거:** [evidence source, if any]
```

For LOGIC / STRUCTURE / FACTUAL issues, each reviewer usually provides a
single alternative — display only what exists. For STYLE / HEDGING /
TERMINOLOGY issues, reviewers typically provide 2-3 alternatives per
agent_reviewer.md Rule 11; show up to 4 alternatives total across reviewers
(prefer variety of `tone` labels over redundant near-duplicates).

**User action:** Choose between alternatives (`"R1-A 적용"`, `"R3-A 적용"`) or modify.

#### Category 2: Unique Finding (1 reviewer only, with evidence)

Only one reviewer flagged it, but provides a rationale.

**Presentation:**
```markdown
#### [R1 발견] — Issue title
[문제 설명 — 한국어]

**수정안 (A, [tone]):** `[alternative A text]`
**수정안 (B, [tone]):** `[alternative B text]`   (only if reviewer provided multiple per Rule 11)
**근거:** [what knowledge/rule informed this finding]
```

**User action:** Accept (`"적용"` for single alt, `"A 적용"` / `"B 적용"` for multiple), reject, or request more detail.

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

## Top-N Priority Sort (Closing Block)

After classification (consensus / unique / conflict) and confidence routing,
the orchestrator must produce a **Top-N Priority Revisions** block as the
closing element of every Mode 1 and Mode 2 review. This block answers the
user's first practical question: *"Of all these findings, what should I fix
first?"*

### When to produce

| Mode | Top-N produced? | N |
|---|---|---|
| Mode 1 (Paper) | Yes | 5 |
| Mode 2 (Section) | Yes | 5 |
| Mode 3 — paragraph phase | Yes | 3 |
| Mode 3 — sentence phase | No (per-sentence decisions are atomic) |

### Ranking rules

Pool every issue across all reviewers (consensus + unique + conflict).
Score each issue by **impact**, then sort descending. Impact = a
combination of severity, agreement, and category weight:

```
impact_score =
    severity_weight (CRITICAL=4, HIGH=3, MEDIUM=2, LOW=1)
  + reviewer_agreement (count of reviewers flagging it, max 4)
  + category_weight
```

Where `category_weight` adds priority for findings that affect
the paper's credibility, not only its style:

| Finding category | category_weight |
|---|---|
| Numerical inconsistency (from `quantitative_integrity.md`) | +3 |
| Secondary-source citation for quantitative claim | +2 |
| Reference verification failure (from `agent_b.md` NOT_FOUND) | +3 |
| Terminological inconsistency across sections (Banana Rule) | +2 |
| Argument-structure / logic issue | +2 |
| Cohesion / Given-New violation | +1 |
| Hedge under/over-calibration | +1 |
| Clutter / redundancy | +1 |
| Sentence-craft polish | +0 |

Tie-break order: severity → agreement → location (earlier in document first).

### Output format

```markdown
### 우선 수정 [N]건 (영향도 순)

| 순위 | 위치 | 분류 | 문제 요약 | 합의/발견/충돌 | 영향도 |
|---|---|---|---|---|---|
| 1 | [Section/Para/Sent ref] | [category] | [1-line summary] | [agreement tag] | [score] |
| 2 | ... | ... | ... | ... | ... |
| 3 | ... | ... | ... | ... | ... |
```

Below the table, expand the **top item** with its full revision suggestion
so the user can act immediately. The remaining items reference the detail
already presented in the consensus/unique/conflict blocks above.

```markdown
**1순위 상세:**
**[EN]** `[problematic text]`
**수정안 (A, [tone]):** `[alternative A text]`
**수정안 (B, [tone]):** `[alternative B text]`   (if reviewer provided multiple per Rule 11)
**근거:** [rationale]

→ "1순위 A 적용" / "1순위 B 적용" / "2순위 보기" / "전체 보기"
```

When only a single alternative exists (LOGIC / STRUCTURE / FACTUAL issues, or LOW severity), display one `**수정안:**` line without an A/B label.

### Suppression rule

If fewer than N issues exist, list only what exists. If zero issues exist
across all reviewers, omit the block and replace with:

```markdown
### 우선 수정
이 [모드 단위]에서 우선 수정할 항목이 없습니다.
```

### Interaction with confidence routing

If the top-ranked item has CONFIDENCE: LOW, the orchestrator must still
present it but flag it explicitly:

```markdown
**1순위** [LOW 신뢰도] — 추가 검색을 권장합니다.
```

The user can request "검색해봐" to invoke the web-search supplement before
deciding.

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
    user_rejected: int,              // user rejected a suggestion
    // Integrity counters from Agent B (see agents/agent_b.md):
    ref_not_found: int,              // citation existence failures
    numeric_inconsistencies: int,    // N/percentage/sig-fig conflicts
    secondary_citations_flagged: int // Telephone Game audit hits
}
```
