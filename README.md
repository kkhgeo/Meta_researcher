# Meta_researcher

논문 PDF에서 지식을 추출하고, 다중 소스(Knowledge, PDF, Web)를 활용하여 학술 글쓰기를 지원하는 Claude Code 플러그인.

## 개요

```
┌─────────────────────────────────────────────────────────────────────┐
│                                                                     │
│  📄 논문 PDF                                                        │
│       ↓                                                             │
│  🔬 knowledge-extraction                                            │
│       ↓                                                             │
│  📁 Knowledge_{주제}/ 폴더에 마크다운 저장                          │
│       ↓                                                             │
│  ✍️ meta-writing (다중 소스 지원)                                   │
│       ├── Knowledge 폴더 (1순위)                                    │
│       ├── PDF 폴더 (2순위)                                          │
│       └── Web 검색 (3순위, 보완용)                                  │
│       ↓                                                             │
│  📝 학술 글쓰기 (영어 + 한국어)                                     │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

## 설치

### Claude Code에서 사용

프로젝트 폴더에 `.claude/skills/` 디렉토리를 만들고 스킬 복사:

```
your-project/
├── .claude/
│   └── skills/
│       ├── knowledge-extraction/
│       │   ├── SKILL.md
│       │   └── references/
│       │       └── extraction_template.md
│       └── meta-writing/
│           ├── SKILL.md
│           └── references/
│               ├── writing_template.md
│               └── section_guides.md
├── papers/                         # 논문 PDF
└── Knowledge_동위원소/             # 추출 결과
```

## 스킬 목록

### v0.2.1 (현재)

| 스킬 | 설명 | 상태 |
|------|------|------|
| knowledge-extraction | 논문 PDF → 구조화된 지식 마크다운 | ✅ 완료 |
| meta-writing | 다중 소스 기반 학술 글쓰기 + 레퍼런스 검증 | ✅ 완료 |

### 예정

| 스킬 | 설명 | 상태 |
|------|------|------|
| knowledge-search | Knowledge 폴더 고급 검색 | 🔜 예정 |

---

## 1. knowledge-extraction 스킬

### 기능
- 논문 PDF에서 핵심 지식 추출
- 5가지 인식론적 카테고리 분류
- 구조화된 마크다운으로 저장
- 병렬 처리 (Subagent) 지원

### 지식 추출 카테고리
| 카테고리 | 설명 |
|----------|------|
| Theoretical Foundations | 핵심 이론, 개념적 프레임워크, 가설, 모델 |
| Empirical Precedents | 이전 연구의 데이터, 측정값, 실험 결과 |
| Methodological Heritage | 연구 방법, 분석 기법, 측정 도구 |
| Contextual Knowledge | 지리적, 시간적, 정책적, 사회적 맥락 |
| Critical Discourse | 학술적 논쟁, 한계점, 미해결 문제 |

### 사용 예시
```
> "Chen2024.pdf를 읽고 Knowledge_동위원소에 저장해줘"
> "papers 폴더의 모든 PDF를 Knowledge_환경모니터링에 저장해줘"
```

---

## 2. meta-writing 스킬

### 기능
- 다중 소스 기반 학술 글쓰기
- 5회 루프로 지식 탐색
- 영어 + 한국어 이중 출력
- IMRaD 섹션별 글쓰기 지원

### 지식 소스 (우선순위)
| 순위 | 소스 | 설명 |
|------|------|------|
| 1순위 | Knowledge 폴더 | 이미 추출된 마크다운 지식 |
| 2순위 | PDF 폴더 | 원본 논문 직접 읽기 |
| 3순위 | Web 검색 | 부족한 정보 보완 |

### 5회 루프 구조
| 루프 | 작업 |
|------|------|
| 1 | 소스 스캔 + 탐색 계획 |
| 2 | Knowledge 파일 읽기 |
| 3 | 추가 Knowledge + PDF 읽기 |
| 4 | 갭 체크 + Web 검색 (필요시) |
| 5 | 종합 → 글쓰기 |

### 사용 예시
```
# Knowledge만 사용
> "Knowledge_동위원소에서 Introduction 선행연구 부분 작성해줘"

# Knowledge + PDF
> "Knowledge_동위원소와 papers 폴더를 참고해서 Methods 작성해줘"

# 전체 소스 활용
> "Knowledge_환경모니터링과 papers 폴더 기반으로 Discussion 작성해줘.
    최신 연구가 부족하면 웹 검색도 해줘."

# 그림/표 해석
> "Figure 1을 Knowledge_동위원소 기반으로 해석해줘"
```

### 출력 형식
```markdown
# A) Approach checklist (접근법)
# B) Source Summary (소스 요약)
# C) Main text (영어 + 한국어)
# D) References (소스 유형별 APA 7)
# E) Self-assessment (자기 평가)
# F) Reference Verification Report (레퍼런스 검증 보고서)
```

### 레퍼런스 검증 (v0.2.1+)
글쓰기 완료 후 자동 검증:
- 인용-참고문헌 매칭 확인
- APA 7 형식 검증
- 고아 참고문헌 탐지
- 누락 필드 확인

---

## 출력 구조

### Knowledge 폴더
```
Knowledge_동위원소/
├── index.md              # 논문 목록 (자동 업데이트)
├── Chen2024.md           # 개별 논문 지식
├── Kim2023.md
└── Park2022.md
```

### 인용 표기
```
Knowledge 기반: (Chen et al., 2024)
PDF 직접 읽기: (Kim et al., 2023)*
Web 검색: (Park et al., 2025)†
```

---

## 지구화학 특화 기능

- 동위원소 표기 (δ18O, 87Sr/86Sr, εNd)
- 분석 기기 정보 (MC-ICP-MS, TIMS)
- 시료 메타데이터
- 분석 정밀도 (2σ)

---

## 버전 히스토리

### v0.2.1 (현재)
- 레퍼런스 검증 절차 추가 (Phase 4)
- 인용-참고문헌 매칭 자동 검증
- APA 7 형식 검증
- 검증 보고서 자동 생성

### v0.2.0
- meta-writing 스킬 추가
- 다중 소스 지원 (Knowledge + PDF + Web)
- 5회 루프 지식 탐색
- 영어 + 한국어 이중 출력

### v0.1.0
- knowledge-extraction 스킬 초기 버전
- 5가지 인식론적 카테고리 분류
- 병렬 처리 (Subagent) 지원

---

## 라이선스

MIT

## 작성자

KKH
