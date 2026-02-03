---
name: knowledge-extraction
description: |
  논문 PDF에서 핵심 지식을 추출하여 구조화된 마크다운으로 저장.
  5가지 인식론적 카테고리(이론, 실증, 방법론, 맥락, 비판)로 분류.
  Subagent 병렬 처리 지원. 사용 시점: "논문 분석", "지식 추출", 
  "Knowledge 저장" 요청 시 자동 호출.
allowed-tools: [Read, Write, Edit, Bash, Task]
---

# Knowledge Extraction Skill

## 개요

논문 PDF에서 인용 기반 지식 단위(Knowledge Units)를 추출하여 
주제별 Knowledge 폴더에 구조화된 마크다운으로 저장한다.

## 사용 트리거

다음 요청 시 이 스킬 자동 활용:
- "이 논문 분석해줘"
- "Knowledge 폴더에 저장해줘"
- "논문에서 지식 추출해줘"
- "papers 폴더의 PDF 처리해줘"

## 작업 흐름

```
1. 입력 확인
   - PDF 파일 경로
   - 대상 섹션 (미지정 시 전체 논문)
   - 저장할 Knowledge 폴더명

2. 논문 읽기
   - Claude가 PDF 직접 읽음
   - 섹션별 구조 파악

3. 지식 추출 (5가지 카테고리)
   - Theoretical Foundations (이론적 기반)
   - Empirical Precedents (실증적 선행연구)
   - Methodological Heritage (방법론적 유산)
   - Contextual Knowledge (맥락적 지식)
   - Critical Discourse (비판적 담론)

4. 마크다운 저장
   - Knowledge_{주제}/ 폴더에 저장
   - 파일명: {저자}{연도}.md (예: Chen2024.md)
   - index.md 자동 업데이트
```

## 출력 구조

### 폴더 구조
```
Knowledge_{주제}/
├── index.md              # 논문 목록 (자동 생성/업데이트)
├── Chen2024.md           # 개별 논문 지식
├── Kim2023.md
└── Park2022.md
```

### 개별 논문 파일 구조

```markdown
# {저자} et al. ({연도}) - {짧은 제목}

## A. 논문 정보
- **Title**: 
- **Authors**: 
- **Year**: 
- **Journal**: 
- **DOI**: 
- **Full Citation (APA 7th)**: 

## B. 연구 요약
### 연구 목적
[한국어로 작성]

### 주요 발견
1. [Primary finding]
2. [Secondary finding]

### 주요 기여
- **이론적 기여**: 
- **실증적 기여**: 
- **방법론적 기여**: 

## C. 지식 추출

### 1. THEORETICAL FOUNDATIONS (이론적 기반)
이 논문에서 인용한 핵심 이론, 개념적 프레임워크, 가설, 모델.

| Knowledge Claim | 한국어 번역 | Citation Context | Reference (APA) | Section |
|-----------------|-------------|------------------|-----------------|---------|
| [내용] | [번역] | [맥락] | [인용] | [위치] |

**References:**
- [Full APA citations]

### 2. EMPIRICAL PRECEDENTS (실증적 선행연구)
이전 연구의 데이터, 측정값, 관찰 결과, 실험 결과.

| Knowledge Claim | 한국어 번역 | Citation Context | Reference (APA) | Section |
|-----------------|-------------|------------------|-----------------|---------|
| [내용] | [번역] | [맥락] | [인용] | [위치] |

**References:**
- [Full APA citations]

### 3. METHODOLOGICAL HERITAGE (방법론적 유산)
연구 방법, 분석 기법, 측정 도구, 실험 프로토콜.

| Knowledge Claim | 한국어 번역 | Citation Context | Reference (APA) | Section |
|-----------------|-------------|------------------|-----------------|---------|
| [내용] | [번역] | [맥락] | [인용] | [위치] |

**References:**
- [Full APA citations]

### 4. CONTEXTUAL KNOWLEDGE (맥락적 지식)
연구의 지리적, 시간적, 정책적, 사회적 맥락 정보.

| Knowledge Claim | 한국어 번역 | Citation Context | Reference (APA) | Section |
|-----------------|-------------|------------------|-----------------|---------|
| [내용] | [번역] | [맥락] | [인용] | [위치] |

**References:**
- [Full APA citations]

### 5. CRITICAL DISCOURSE (비판적 담론)
학술적 논쟁, 상충하는 연구 결과, 한계점 인정, 미해결 문제.

| Knowledge Claim | 한국어 번역 | Citation Context | Reference (APA) | Section |
|-----------------|-------------|------------------|-----------------|---------|
| [내용] | [번역] | [맥락] | [인용] | [위치] |

**References:**
- [Full APA citations]

## D. 추출 요약
- **처리 일자**: 
- **지식 분포**: Theory (X%) | Empirical (X%) | Method (X%) | Context (X%) | Discourse (X%)
- **총 인용 수**: 
- **인용 중앙 연도**: 

## E. 생성된 연구 질문

### Level 1: 문장 수준 질문
1. [질문]
2. [질문]

### Level 2: 단락 수준 질문
1. [질문]
2. [질문]

### Level 3: 연구 수준 질문
1. [질문]

## F. 나의 메모
[사용자가 나중에 추가할 공간]

---
**Keywords**: #키워드1 #키워드2 #키워드3
**Extracted by**: Meta_researcher / knowledge-extraction
**Last Updated**: {날짜}
```

## index.md 자동 업데이트

새 논문 추가 시 index.md에 다음 형식으로 추가:

```markdown
# Knowledge Index: {주제}

## 논문 목록

| 저자 | 연도 | 제목 | 저널 | 키워드 | 파일 |
|------|------|------|------|--------|------|
| Chen et al. | 2024 | [제목] | GCA | 동위원소, 지하수 | [링크](Chen2024.md) |
| Kim et al. | 2023 | [제목] | EPSL | ... | [링크](Kim2023.md) |

## 통계
- 총 논문 수: N
- 최근 추가: {날짜}
- 주요 키워드: ...
```

## 병렬 처리 (Subagent)

여러 PDF 처리 요청 시:

```
사용자: "papers 폴더의 모든 PDF를 Knowledge_동위원소에 저장해줘"

실행:
1. papers/ 폴더 스캔 → PDF 목록 확인
2. 각 PDF에 대해 Task (Subagent) 생성
3. 각 Subagent가 독립적으로 지식 추출
4. 결과를 Knowledge_동위원소/ 폴더에 저장
5. index.md 통합 업데이트
```

## 품질 기준

1. **정확성**: 모든 인용은 원문 그대로
2. **완전성**: 인용이 있는 모든 주장 캡처
3. **일관성**: 동일한 분류 기준 유지
4. **추적성**: 모든 지식 단위는 원본 위치 표기

## 오류 처리

- PDF 없음 → 즉시 중단, 파일 요청
- 섹션 불명확 → 전체 논문 분석 여부 확인
- 저장 폴더 없음 → 폴더 생성 여부 확인

## 지구화학 특화 필드 (선택적)

지구화학 논문의 경우 추가 추출:
- 동위원소 데이터 범위 (δ18O, 87Sr/86Sr 등)
- 시료 유형 및 개수
- 분석 기기 (MC-ICP-MS, TIMS 등)
- 사용 표준물질
- 분석 정밀도 (2σ)

## 사용 예시

```
# 단일 논문
> "Chen2024.pdf를 읽고 Knowledge_동위원소에 저장해줘"

# 특정 섹션만
> "이 논문의 Introduction만 분석해줘"

# 여러 논문 (병렬)
> "papers 폴더의 모든 PDF를 Knowledge_환경모니터링에 저장해줘"

# 기존 Knowledge 업데이트
> "새 논문 Park2024.pdf를 기존 Knowledge에 추가해줘"
```
