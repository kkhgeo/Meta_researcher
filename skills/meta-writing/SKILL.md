---
name: meta-writing
description: |
  다중 소스(Knowledge 마크다운, PDF, Web 검색) 기반 학술 논문 섹션 작성.
  5회 루프로 지식 탐색, 부족 시 Web 검색 보완.
  영어+한국어 이중 출력, IMRaD 섹션별 글쓰기 지원.
  사용 시점: "글쓰기", "섹션 작성", "선행연구 정리" 요청 시.
allowed-tools: [Read, Write, Edit, Task, WebSearch, WebFetch]
---

# Meta Writing Skill

## 개요

다중 소스를 활용하여 학술 논문의 각 섹션을 작성한다.

**지식 소스 (우선순위):**
1. Knowledge 폴더 (마크다운) - 이미 추출된 지식
2. 지정 폴더의 PDF - 원본 논문 직접 읽기
3. Web 검색 - 부족한 정보 보완

## 사용 트리거

다음 요청 시 이 스킬 자동 활용:
- "Introduction 작성해줘"
- "선행연구 정리해줘"
- "Discussion 작성해줘"
- "Knowledge 기반으로 글써줘"
- "PDF도 참고해서 작성해줘"
- "최신 연구도 검색해서 포함해줘"

---

## 입력 요구사항

### 필수 입력
1. **섹션 또는 요청 유형**
2. **지식 소스** (최소 1개):
   - Knowledge 폴더 경로, 또는
   - PDF 폴더 경로, 또는
   - Web 검색 허용 여부

### 선택 입력
- 특정 주제/키워드
- 단락 수 (기본: 2개)
- 최소 인용 수 (기본: 5개)
- 그림/표 파일 (해석 요청 시)

### 입력 예시
```
> "Knowledge_동위원소와 papers 폴더의 PDF를 참고해서 
    Introduction 선행연구 부분 작성해줘. 
    부족하면 웹 검색도 해줘."
```

---

## 지식 소스 설명

### 1순위: Knowledge 폴더 (마크다운)

```
Knowledge_동위원소/
├── index.md              ← 논문 목록
├── Chen2024.md           ← 이미 추출된 지식
├── Kim2023.md
└── Park2022.md
```

**장점**: 이미 구조화됨, 빠른 탐색, 인용 정보 완비
**사용 시점**: 항상 1순위로 탐색

### 2순위: PDF 폴더

```
papers/
├── Chen2024.pdf          ← 원본 논문
├── Kim2023.pdf
└── new_paper.pdf         ← Knowledge에 없는 논문
```

**장점**: Knowledge에 없는 논문 활용 가능
**사용 시점**: Knowledge에서 부족할 때, 또는 사용자가 명시적 요청

### 3순위: Web 검색

**장점**: 최신 연구, Knowledge/PDF에 없는 정보 보완
**사용 시점**: 
- 지식 갭 발생 시
- 최신 정보 필요 시
- 사용자가 명시적 요청

---

## Phase 1: 입력 파싱

사용자 요청을 분석하여 다음 항목 결정:

### 1.1 섹션 확인
| 섹션 | 포함 내용 |
|------|----------|
| Introduction | 연구 배경, 선행연구, 연구 갭, 연구 목적 |
| Methods | 연구 방법, 분석 기법, 시료 정보 |
| Results | 데이터 해석, 선행연구와 비교 |
| Discussion | 학술적 논의, 의미 해석, 한계점, 향후 연구 |

### 1.2 범위 확인
- **전체 섹션**: 섹션 전체 작성
- **특정 부분**: 선행연구만, 연구 갭만, 특정 주제만

### 1.3 소스 확인
- Knowledge 폴더 경로
- PDF 폴더 경로
- Web 검색 허용 여부 (기본: 부족 시 허용)

### 1.4 파싱 결과 템플릿
```
<task_spec>
Core topic: [주제]
Section: [Introduction/Methods/Results/Discussion]
Scope: [전체/특정 부분]
Request type: [주제/그림/표]

Knowledge folder: [경로 또는 없음]
PDF folder: [경로 또는 없음]
Web search: [allowed/not allowed]

Minimum citations: [숫자]
Number of paragraphs: [1-3]
Length per paragraph: [150-250 words]
Citation style: APA 7
</task_spec>
```

---

## Phase 2: 지식 탐색 (5회 루프)

### 루프 구조

```
┌─────────────────────────────────────────────────────────────────────┐
│                                                                     │
│  루프 1: 소스 스캔 및 계획                                          │
│  ├── Knowledge 폴더 index.md 확인 (있으면)                          │
│  ├── PDF 폴더 파일 목록 확인 (있으면)                               │
│  ├── 주제와 관련된 파일 선별                                        │
│  └── 출력: 탐색 계획 (어떤 파일을 어떤 순서로)                      │
│                                                                     │
│  루프 2: Knowledge 파일 읽기                                        │
│  ├── 선별된 Knowledge 마크다운 파일 읽기 (최대 5개)                 │
│  ├── 관련 Knowledge Claim 추출                                      │
│  └── 출력: 중간 결과 A                                              │
│                                                                     │
│  루프 3: Knowledge 추가 + PDF 읽기                                  │
│  ├── 추가 Knowledge 파일 읽기 (있으면)                              │
│  ├── Knowledge에 없는 정보 → PDF 직접 읽기                          │
│  └── 출력: 중간 결과 B                                              │
│                                                                     │
│  루프 4: 갭 체크 + Web 검색                                         │
│  ├── 현재까지 수집된 지식 평가                                      │
│  ├── 부족한 정보 식별:                                              │
│  │   - 인용 수 부족?                                                │
│  │   - 특정 주제 커버 안 됨?                                        │
│  │   - 최신 연구 필요?                                              │
│  ├── Web 검색으로 보완 (허용된 경우)                                │
│  └── 출력: 중간 결과 C + 갭 보고서                                  │
│                                                                     │
│  루프 5: 종합 → 글쓰기                                              │
│  ├── 중간 결과 A + B + C 병합                                       │
│  ├── 소스별 구분 유지                                               │
│  ├── 시간순/지역순 정렬                                             │
│  └── 최종 글쓰기                                                    │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### 소스 우선순위 로직

```
IF Knowledge 폴더 있음:
    → 1순위로 탐색
    → 충분하면 여기서 종료

IF Knowledge 부족 AND PDF 폴더 있음:
    → PDF 직접 읽기로 보완

IF 여전히 부족 AND Web 검색 허용:
    → Web 검색으로 보완
    → 검색 쿼리: 주제 + 키워드 기반
```

### 루프 4: 갭 체크 기준

| 갭 유형 | 판단 기준 | 대응 |
|---------|----------|------|
| 인용 수 부족 | 최소 인용 수 미달 | PDF/Web 추가 탐색 |
| 주제 커버 부족 | 핵심 키워드 관련 정보 없음 | Web 검색 |
| 최신 정보 필요 | 2023년 이후 연구 없음 | Web 검색 |
| 지역 데이터 부족 | 비교할 로컬 데이터 없음 | Web 검색 |

---

## Phase 3: 글쓰기

### 3.1 소스별 인용 표기

```markdown
## 인용 형식

Knowledge 기반:
(Chen et al., 2024)

PDF 직접 읽기:
(Kim et al., 2023)*
*PDF에서 직접 추출

Web 검색:
(Park et al., 2025)†
†Retrieved from [URL], accessed [날짜]
```

### 3.2 References 섹션 구분

```markdown
## References

### From Knowledge Base
Chen, A., et al. (2024). Title... *GCA*, ...
Kim, B., et al. (2023). Title... *EPSL*, ...

### From PDF (Direct Reading)
Lee, C., et al. (2022). Title... *ES&T*, ...

### From Web Search
Park, D., et al. (2025). Title... Retrieved from [URL]
```

### 3.3 논리 구조

| 섹션 | 구조 |
|------|------|
| Introduction | Problem/Gap → Prior work → New angle → Research purpose |
| Methods | Approach justification → Procedure → Validation |
| Results | Data presentation → Pattern identification → Comparison |
| Discussion | Interpretation → Implications → Limitations → Future work |

### 3.4 단락 작성 규칙

1. **Topic sentence**: 중심 주장으로 시작
2. **Evidence development**: 최소 3개 인용으로 뒷받침
3. **Transitions**: 자연스러운 연결어 사용
4. **Concluding sentence**: 함의 또는 다음 단락 연결
5. **소스 다양성**: 가능하면 다양한 소스 혼합

---

## Phase 4: 레퍼런스 검증

글쓰기 완료 후, 모든 인용과 참고문헌의 정확성을 검증한다.

### 4.1 검증 절차

```
┌─────────────────────────────────────────────────────────────────────┐
│                                                                     │
│  Step 1: 인용-참고문헌 매칭 검증                                    │
│  ├── 본문 내 모든 인용 추출: (Author, Year) 패턴                   │
│  ├── References 섹션의 모든 항목 추출                               │
│  ├── 매칭 확인:                                                     │
│  │   ✓ 본문 인용 → References에 존재?                              │
│  │   ✓ References 항목 → 본문에서 인용됨?                          │
│  └── 불일치 항목 리스트 생성                                        │
│                                                                     │
│  Step 2: 참고문헌 형식 검증                                         │
│  ├── APA 7 형식 준수 여부 확인                                      │
│  │   - 저자명 형식: Last, F. M.                                     │
│  │   - 연도 위치: (Year).                                           │
│  │   - 제목 이탤릭: 저널명                                          │
│  │   - DOI 형식: https://doi.org/...                                │
│  ├── 필수 필드 확인:                                                │
│  │   - Authors, Year, Title, Journal, Volume, Pages, DOI            │
│  └── 누락 필드 표기: [missing: field]                               │
│                                                                     │
│  Step 3: 소스별 검증                                                │
│  ├── Knowledge 기반: 원본 마크다운과 대조                           │
│  ├── PDF 기반: 논문 메타데이터 재확인                               │
│  └── Web 검색: URL 접근 가능 여부, 접근 날짜 표기                   │
│                                                                     │
│  Step 4: 검증 보고서 생성                                           │
│  ├── 총 인용 수                                                     │
│  ├── 검증 통과/실패 항목                                            │
│  ├── 수정 필요 항목                                                 │
│  └── 최종 품질 점수                                                 │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### 4.2 검증 체크리스트

| 검증 항목 | 기준 | 통과 조건 |
|-----------|------|-----------|
| 인용-참고문헌 일치 | 모든 인용이 References에 존재 | 100% 매칭 |
| 고아 참고문헌 없음 | References 항목이 본문에서 인용됨 | 미인용 항목 0개 |
| APA 7 형식 | 저자, 연도, 제목, 저널, DOI 형식 | 형식 오류 0개 |
| 필수 필드 완비 | 모든 필수 메타데이터 존재 | [missing] 태그 0개 |
| Web 소스 검증 | URL 유효, 접근 날짜 표기 | 모든 Web 소스 검증됨 |

### 4.3 검증 보고서 템플릿

```markdown
## Reference Verification Report (레퍼런스 검증 보고서)

### Summary (요약)
- **총 본문 인용 수**: N개
- **총 참고문헌 수**: N개
- **매칭 성공**: N개 (100%)
- **검증 상태**: ✅ PASS / ⚠️ ISSUES FOUND

### Citation-Reference Matching (인용-참고문헌 매칭)

| 상태 | 인용 | 참고문헌 |
|------|------|----------|
| ✅ | (Chen et al., 2024) | Chen, A., et al. (2024). Title... |
| ✅ | (Kim et al., 2023) | Kim, B., et al. (2023). Title... |
| ❌ Missing | (Park et al., 2022) | [NOT FOUND IN REFERENCES] |
| ⚠️ Orphan | - | Lee, C., et al. (2021). [NOT CITED] |

### Format Validation (형식 검증)

| 참고문헌 | APA 7 형식 | 누락 필드 |
|----------|-----------|-----------|
| Chen et al. (2024) | ✅ Valid | - |
| Kim et al. (2023) | ⚠️ Issue | [missing: DOI] |

### Source Verification (소스별 검증)

| 소스 유형 | 항목 수 | 검증됨 | 미검증 |
|-----------|---------|--------|--------|
| Knowledge | 5 | 5 | 0 |
| PDF | 2 | 2 | 0 |
| Web | 1 | 1 | 0 |

### Issues to Fix (수정 필요 항목)
1. ❌ (Park et al., 2022) - References 섹션에 추가 필요
2. ⚠️ Kim et al. (2023) - DOI 추가 필요
3. ⚠️ Lee et al. (2021) - 본문에서 인용하거나 References에서 삭제

### Quality Score (품질 점수)
**[8/10]** - 2개 항목 수정 필요
```

### 4.4 자동 수정

검증 후 발견된 문제 자동 수정:

1. **누락된 참고문헌**: 소스에서 메타데이터 재추출하여 추가
2. **고아 참고문헌**: 사용자에게 삭제 또는 인용 추가 제안
3. **형식 오류**: APA 7 형식으로 자동 재포맷
4. **누락 필드**: 원본 소스에서 재확인 후 보완

---

## 출력 형식

### A) Approach checklist (접근법 체크리스트)

**English:**
- [1] Parse request → identify section, scope, sources
- [2] Loop 1: Scan Knowledge index + PDF folder
- [3] Loop 2: Read Knowledge files
- [4] Loop 3: Read additional Knowledge + PDF if needed
- [5] Loop 4: Gap check → Web search if needed
- [6] Loop 5: Synthesize all sources → write
- [7] Generate references by source type
- [8] **Reference verification → validate all citations**
- [9] Self-audit quality

**한국어:**
- [1] 요청 파싱 → 섹션, 범위, 소스 식별
- [2] 루프 1: Knowledge index + PDF 폴더 스캔
- [3] 루프 2: Knowledge 파일 읽기
- [4] 루프 3: 추가 Knowledge + 필요시 PDF 읽기
- [5] 루프 4: 갭 체크 → 필요시 Web 검색
- [6] 루프 5: 모든 소스 종합 → 글쓰기
- [7] 소스 유형별 참고문헌 생성
- [8] **레퍼런스 검증 → 모든 인용 유효성 확인**
- [9] 품질 자기 검증

---

### B) Source Summary (소스 요약)

```markdown
## 사용된 소스

| 소스 유형 | 파일/쿼리 | 추출 항목 수 |
|-----------|----------|-------------|
| Knowledge | Chen2024.md, Kim2023.md | 8 claims |
| PDF | Lee2022.pdf | 3 claims |
| Web Search | "groundwater isotope Korea 2024" | 2 claims |

**갭 보고:**
- [해결됨] 최소 인용 수 충족 (5개 이상)
- [해결됨] 한국 지하수 데이터 포함
- [Web으로 보완] 2024년 최신 연구 추가
```

---

### C) Main text (본문)

#### Paragraph 1

**[English]**
[Topic sentence with central claim]. [Evidence from Knowledge 
(Author1, Year)]. Furthermore, [evidence from PDF (Author2, Year)*]. 
Recent studies have also shown [evidence from Web (Author3, Year)†]. 
[Concluding sentence with implications].

**[한국어]**
[중심 주장의 주제문]. [Knowledge 기반 증거 (Author1, Year)]. 
또한, [PDF 기반 증거 (Author2, Year)*]. 최근 연구들은 또한 
[Web 검색 증거 (Author3, Year)†]를 보여주었다. 
[함의를 담은 결론문].

---

### D) References (APA 7)

**From Knowledge Base:**
Chen, A., et al. (2024). Title... *GCA*, ...
Kim, B., et al. (2023). Title... *EPSL*, ...

**From PDF (Direct Reading):**
*Lee, C., et al. (2022). Title... *ES&T*, ...

**From Web Search:**
†Park, D., et al. (2025). Title... Retrieved from [URL], accessed [날짜].

---

### E) Self-assessment checklist (자기 평가)

**English:**
- [ ] Topic sentence clarity
- [ ] Minimum citation count met (≥N)
- [ ] Source diversity (multiple source types used)
- [ ] Temporal/geographic ordering logic
- [ ] Natural, logical transitions
- [ ] Concluding sentence with broader implications
- [ ] APA metadata accuracy
- [ ] All sources clearly marked by type
- [ ] Web search results verified for reliability
- [ ] **All in-text citations have matching references**
- [ ] **No orphan references (all refs cited in text)**
- [ ] **Reference format validated (APA 7)**
- [ ] **No missing fields in references**

**한국어:**
- [ ] 주제문 명확성
- [ ] 최소 인용 수 충족 (≥N개)
- [ ] 소스 다양성 (다중 소스 유형 사용)
- [ ] 시간·지역 배열의 논리성
- [ ] 자연스럽고 논리적인 전환
- [ ] 더 넓은 함의를 제시하는 결론문
- [ ] APA 서지 메타데이터 정확성
- [ ] 모든 소스 유형별 명확히 표기
- [ ] Web 검색 결과 신뢰성 확인
- [ ] **모든 본문 인용이 참고문헌에 존재**
- [ ] **고아 참고문헌 없음 (모든 참고문헌이 본문에서 인용됨)**
- [ ] **참고문헌 형식 검증됨 (APA 7)**
- [ ] **참고문헌에 누락 필드 없음**

---

### F) Reference Verification Report (레퍼런스 검증 보고서)

*Phase 4 검증 결과를 여기에 포함*

```markdown
## 검증 결과 요약
- 총 인용: N개 | 총 참고문헌: N개
- 매칭: ✅ 100% | 형식: ✅ Valid
- 품질 점수: [X/10]

## 수정 항목 (있는 경우)
1. [항목]
2. [항목]
```

---

## 제약 조건

### 소스 우선순위
1. Knowledge 폴더 (가장 신뢰)
2. PDF 직접 읽기 (신뢰)
3. Web 검색 (보완용, 신뢰성 확인 필요)

### 인용 엄격성
- DOI/URL/연도/저자 조작 금지
- 불명확한 필드: [missing: field]로 표기
- Web 검색 결과는 출처 명시 필수

### Web 검색 가이드라인
- 학술 소스 우선 (Google Scholar, 저널 사이트)
- 검색 쿼리 기록
- 접근 날짜 표기
- 신뢰할 수 없는 소스 제외

### 품질 기준
- 단락당 최소 3개 인용
- 단일 소스 과의존 금지
- 가능하면 다양한 소스 유형 혼합

---

## 사용 예시

### 예시 1: Knowledge만 사용
```
> "Knowledge_동위원소에서 Introduction 선행연구 부분 작성해줘."
```

### 예시 2: Knowledge + PDF
```
> "Knowledge_동위원소와 papers 폴더의 PDF를 참고해서 
    Methods 섹션 작성해줘."
```

### 예시 3: 전체 소스 활용
```
> "Knowledge_환경모니터링과 papers 폴더를 기반으로 
    Discussion 작성해줘. 최신 연구가 부족하면 웹 검색도 해줘."
```

### 예시 4: Web 검색 우선
```
> "2024년 이후 최신 지하수 동위원소 연구를 웹에서 검색해서 
    Introduction에 추가해줘."
```

### 예시 5: 그림 해석
```
> "이 Figure 1을 Knowledge_동위원소와 papers 폴더 기반으로 해석해줘.
    필요하면 추가 검색도 해줘."
```

---

## 오류 처리

| 상황 | 대응 |
|------|------|
| 모든 소스 없음 | 최소 1개 소스 요청 |
| Knowledge 폴더 없음 | PDF 또는 Web으로 대체 가능 |
| PDF 읽기 실패 | 오류 보고 + 다른 소스로 대체 |
| Web 검색 불허 + 지식 부족 | "Insufficient evidence" 명시 |
| 신뢰할 수 없는 Web 소스 | 제외 + 다른 소스 검색 |

---

## 지구화학 특화

### Web 검색 키워드 예시
```
"groundwater stable isotope Korea"
"δ18O δ2H precipitation Asia"
"Sr isotope contamination agricultural"
"MC-ICP-MS groundwater analysis method"
```

### 신뢰할 수 있는 소스
- Google Scholar
- ScienceDirect
- Wiley Online Library
- SpringerLink
- AGU Publications
- GSA Publications

---

**Extracted by**: Meta_researcher / meta-writing
**Version**: 0.2.0
