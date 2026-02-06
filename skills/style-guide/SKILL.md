---
name: style-guide
description: |
  참고 논문에서 IMRaD 섹션별 문장/어휘/표현 패턴을 최대한 추출하여 데이터뱅크 구축(Mode A),
  사용자 초고를 데이터뱅크 기반으로 스타일 교정(Mode B).
  사용 시점: "스타일 추출", "톤 분석", "문체 교정", "스타일 맞춰줘" 요청 시.
  **반드시 references/extraction_template.md 또는 revision_guide.md를 먼저 읽을 것!**
allowed-tools: [Read, Write, Edit, Bash, Task, Glob, Grep]
---

> **필수 사항**: 이 스킬은 두 가지 모드로 동작합니다.
> - **Mode A (추출)**: `references/extraction_template.md`를 먼저 읽을 것
> - **Mode B (교정)**: `references/revision_guide.md`를 먼저 읽을 것

# Style Guide Skill

## 개요

학술 논문의 글쓰기 스타일을 **섹션별로 추출**하고, 추출된 데이터를 기반으로 **사용자 초고를 교정**하는 스킬.

**핵심 원칙: 점수 매기기가 아니라, 실제 문장·어휘·표현을 최대한 많이 수집한다.**

### 2-Mode 구조

```
Mode A: 스타일 추출 (Style Extraction)
  참고 논문 → IMRaD 섹션별 데이터뱅크 구축 → Style_{주제}/ 에 저장

Mode B: 스타일 교정 (Style Revision)
  사용자 초고 + 데이터뱅크 → 섹션별 스타일 매칭 → 교정된 텍스트 출력
```

---

## 사용 트리거

### Mode A (추출)
- "이 논문 스타일 추출해줘"
- "톤 분석해줘"
- "스타일 데이터뱅크 만들어줘"
- "문체 패턴 추출해줘"
- "Science Advances 스타일 분석해줘"

### Mode B (교정)
- "이 글 스타일 맞춰줘"
- "문체 교정해줘"
- "스타일 데이터뱅크 기반으로 수정해줘"
- "Introduction 톤 맞춰줘"
- "이 단락 고져줘" (+ 데이터뱅크 경로)

---

## Mode A: 스타일 추출

### 입력 요구사항

**필수:**
1. 참고 논문 텍스트 (PDF 또는 텍스트)
2. 텍스트는 **섹션 태깅** 필수: `[INTRO]`, `[METHODS]`, `[RESULTS]`, `[DISCUSSION]`

**선택:**
- 저널명, 분야
- 특정 관심 카테고리 (예: hedging 위주, 통계 보고 위주)
- 모드: Detailed (기본) / Lite

### 작업 흐름

**반드시 `references/extraction_template.md`를 먼저 읽고 따를 것!**

```
Phase 0: 템플릿 로드
  → references/extraction_template.md 읽기

Phase 1: 입력 검증 (Sanity Check)
  → 섹션 태그 존재 여부
  → 텍스트 길이 검증
  → 경고 처리

Phase 2: 섹션 분류 & 개요
  → 각 섹션별 시제/voice/person/목적 파악

Phase 3: 섹션별 추출 루프 (핵심)
  → Introduction / Methods / Results / Discussion 각각에 대해:
    3a. Sentence Patterns (전수 수집)
    3b. Vocabulary (특징적 표현 전수 수집)
    3c. Transitions & Connectors (전수 수집)
    3d. Hedging & Stance Markers (전수 수집)
    3e. Quantitative Expressions (전수 수집)
    3f. Citation Integration Patterns (전수 수집)

Phase 4: 섹션 간 비교
  → 시제/voice/hedging 밀도/인용 밀도 변화 매트릭스

Phase 5: 스타일 규칙 도출
  → 섹션별 Do/Don't
  → 섹션별 Sentence Templates
  → Substitution Dictionary

Phase 6: 저장
  → Style_{주제}/ 폴더에 저장
  → JSON 데이터뱅크 포함
```

### 출력 구조 (Mode A)

```
Style_{주제}/
├── index.md                    # 분석 논문 목록
├── {저자}{연도}_style.md       # 개별 논문 스타일 데이터뱅크
├── {저자}{연도}_style.json     # JSON 데이터뱅크 (LLM 참조용)
└── cross_section_matrix.md     # 섹션 간 비교 매트릭스
```

### 개별 데이터뱅크 파일 구조

```markdown
# Style Data Bank: {저자} et al. ({연도})

## A. 논문 정보
- **Title**:
- **Journal**:
- **Year**:
- **Field**:

## B. 섹션 개요
| Feature | Introduction | Methods | Results | Discussion |
|---------|-------------|---------|---------|------------|
| Tense | | | | |
| Voice | | | | |
| Person | | | | |
| Hedging density | | | | |

## C. Introduction Data Bank
### Sentence Patterns
| # | Sentence | Length | Structure | Tense | Voice | Function | Source |
### Vocabulary
| Term | POS | Usage Context | Full Sentence | Source |
### Transitions
| Transition | Category | Full Sentence | Position | Source |
### Hedging & Stance
| Expression | Type | Strength | Full Sentence | Source |
### Quantitative Expressions
| Expression | Type | Full Sentence | Source |
### Citation Patterns
| Pattern | Type | Full Sentence | Source |

## D. Methods Data Bank
[동일 구조 반복]

## E. Results Data Bank
[동일 구조 반복]

## F. Discussion Data Bank
[동일 구조 반복]

## G. Style Rules
### Do / Don't (섹션별)
### Sentence Templates (섹션별 5개 이상)
### Substitution Dictionary

---
**Extracted by**: Meta_researcher / style-guide (Mode A)
**Last Updated**: {날짜}
```

---

## Mode B: 스타일 교정

### 입력 요구사항

**필수:**
1. 교정할 텍스트 (섹션, 단락, 또는 문장)
2. 해당 텍스트의 **섹션 유형** (Introduction / Methods / Results / Discussion)
3. 참조할 **Style 데이터뱅크 경로** (Style_{주제}/ 폴더)

**선택:**
- 교정 강도: Light (표현만) / Standard (구조+표현) / Deep (전면 재작성)
- 특정 카테고리 집중: hedging, transitions, sentence structure 등

### 작업 흐름

**반드시 `references/revision_guide.md`를 먼저 읽고 따를 것!**

```
Phase 0: 템플릿 로드
  → references/revision_guide.md 읽기

Phase 1: 입력 분석
  → 사용자 텍스트의 섹션 유형 확인
  → 데이터뱅크 로드 (해당 섹션)
  → 교정 강도 확인

Phase 2: 진단 (Diagnosis)
  → 사용자 텍스트를 데이터뱅크와 비교:
    2a. Sentence Structure 비교
    2b. Vocabulary 비교
    2c. Transitions 비교
    2d. Hedging/Stance 비교
    2e. Quantitative Reporting 비교
    2f. Citation Style 비교
  → 각 카테고리별 불일치 항목 리스트

Phase 3: 처방 (Prescription)
  → 불일치 항목별 구체적 수정 제안
  → 데이터뱅크에서 참조 예시 인용
  → 수정 근거 설명

Phase 4: 교정 실행
  → 교정된 텍스트 생성 (영어)
  → 변경 사항 하이라이트
  → Before/After 비교

Phase 5: 검증
  → 교정 후 텍스트가 데이터뱅크 패턴과 일치하는지 재확인
  → 교정 리포트 생성
```

### 출력 형식 (Mode B)

```markdown
## Style Revision Report

### A. 진단 요약
| 카테고리 | 일치도 | 주요 불일치 | 수정 필요 |
|----------|--------|------------|----------|
| Sentence Structure | 60% | 문장 길이 짧음 | 3곳 |
| Vocabulary | 70% | 비학술적 동사 사용 | 5곳 |
| Transitions | 40% | 전환어 부족 | 4곳 |
| Hedging | 50% | 과도한 단정 | 6곳 |

### B. 상세 진단 & 처방

#### [수정 1] Sentence Structure
- **원문**: "We tested the samples."
- **문제**: 너무 짧고 단순. Methods 섹션 패턴과 불일치.
- **참조**: "Samples were analyzed using [method] following [protocol] (ref)." [EX#1-METHODS]
- **수정**: "The collected samples were analyzed using ICP-MS following the protocol established by Smith et al. (2020)."

#### [수정 2] Hedging
- **원문**: "This proves that..."
- **문제**: Discussion에서 overclaim. 데이터뱅크 패턴은 hedging 사용.
- **참조**: "Our findings suggest that..." [EX#1-DISCUSSION]
- **수정**: "These results suggest that..."

[...모든 수정 항목...]

### C. 교정된 텍스트

**[Before]**
원문 텍스트

**[After]**
교정된 텍스트 (변경 부분 **볼드** 표시)

### D. 변경 요약
- 총 수정: N곳
- Sentence Structure: N곳
- Vocabulary: N곳
- Transitions: N곳
- Hedging: N곳
- 교정 후 스타일 일치도: X% → Y%

---
**Revised by**: Meta_researcher / style-guide (Mode B)
```

---

## 두 모드의 연계

```
┌──────────────────────────────────────────────────────────┐
│  Mode A: 참고 논문 → 스타일 추출 → Style_{주제}/ 저장   │
│                                        │                 │
│                                        ▼                 │
│  Mode B: 사용자 초고 + Style_{주제}/ → 스타일 교정       │
└──────────────────────────────────────────────────────────┘
```

**일반적인 워크플로우:**
1. 목표 저널의 논문 2~3편으로 Mode A 실행 → 데이터뱅크 구축
2. 자신의 초고 섹션별로 Mode B 실행 → 스타일 교정
3. 필요시 추가 논문으로 Mode A 보강 → Mode B 재실행

---

## 병렬 처리 (Subagent)

### Mode A 병렬
여러 논문 동시 추출:
```
사용자: "이 3개 논문 스타일 추출해줘"
→ 각 논문에 대해 Task (Subagent) 생성
→ 각 Subagent가 독립적으로 추출
→ Style_{주제}/ 폴더에 통합 저장
```

### Mode B 병렬
여러 섹션 동시 교정:
```
사용자: "Introduction과 Discussion 교정해줘"
→ 각 섹션에 대해 Task (Subagent) 생성
→ 각 Subagent가 해당 섹션 데이터뱅크 참조하여 교정
```

---

## 품질 기준

### Mode A
1. **완전성**: 모든 특징적 표현 빠짐없이 수집
2. **정확성**: 원문 그대로 인용, 섹션 태그 정확
3. **구조성**: 6개 카테고리 × 4개 섹션 = 24개 테이블 모두 작성
4. **추적성**: 모든 예시에 `[EX#N-SECTION]` 소스 태그

### Mode B
1. **근거 기반**: 모든 수정에 데이터뱅크 참조 예시 첨부
2. **보존성**: 원문의 학술적 내용은 변경하지 않음 (스타일만 수정)
3. **설명성**: 왜 수정했는지 이유 명시
4. **비교성**: Before/After 명확히 대조

---

## 오류 처리

| 상황 | 대응 |
|------|------|
| 섹션 태그 없음 (Mode A) | 태깅 요청 또는 자동 분류 시도 + 경고 |
| 텍스트 너무 짧음 (<500w) | 경고: "추출 결과가 thin할 수 있음" |
| 데이터뱅크 없음 (Mode B) | Mode A 먼저 실행 안내 |
| 섹션 유형 불일치 | 올바른 섹션 확인 요청 |
| 참고 논문 1편만 제공 | 경고: "단일 논문 패턴, 일반화 제한적" |

---

## 사용 예시

### Mode A 예시
```
# 단일 논문 추출
> "이 논문 스타일 추출해서 Style_생태학에 저장해줘"

# 여러 논문 (병렬)
> "papers 폴더의 논문 3편 스타일 추출해줘"

# 특정 카테고리 집중
> "이 논문에서 hedging 패턴만 추출해줘"
```

### Mode B 예시
```
# 단락 교정
> "이 Introduction 단락을 Style_생태학 기반으로 교정해줘"

# 전체 섹션 교정
> "Discussion 전체를 Science Advances 스타일로 맞춰줘"

# 가벼운 교정
> "이 문장들만 톤 맞춰줘 (Light 모드)"
```

### 연계 사용
```
# Step 1: 추출
> "Weber2021.pdf 스타일 추출해서 Style_진화생물학에 저장해줘"

# Step 2: 교정
> "내가 쓴 Introduction을 Style_진화생물학 기반으로 교정해줘"
```

---

**Version**: 1.0.0
**Extracted by**: Meta_researcher / style-guide
