---
name: paper-proofreader-v2
description: >
  Multi-reviewer academic paper proofreading with knowledge-distributed
  deliberation. 2-4 reviewers with identical instructions but different
  knowledge allocations review the same text in parallel, then deliberate
  to reach consensus. Supports 3 modes: Paper (full draft), Section,
  and Paragraph (with sentence-level review). Local knowledge files
  (extraction-knowledge, extraction-logic, extraction-vocab, PDFs,
  freeform markdown) are auto-discovered and distributed to reviewers.
  All user-facing output in Korean.
  
  Trigger phrases: "논문 교정", "paper proofreading", "proofread",
  "교정 시작", "논문 검토", "paper review", "교정해줘",
  "논문 교정 v2", "리뷰어 교정"
---

# Paper Proofreader v2 — Multi-Reviewer Deliberation Orchestrator

## 1. Environment

- **Runtime:** Claude Code with SendMessage for parallel reviewer sub-agents
- **Output language:** All user-facing output in Korean (한국어)
- **Agent internal prompts:** English (for optimal LLM performance)
- **English original text:** Always displayed alongside Korean explanation
- **User input style:** Korean or English, free-form; see `config/navigation.md` for input mapping
- **Tools used:** Read, Glob, Grep, WebSearch, WebFetch, SendMessage

---

## 2. File Map

```
paper-proofreader-v2/
├── SKILL.md                              ← This file (orchestrator)
├── agents/
│   ├── agent_e.md                        ← Knowledge Bank Builder
│   ├── agent_reviewer.md                 ← Universal Reviewer Prompt Template (R1-R4)
│   └── agent_b.md                        ← Reference Verification
├── config/
│   ├── navigation.md                     ← Mode switching & user input mapping
│   ├── output_format.md                  ← Display rules (bilingual, deliberation results)
│   └── session_management.md             ← State, save/restore, error handling
├── harness/
│   ├── deliberation.md                   ← Multi-reviewer result synthesis protocol
│   ├── confidence_routing.md             ← Adaptive workflow by confidence level
│   └── context_loading.md                ← Mode-specific loading rules
├── knowledge/
│   ├── distribution_strategy.md          ← Knowledge allocation to reviewers
│   ├── input_handler.md                  ← File type detection & parsing
│   ├── knowledge_bank_schema.md          ← Unified knowledge schema
│   └── search_strategy.md               ← Web search for knowledge supplementation
└── writing-manual/
    ├── INDEX.md                          ← Manual routing table
    ├── sections/                         ← Per-section rules (01-07)
    └── cross_section/                    ← Cross-cutting principles
```

---

## 3. Agent Configuration

| Agent | File | Instances | Role |
|---|---|---|---|
| **Agent E** | `agents/agent_e.md` | 1 | Knowledge discovery, parsing, distribution |
| **Agent R** | `agents/agent_reviewer.md` | 2-4 (R1, R2, R3, R4) | Parallel review with distributed knowledge |
| **Agent B** | `agents/agent_b.md` | 1 | Post-paragraph reference verification |
| **Orchestrator** | This file | 1 | Workflow control, deliberation, user interaction |

### Reviewer Knowledge Allocation

| Reviewer | Knowledge | Perspective |
|---|---|---|
| R1 | writing-manual + Knowledge Group A | Domain expert A |
| R2 | writing-manual + Knowledge Group B | Domain expert B |
| R3 | writing-manual only | Rule-based judge |
| R4 | None (no references) | Educated reader (LLM judgment only) |

Exact grouping rules: Read `knowledge/distribution_strategy.md`.

---

## 4. Step 0: Initialization

### 0a. Load Writing-Manual Index

Read `writing-manual/INDEX.md` to get the routing table for section-specific
manual files. Do NOT load individual section files yet — those load on demand
when entering Mode 2 or Mode 3.

### 0b. Interpret User Input

Parse the user's request to extract:

| Parameter | Source | Example |
|---|---|---|
| `paper_path` | User provides file or folder path | `Z:/KKH_Research/Project/Draft/discussion.md` |
| `section` | Explicit or inferred from filename | `Discussion` |
| `target_journal` | User states or null | `"Geoderma"` |
| `knowledge_path` | Explicit path or auto-discovered | `Z:/KKH_Research/Project/Knowledge/` |
| `mode` | User intent or default | `paper`, `section`, `paragraph` |

If the user pastes text directly without a file path, treat as Mode 3 (paragraph).
See `config/navigation.md` for full input mapping.

### 0c. Parse Paper Files

Read the paper file(s) at `paper_path`.

- If a single `.md` or `.txt` file: read fully, split into sections by `#` headings
- If a folder: glob for `*.md` files, read each, map to sections
- If a `.docx` or `.pdf`: convert to text first (use markitdown skill if available)

Build sections list:
```
sections = [{ name: str, text: str, para_count: int }]
```

### 0d. Run Agent E (Knowledge Scan + Distribution)

Follow the full Agent E workflow from `agents/agent_e.md`:

1. **Phase 1:** Scan project Knowledge/Logic/Vocab directories.
   For each file, read first 20 lines only (Read tool with `limit: 20`).
   Build `knowledge_index[]`.

2. **Phase 2:** Match files to current context keywords.
   Full-read matched files. Parse by type (6 types).
   Populate `knowledge_bank`.

3. **Phase 3:** If `knowledge_bank.quality.local_sources < 3`,
   run web search supplement (Google Scholar, Semantic Scholar API).
   Skip if user opted out.

4. **Phase 4:** Distribute knowledge to reviewers per
   `knowledge/distribution_strategy.md` rules.

### 0e. Show Distribution Summary, Get User Approval

Display the distribution table (in Korean):

```markdown
---
### Knowledge Distribution

| Reviewer | Files | Focus |
|---|---|---|
| R1 | [files] | [focus] |
| R2 | [files] | [focus] |
| R3 | writing-manual only | Rule baseline |
| R4 | (none) | LLM judgment |

Total: [N] knowledge files across [M] reviewers

---
*"이대로 진행" / "분배 변경" / "파일 추가: [경로]"*
```

Wait for user approval. Handle overrides per `knowledge/distribution_strategy.md`
User Override section.

---

## 5. Mode 1: Paper — Full Draft Review

**Entry:** User says "전체 초고 봐줘", "논문 전체 검토", "full draft", etc.

**Context loading:** Follow `harness/context_loading.md` Mode 1 rules.
Load only each section's first 2 sentences + `writing-manual/INDEX.md` routing table.

### 5a. Run Reviewers in Parallel

Launch R1-R4 (or however many active reviewers) in parallel via SendMessage.
Each reviewer receives the prompt from `agents/agent_reviewer.md` with:
- `{mode}` = `paper`
- `{target_text}` = section summaries (first 2 sentences per section)
- `{allocated_knowledge}` = per distribution plan
- `{writing_manual_content}` = INDEX.md only (not full section files)

All reviewers use mode-specific focus: STRUCTURE primary, LOGIC secondary.

### 5b. Deliberation

Collect all reviewer results. Apply `harness/deliberation.md` protocol:

1. Match issues across reviewers by location + type
2. Classify into three categories:
   - **Consensus** (2+ reviewers agree) — present first
   - **Unique finding** (1 reviewer only, with evidence) — present second
   - **Conflict** (reviewers disagree) — present last

Within each category, order by severity (HIGH > MEDIUM > LOW).
Apply `harness/confidence_routing.md` for display detail level.

### 5c. Present Priority Sections

Display results following `config/output_format.md` Mode 1 format:

```markdown
## 논문 전체 검토

### 구조 맵
[Section] → [Section] → ... (연결 품질 표시)

### 리뷰어 토론 결과
[합의/발견/충돌 항목들]

### 우선 수정 섹션
1. [섹션명] — [이유]
2. [섹션명] — [이유]

---
*"[섹션] 검토" / "다른 섹션" / "종료"*
```

---

## 6. Mode 2: Section — Section Review

**Entry:** User says "[섹션] 검토", "Discussion 교정", etc.
Or drill-down from Mode 1.

**Context loading:** Follow `harness/context_loading.md` Mode 2 rules.
- Read full section text
- Read section-specific writing-manual file (from INDEX.md routing)
- Read `cross_section/cohesion_flow.md` + `cross_section/stance_hedging.md`
- Match knowledge_index to section keywords
- Full-read matched knowledge files (Phase 2 load if not yet loaded)

### 6a. Run Reviewers in Parallel

Launch R1-R4 via SendMessage with:
- `{mode}` = `section`
- `{target_text}` = full section text
- `{section_name}` = section name
- `{allocated_knowledge}` = per distribution (re-matched to section keywords)
- `{writing_manual_content}` = section file + cross-section files

Mode-specific focus: STRUCTURE primary (paragraph arrangement),
LOGIC + HEDGING secondary.

### 6b. Deliberation

Same protocol as Mode 1. Classify results into consensus/unique/conflict.
Focus on paragraph-level issues.

### 6c. Present Results

Display following `config/output_format.md` Mode 2 format:

```markdown
## [섹션명] — 단락 [N]개

| # | 첫 문장 요약 | 역할 |
|---|---|---|
| 1 | [...] | [도입/근거/해석/전환 등] |

### 리뷰어 토론 결과
[합의/발견/충돌 항목들]

---
*"단락 [N] 검토" / "다음 섹션" / "전체 보기"*
```

---

## 7. Mode 3: Paragraph — Paragraph + Sentence Review

**Entry:** User says "단락 [N] 검토", "이 단락 봐줘", or drills down from Mode 2.

**Context loading:** Follow `harness/context_loading.md` Mode 3 rules.
- Target paragraph + prev/next paragraph for context
- All cross-section files (including `sentence_craft.md`, `advanced_nns_issues.md`)
- Knowledge narrowed to paragraph-relevant entries

### 7a. Intent Confirmation

Display the paragraph with its Korean translation. Present the orchestrator's
interpretation of the paragraph's intent:

```markdown
### 단락 [N]

**[EN]** `[paragraph text]`
**[KR]** `[번역]`

### 의도 확인
이 단락의 의도를 이렇게 파악했습니다:

**핵심 메시지:** [요약]
**섹션 내 역할:** [기능]
**핵심 주장:** [중심 주장]

맞나요? 다르면 말씀해주세요.
```

Wait for user confirmation or correction. Store as `{confirmed_intent}`.

### 7b. Paragraph-Level Review by R1-R4

Launch R1-R4 in parallel with:
- `{mode}` = `paragraph`
- `{target_text}` = paragraph (with surrounding context)
- `{confirmed_intent}` = user-confirmed intent
- Focus: does the paragraph deliver the confirmed intent?

Run deliberation on paragraph-level results.
Present consensus/unique/conflict items about the paragraph as a whole.

### 7c. Sentence-by-Sentence Review

Split the paragraph into sentences (follow sentence splitting rules
from `config/session_management.md`).

For each sentence:

1. Display the sentence with context:
   ```markdown
   ### 단락 [N] — 문장 [M/전체]
   *이전:* [이전 문장]
   **[EN]** `[current sentence]`
   **[KR]** `[번역]`
   ```

2. Launch R1-R4 in parallel via SendMessage.
   Each reviewer gets the sentence + paragraph context + confirmed intent
   + their allocated knowledge + writing-manual content.
   All six criteria checked at sentence level.

3. Collect results. Run deliberation protocol.
   Classify into consensus/unique/conflict.

4. Present results with confidence-appropriate detail
   (see `harness/confidence_routing.md`).

5. Show action options:
   ```markdown
   *"적용" / "수정안 A" / "수정안 B" / "다음" / "건너뛰기" / "검색해봐"*
   ```

6. Wait for user decision. Record in session state.

7. Advance to next sentence. Repeat.

### 7d. Post-Paragraph: Agent B Reference Verification

After all sentences in the paragraph are reviewed, automatically run
Agent B following `agents/agent_b.md`:

1. Collect all citations from the paragraph
2. Check `knowledge_bank.sources[]` first — auto-FOUND for matches
3. Check `session.ref_cache` — use cached results
4. Web search remaining unverified citations
5. Cache all results in `session.ref_cache`
6. Present reference verification table

### 7e. Paragraph Completion Summary

```markdown
---
### 단락 [N] 검토 완료
- 수정: [X]건 (합의 [a], 발견 [b])
- 승인: [Y]건
- 건너뛰기: [Z]건

#### 레퍼런스 확인
| REF | 상태 | 제목 | DOI |
|---|---|---|---|
| Author (Year) | 확인됨 / 미확인 | [...] | [...] |

---
*"다음 단락" / "이 단락 다시" / "섹션으로"*
```

Advance to next paragraph or follow user navigation.

---

## 8. Session Summary

When the user ends the session ("오늘 여기까지", "종료", "done"),
display the session summary following `config/output_format.md`:

```markdown
---
## 교정 세션 요약

| 항목 | 값 |
|---|---|
| 검토 모드 | [사용된 모드 목록] |
| 검토 단위 | 섹션 [N]개, 단락 [N]개, 문장 [N]개 |
| 리뷰어 수 | [N]명 |
| 합의 이슈 | [N]건 |
| 고유 발견 | [N]건 |
| 의견 충돌 | [N]건 |
| 수정 적용 | [N]건 |
| 승인 (무수정) | [N]건 |
| 건너뛰기 | [N]건 |
| 레퍼런스 미확인 | [N]건 |
| 추가 검색 실행 | [N]건 |

### 수정 이력

| # | 원문 | 수정문 | 근거 |
|---|---|---|---|
| 1 | [original] | [revised] | [합의/R1/R2/...] |

---
```

Offer session save if not already saved.

---

## 9. Reference Files

For implementation details beyond this orchestrator, read these files:

| Topic | File |
|---|---|
| Knowledge discovery & parsing | `agents/agent_e.md`, `knowledge/input_handler.md` |
| Review prompt template | `agents/agent_reviewer.md` |
| Reference verification | `agents/agent_b.md` |
| Knowledge distribution rules | `knowledge/distribution_strategy.md` |
| Knowledge bank schema | `knowledge/knowledge_bank_schema.md` |
| Web search strategy | `knowledge/search_strategy.md` |
| Deliberation protocol | `harness/deliberation.md` |
| Confidence-based routing | `harness/confidence_routing.md` |
| Context loading per mode | `harness/context_loading.md` |
| Mode switching & navigation | `config/navigation.md` |
| Output formatting rules | `config/output_format.md` |
| Session state & error handling | `config/session_management.md` |
| Writing-manual routing | `writing-manual/INDEX.md` |
