# Output Format Rules

## Language Policy

- **Review target:** English academic papers
- **All output to user:** Korean (한국어)
- **English original text:** Always displayed alongside Korean translation
- **Agent internal prompts:** English (for optimal LLM performance)

---

## Bilingual Display

### English original + Korean translation

**[EN]** `[English text]`

**[KR]** `[한국어 번역]`

### Translation quality rules
- Academic Korean, not literal translation
- Technical terms: English alongside — "토양 유기 탄소(SOC)"
- Long text: summary translation allowed

---

## Deliberation Result Display

### Consensus issue (2+ reviewers agree)

```markdown
#### [합의] — Issue title in Korean
**지적:** R1, R2 동의

**[EN]** `[problematic text]`
**[KR]** `[번역]`

`[한국어로 쉬운 설명: 독자 관점에서 왜 문제인지]`

**수정안:** `[revised text]`
**근거:** [어떤 지식/규칙에 기반한 지적인지]
```

### Unique finding (1 reviewer only)

```markdown
#### [R1 발견] — Issue title in Korean
`[한국어 설명]`

**수정안:** `[revised text]`
**근거:** [근거]
```

### Conflict (reviewers disagree)

```markdown
#### [의견 충돌] — Issue title in Korean
`[충돌 내용 한국어 설명]`

**R1:** `[R1 의견 + 수정안]`
**R4:** `[R4 의견 + 수정안]`

[왜 이 충돌이 발생했는지 한국어 설명]
```

### No issues found

```markdown
### [전원 동의] — 문제 없음
모든 리뷰어가 수정이 필요하지 않다고 판단했습니다.
```

---

## Mode-Specific Output

### Mode 1: Paper

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

### Mode 2: Section

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

### Mode 3: Paragraph

**Intent confirmation phase:**

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

**Sentence review phase:**

```markdown
### 단락 [N] — 문장 [M/전체]

*이전:* [이전 문장]

**[EN]** `[current sentence]`
**[KR]** `[번역]`

---

[합의/발견/충돌 항목들 — 토론 결과]

---
*"적용" / "수정안 A" / "수정안 B" / "다음" / "건너뛰기" / "검색해봐"*
```

**Paragraph completion:**

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

---

## Plain Explanation Rules (mandatory)

Every issue must have a plain Korean explanation:

- No technical jargon (no "Given-New", "nominalization", "stress position")
- Frame as reader experience:
  "이 문장을 읽으면..." / "독자 입장에서는..."
- Compare original and suggestion specifically
- Keep under 2 sentences

---

## Session Summary

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

---

## User Input Prompts

Always show available actions in italic at the bottom of each output block.
Use Korean for action descriptions:

```markdown
*"적용" / "수정안 A" / "다음" / "건너뛰기" / "검색해봐" / "섹션으로"*
```
