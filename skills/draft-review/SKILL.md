---
name: draft-review
description: |
  Review and improve user's draft (paragraph/section) against multiple reference paper
  extraction files (logic + vocab). Per-paper Reviewer subagents run parallel reviews,
  then Orchestrator synthesizes using four academic writing principles.
  Triggers: "초고 점검", "단락 개선", "리뷰해줘", "draft review".
  **MUST read references/review_template.md before starting!**
allowed-tools: [Read, Write, Edit, Bash, Task, Glob, Grep]
---

> **REQUIRED**: Read `references/review_template.md` before execution.
> The template is the **authoritative source** for all detailed procedures and output formats.

# Draft Review Skill

## Overview

Review a user's academic draft (paragraph or section) by comparing it against pre-extracted reference paper analysis files (`_logic.md`, `_vocab.md`) from multiple angles, then produce an improved version.

**Core Architecture:**
```
User's draft text
    ↓
[Phase 1] Input parsing & extraction file discovery
    ↓
[Phase 2] Reviewer-1 ~ Reviewer-N parallel review (one subagent per paper)
    ↓
[Phase 3] Orchestrator synthesis & output (four academic principles applied)
    ↓
Output: Review_{timestamp}/ folder with all reports + improved draft
```

**Relationship to Other Skills:**
- **Downstream consumer** of `logic-extraction` and `vocab-extraction` outputs
- **Complementary** to `style-guide Mode B`: style-guide uses style databank; draft-review uses logic+vocab extraction files

---

## Triggers

### Korean
- "이 단락 리뷰해줘"
- "초고 점검해줘"
- "이 문단 개선해줘"
- "논리랑 용어 점검해줘"
- "extractions 기반으로 수정해줘"
- "참조 논문 기준으로 고쳐줘"

### English
- "review this draft"
- "draft review"
- "check logic and terminology"
- "improve this paragraph"
- "review against extractions"

---

## Input Requirements

### Required
1. **Draft text**: Paragraph or section to review (inline text or file path)
2. **Section type**: Introduction / Methods / Results / Discussion (explicit or auto-detected)

### Optional
- **Extraction directory**: Path to folder containing `_logic.md` and `_vocab.md` files (default: `./extractions/` or current directory)
- **Reference papers**: All (default) or specific papers only
- **Focus**: logic / vocab / both (default: both)
- **Intensity**: Light (flag only) / Standard (flag + rewrites, default) / Deep (full rewrite)

### Intensity Definitions

| Intensity | Scope | Description |
|-----------|-------|-------------|
| **Light** | Flag issues only | Identify problems without providing rewrites. Diagnostic report only. |
| **Standard** | Flag + rewrite Critical/Major | Provide concrete rewrites for Critical and Major issues. Minor issues flagged only. |
| **Deep** | Full rewrite | Rewrite all issues + suggest structural reorganization. Four principles fully applied. |

### Severity Definitions

| Severity | Criteria | Examples |
|----------|----------|----------|
| **Critical** | Logical fallacy, factual term error, overclaim | Missing evidence for a claim; incorrect technical term; unsupported assertion |
| **Major** | Missing transition, terminology inconsistency, register violation | No connector between paragraphs; synonym switching; informal verb in Methods |
| **Minor** | Suboptimal phrasing, slight rhythm issue, style preference | Sentence too long; repetitive opening; could use stronger verb |

### Input Examples
```
> "Review this Introduction paragraph. Use all files in the extractions folder."

> "Improve this Discussion paragraph. Only reference ngeo1617 and Review_urban."

> "Check logic flow only. Light mode."

> "Deep rewrite this Methods section using all reference papers."

> "Review this paragraph using extractions from Z:\other_project\extractions."
```

---

## Workflow

**MUST read `references/review_template.md` first and follow it exactly!**

### Phase 1: Input Parsing & Extraction File Discovery

```
1. Acquire draft text
   → Inline text: capture directly
   → File path: read the file

2. Determine section type
   → Explicit: user states "Introduction", "Methods", etc.
   → Auto-detect heuristics:
     - "previous studies", "remains unknown" → Introduction
     - "was performed", "were collected" → Methods
     - "Figure X shows", "Table X presents" → Results
     - "suggests that", "consistent with" → Discussion

3. Scan extraction directory
   → Find *_logic.md and *_vocab.md files
   → Match file pairs by paper name (e.g., ngeo1617_logic.md + ngeo1617_vocab.md)

4. Confirm reference paper list
   → Display discovered papers to user
   → Ask: use all or select specific ones?

5. Set parameters & output task specification
```

### Phase 2: Parallel Review (Reviewer-1 ~ Reviewer-N)

Assign **one subagent per reference paper**.

Each Reviewer performs three review steps (see `review_template.md` Phase 2 for detailed procedures):

```
FOR EACH reference paper (i = 1 to N):
    → Launch Task (Subagent): Reviewer-{i}
    → Input: draft text + paper's _logic.md + _vocab.md

    Step A: Logic Review (from _logic.md)
        - Argument structure check (Claim→Evidence→Warrant)
        - Given-New information flow
        - Transition appropriateness
        - Sentence frame matching against rhetorical templates

    Step B: Vocabulary Review (from _vocab.md)
        - Technical term accuracy and consistency
        - Register check (informal expression detection)
        - Synonym/near-synonym consistency
        - Section-appropriate vocabulary validation

    Step C: Generate Suggestions
        - Aggregate issues from Steps A and B
        - Classify severity: Critical / Major / Minor
        - Generate concrete rewrites (per intensity level)
```

**Output**: Structured Reviewer report → see `review_template.md` Reviewer Output Template.

### Phase 3: Orchestrator Synthesis & Output

Collect all Reviewer reports and optimize using **four academic writing principles**.

```
Step 1: Collection & Classification
    → Merge all Reviewer suggestions by category
    → Identify COMMON issues (2+ Reviewers flag same problem → high priority)
    → Identify CONFLICTS (contradictory suggestions)

Step 2: Apply Four Academic Principles

    [A] Argument Architecture
        - Claim → Evidence → Warrant triad
        - Given-New Contract: given info first, new info last
        - Paragraph unity: one paragraph = one central claim
        - Logical transitions: explicit inter-paragraph connectors

    [B] Prose Rhythm
        - Sentence length variation: long-short alternation (prevent monotony)
        - Information density control: key claims short and strong
        - End-focus: most important information at sentence end
        - Parallel structure: grammatical parallelism in series
        - Sentence opening variety: avoid 3+ consecutive same-pattern openings

    [C] Cohesion & Coherence
        - Lexical chains: consistent recurrence of key terms
        - Thematic progression: natural Theme-Rheme flow
        - Reference balance: repetition vs. pronoun equilibrium
        - Conjunctive ties: appropriate placement of however, moreover, consequently

    [D] Academic Register
        - Hedging: may, suggest, indicate — assertion vs. reservation calibration
        - Objectivity: avoid unnecessary first-person, balance active/passive
        - Nominalization: appropriate level of abstraction
        - Section convention: IMRaD section-specific tone

Step 3: Conflict Resolution & Priority Ordering
    → Critical + Common > Critical + Unique > Major + Common > Major + Unique > Minor
    → Section-specific preferences (Discussion → prefer hedged; Methods → prefer precise)
    → Content preservation: NEVER alter scientific meaning

Step 4: Generate Improved Draft
    → Apply changes in priority order
    → After each change: re-check rhythm and cohesion; adjust if broken
    → Final pass for overall flow

Step 5: Write Output Files
    → Write all files to Review_{timestamp}/ folder
    → Validate against checklist (see review_template.md VALIDATION CHECKLIST)
```

---

## Output Structure

```
Review_{YYYYMMDD_HHMMSS}/
├── input.md                 # Original draft + metadata
├── reviewer_1.md            # Reviewer-1 report (paper A)
├── reviewer_2.md            # Reviewer-2 report (paper B)
├── ...
├── reviewer_N.md            # Reviewer-N report (paper N)
├── synthesis.md             # Cross-reviewer synthesis + principle evaluation
└── improved_draft.md        # Final improved text + change log
```

### File Summaries

| File | Key Sections | Details |
|------|-------------|---------|
| `input.md` | Metadata + original draft | Date, section type, focus, intensity, full draft text |
| `reviewer_{i}.md` | A. Paper Info → B. Logic Review (B1-B4) → C. Vocab Review (C1-C3) → D. Consolidated Suggestions → E. Summary Stats | One per reference paper |
| `synthesis.md` | A. Common Issues → B. Conflicts & Resolutions → C. Principle Evaluation (C1-C4) → D. Final Priority List | Cross-reviewer synthesis |
| `improved_draft.md` | A. Before → B. After → C. Change Log → D. Principles Applied → E. Unapplied Suggestions → F. Self-Assessment Checklist | Final deliverable |

**For complete output templates with exact table columns and formats**, see `references/review_template.md` Step 3.6: Output Generation.

---

## Parallel Processing (Subagent)

### Reviewer Parallel Execution

```
N reference papers found
→ Launch N Task (Subagent) calls concurrently
→ Each Subagent:
    1. Read references/review_template.md
    2. Read assigned paper's _logic.md + _vocab.md
    3. Compare against user's draft
    4. Output structured review report
→ After ALL Subagents complete → run Orchestrator (Phase 3)
```

### Subagent Prompt Template

```
You are Reviewer-{i}. Your task is to review the user's draft text
against extraction files from a reference paper.

**MUST READ FIRST**: {skill_path}/references/review_template.md

**Draft text**: {draft_text}
**Section type**: {section_type}
**Logic file**: {logic_file_path}
**Vocab file**: {vocab_file_path}
**Focus**: {logic/vocab/both}
**Intensity**: {Light/Standard/Deep}

Follow the Reviewer Output Template in review_template.md exactly.
Output your report as structured markdown.
```

---

## Quality Standards

1. **Evidence-based**: Every suggestion must cite a specific pattern/term/template from extraction files. No generic advice.
2. **Content preservation**: NEVER alter scientific meaning, claims, or data — expression only
3. **Completeness**: Every sentence in the draft must be evaluated
4. **Actionability**: Every suggestion must include a concrete rewrite (not just "improve this"), except in Light mode
5. **Principle-tagged**: Every Orchestrator change must specify which of the four principles it serves
6. **Traceable**: Every change must trace back to a specific Reviewer's suggestion
7. **Prioritized**: Changes ordered Critical > Major > Minor
8. **Rhythm-verified**: Improved draft must show sentence length variation (CV > 0.3)

---

## Error Handling

| Situation | Response |
|-----------|----------|
| No extraction files found | Guide user to run `logic-extraction` / `vocab-extraction` first |
| Only logic files (no vocab) or vice versa | Proceed with available files + warn about limited scope |
| Section type unclear | Ask user to specify |
| Draft too short (< 2 sentences) | Warn: "Paragraph-level or longer text recommended" |
| Only 1 reference paper available | Warn: "Single reference — cross-validation limited" |
| Extraction file format unexpected | Attempt best-effort parsing, flag issues |
| Subagent failure | Skip failed Reviewer, synthesize from remaining reports |

---

## Usage Examples

### Example 1: Basic usage
```
> "Review this Introduction paragraph."
→ Auto-scan extractions/ folder
→ All discovered reference papers → parallel review
→ Standard intensity → improved draft output
```

### Example 2: Specific papers only
```
> "Improve this Discussion paragraph. Only use ngeo1617 and Review_urban."
→ Reviewer-1 (ngeo1617) + Reviewer-2 (Review_urban) only
```

### Example 3: Logic-only check
```
> "Check logic flow only. Light mode."
→ Read _logic.md files only, flag issues without rewrites
```

### Example 4: Full rewrite
```
> "Deep rewrite this Methods section."
→ All reference files, all four principles fully applied, structural reorganization
```

### Example 5: External directory
```
> "Review this paragraph using Z:\other_project\extractions."
→ Use extraction files from specified path
```

---

**Version**: 1.1.0
**Skill by**: Meta_researcher / draft-review
