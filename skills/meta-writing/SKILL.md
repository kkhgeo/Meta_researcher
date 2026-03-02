---
name: meta-writing
description: |
  다중 소스 기반 학술 논문 섹션 작성 스킬.
  본 연구 데이터(My Data: 그림, 표, 데이터 파일)와 선행연구 지식(Knowledge, PDF, Web)을
  명확히 구분하여 IMRaD 섹션별 글쓰기를 수행한다.
  5-Loop 프로세스로 소스 탐색 → 내 데이터 분석 → 갭 보완 → 글쓰기 → 검증을 진행하며,
  영어+한국어 이중 출력과 APA 7 인용을 지원한다.
  사용 시점: "글쓰기", "섹션 작성", "선행연구 정리", "Results 써줘", "Discussion 작성",
  "이 그림 기반으로 써줘", "Knowledge 기반으로 글써줘", "Figure 해석해줘" 요청 시 반드시 사용.
allowed-tools: [Read, Write, Edit, Glob, Task, WebSearch, WebFetch]
---

# Meta Writing Skill

## 개요

본 연구 데이터(My Data)와 선행연구 지식(Knowledge Sources)을 결합하여
학술 논문 섹션을 작성한다.

**두 종류의 정보를 명확히 구분한다:**

| 구분 | My Data (본 연구) | Knowledge Sources (선행연구) |
|------|-------------------|---------------------------|
| 정체 | 내가 생산한 데이터 | 선행연구에서 가져온 지식 |
| 예시 | Figure, Table, CSV | Knowledge MD, PDF, Web |
| 글에서 역할 | 기술·해석 대상 | 비교·근거 |
| Results | "본 연구에서 ~을 보였다 (Figure 1)" | "(Chen et al., 2024)과 유사하다" |
| Discussion | "관찰된 패턴은 ~을 시사한다" | "Chen(2024)의 모델로 설명된다" |

**Knowledge Sources 우선순위:**
1. Knowledge 폴더 (마크다운) — 이미 구조화된 지식, 항상 1순위
2. PDF 폴더 — Knowledge에 없는 논문 보완
3. Web 검색 — 최신 정보 또는 갭 보완

---

## 프로젝트 설정 로드

작업 시작 전 현재 디렉토리에서 `writing.local.md`를 탐색한다.

- **있으면**: 설정 로드 후 사용자에게 요약 제시하고 확인 받는다.
- **없으면**: `writing.local.template.md`를 복사하여 `writing.local.md`를 생성하도록 안내한다. 사용자가 거부하면 소스 경로를 직접 질문한다.
- **사용자가 명시적으로 경로를 지정하면**: writing.local.md보다 우선한다.

---

## 입력 파싱 (Phase 1)

사용자 요청을 분석하여 아래 항목을 결정한다.
파싱 완료 후 사용자에게 확인을 받고 진행한다.

```xml
<task_spec>
Core topic: [주제]
Section: [Introduction/Methods/Results/Discussion]
Scope: [전체 섹션/특정 부분]
Focus: [관점/목표]
Request type: [주제/그림/표]

My Data:
  figures: [파일 경로 목록, 섹션 배치]
  tables: [파일 경로 목록, 섹션 배치]
  data_files: [파일 경로 목록]

Knowledge Sources:
  knowledge_folder: [경로 또는 "없음"]
  pdf_folder: [경로 또는 "없음"]
  web_search: [allowed/not allowed]

Settings:
  min_citations: [숫자, 기본 5]
  paragraphs: [1-3, 기본 2]
  words_per_paragraph: [150-250]
  citation_style: APA 7
  language: [bilingual/english/korean]
</task_spec>
```

### 섹션 확인

| 섹션 | 포함 내용 | My Data 역할 | Knowledge 역할 |
|------|----------|-------------|----------------|
| Introduction | 배경, 선행연구, 갭, 목적 | 거의 없음 | 주요 (선행연구 종합) |
| Methods | 방법, 기법, 시료 | 시료·기기 정보 | 방법론 참조·인용 |
| Results | 데이터 제시, 비교 | **주요** (기술 대상) | 비교 대상 |
| Discussion | 해석, 함의, 한계 | **주요** (해석 대상) | 해석 근거 |

---

## 지식 탐색 및 분석 (Phase 2)

5-Loop 프로세스로 소스를 탐색하고 분석한다.

→ **상세 절차**: `references/writing_template.md`의 Loop 1~4 섹션을 읽고 따른다.

### 루프 요약

```
Loop 1: 소스 스캔 및 계획
  - writing.local.md 로드 (있으면)
  - My Data 폴더 확인 (그림/표/데이터 목록)
  - Knowledge 폴더 확인 (index.md, 관련 파일 선별)
  - PDF 폴더 확인
  - 탐색 계획 수립

Loop 2: Knowledge 읽기
  - 선별된 Knowledge 마크다운 파일 읽기 (최대 5개)
  - Claim + Citation 쌍 추출
  - 중간 결과 A

Loop 3: 내 데이터 분석 + 추가 소스
  - My Data 그림/표 분석 (패턴, 수치 추출)
  - 내 데이터와 Knowledge 비교 쌍 생성
  - 추가 Knowledge/PDF 읽기
  - 중간 결과 B

Loop 4: 갭 체크 + Web 검색
  - 인용 수, 주제 커버, 최신 연구, 비교 데이터 확인
  - 부족하면 Web 검색으로 보완 (허용된 경우)
  - 중간 결과 C + 갭 보고서
```

### 갭 체크 기준

| 갭 유형 | 판단 기준 | 대응 |
|---------|----------|------|
| 인용 수 부족 | 최소 인용 수 미달 | PDF/Web 추가 탐색 |
| 비교 데이터 부족 | 내 데이터 패턴에 대응할 선행연구 없음 | Web 검색 |
| 최신 연구 부족 | 2023년 이후 연구 없음 | Web 검색 |
| 해석 근거 부족 | Discussion에서 인용할 이론/메커니즘 없음 | PDF/Web 검색 |

---

## 글쓰기 (Phase 3)

→ **5-Loop 상세**: `references/writing_template.md`의 Loop 5 섹션을 따른다.
→ **섹션별 구조·전환어·예시**: `references/section_guides.md`의 해당 섹션을 읽는다.

### 핵심 규칙

**My Data vs Knowledge 구분 원칙:**
- My Data는 인용 없이 직접 기술한다. "(Figure 1)", "(Table 2)" 형태로 참조.
- Knowledge Sources는 반드시 (Author, Year) 형태로 인용한다.
- 한 문장에 My Data와 Knowledge를 혼합할 때, 어느 것이 본 연구이고 어느 것이 선행연구인지 명확히 한다.

**단락 작성 규칙:**
1. Topic sentence: 중심 주장으로 시작
2. Evidence: 최소 3개 인용으로 뒷받침 (Knowledge Sources)
3. Transitions: 자연스러운 연결어 (section_guides.md 참조)
4. Concluding sentence: 함의 또는 다음 단락 연결
5. Source diversity: 가능하면 다양한 소스 유형 혼합

**이중 언어 출력:**
- 영어 먼저, 한국어 번역 뒤에
- 학술 용어 일관성 유지
- 한국어에서도 (Author, Year) 인용은 영어로 유지

---

## 검증 (Phase 4)

→ **상세 절차**: `references/citation-and-verification.md` 전체를 읽고 따른다.

### 검증 요약

```
Step 1: 인용-참고문헌 매칭 (본문 인용 ↔ References)
Step 2: APA 7 형식 검증 (필수 필드, 포맷)
Step 3: 소스별 검증 (Knowledge 원본 대조, PDF 메타데이터, Web URL)
Step 4: 검증 보고서 생성 (Quality Score 포함)
```

### 검증 체크 항목
- [ ] 모든 본문 인용이 References에 존재
- [ ] 고아 참고문헌 없음
- [ ] APA 7 형식 준수
- [ ] My Data 참조(Figure/Table)가 실제 파일과 일치
- [ ] DOI/URL/연도/저자 조작 없음

---

## 출력 형식

→ **상세 템플릿**: `references/writing_template.md`의 "출력 형식 상세" 섹션 참조.

모든 출력은 아래 6개 섹션으로 구성한다:

| 섹션 | 내용 |
|------|------|
| A) Approach Checklist | 수행 작업 3~8단계 요약 (영어 + 한국어) |
| B) Source Summary | 소스 유형별 요약 + 갭 보고 |
| C) Main Text | 영어 단락 + 한국어 번역 |
| D) References | APA 7, 소스 유형별 구분 |
| E) Self-Assessment | 품질 체크리스트 |
| F) Verification Report | 레퍼런스 검증 보고서 |

---

## 제약 조건

### 인용 엄격성
- DOI/URL/연도/저자 조작 절대 금지
- 불명확한 필드: `[missing: field]`로 표기
- Web 검색 결과는 출처·접근일 명시 필수

### 품질 기준
- 단락당 최소 3개 Knowledge 인용
- 단일 소스 과의존 금지
- 가능하면 다양한 소스 유형 혼합

### My Data 취급
- 내 데이터를 선행연구처럼 인용하지 않는다
- Figure/Table 번호를 정확히 유지한다
- 데이터 수치를 임의로 변경하지 않는다

---

## References 파일 안내

| 파일 | 참조 시점 | 내용 |
|------|----------|------|
| `references/writing_template.md` | Phase 2~3 | 5-Loop 상세 절차, 소스별 처리, 출력 형식 상세 |
| `references/section_guides.md` | Phase 3 | IMRaD 섹션별 구조, 전환어, 예시, 그림/표 해석 |
| `references/citation-and-verification.md` | Phase 4 | 인용 표기, APA 7, 검증 절차, 보고서 템플릿 |

---

**Version**: 0.2.0
