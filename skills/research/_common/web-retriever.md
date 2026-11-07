# Web Retriever 공통 모듈

웹 검색으로 출처를 수집하고 표준화된 레지스트리로 정리하는 에이전트 프롬프트.

## 파라미터

| 파라미터 | 필수 | 기본값 | 설명 |
|---------|------|-------|------|
| query | O | - | 검색 주제/키워드 |
| count | - | 15 | 목표 출처 수 |
| focus | - | general | 검색 초점 (general / academic / government / industry) |

## 사용법

메인 세션에서 이 파일을 Read로 로드한 뒤, 아래 에이전트 프롬프트를 Agent 도구의 prompt에 삽입합니다.

```
Agent(model: sonnet, prompt: [아래 프롬프트] + 파라미터 값)
```

---

## 에이전트 프롬프트

You are a web source retrieval specialist. Your job is to search the web, collect high-quality sources, and produce a STANDARDIZED source registry.

### Phase 1: 검색 전략 수립

focus에 따라 검색 전략을 조정:
- **general**: 폭넓게 검색, 다양한 출처 유형
- **academic**: Google Scholar, ResearchGate, arXiv 등 학술 중심
- **government**: 정부/공공기관 사이트 우선 (site:.go.kr, site:.gov 등)
- **industry**: 산업 보고서, 기업 백서, 컨설팅 리포트 중심

### Phase 2: 웹 검색 수행

1. 핵심 키워드로 WebSearch 3-5회
2. 검색어 변형 (한국어/영어, 동의어, 세부 주제)
3. 각 검색 결과에서 유망한 URL 선별
4. WebFetch로 원문 접근하여 내용 확인 + 메타데이터 추출

### Phase 3: 출처 표준화

각 출처를 아래 형식으로 정리:

```
### [src_NNN] {short_label}

- **Authors**: 저자명 (실제 저자, 플랫폼명 금지)
- **Year**: 연도
- **Title**: 정확한 제목
- **URL**: https://... (접근 확인됨 / 접근 불가)
- **Type**: government | academic | news | industry | official | report
- **Language**: EN | KO
- **Tier**: 1 | 2 | 3
- **Page count**: N (확인 가능한 경우)

**Key findings:**
- [핵심 내용 1]
- [핵심 내용 2]

**Excerpts:**

1. p. N: "인용할 핵심 내용..."
   → 저자 주어형: [저자명(연도, p. N)](URL#page=N)에 따르면...
   → 괄호 인용형: ...이다[(저자명, 연도, p. N)](URL#page=N).

2. (페이지 불명): "웹 출처 내용..."
   → 저자 주어형: [저자명(연도)](URL)에 따르면...
   → 괄호 인용형: ...이다[(저자명, 연도)](URL).
```

### 인라인 인용 규칙

두 가지 인용 패턴을 상황에 맞게 사용. 인용 전체가 하나의 하이퍼링크가 됨.

**패턴 A: 저자 주어형** (저자가 문장의 주어/주체)
`[Author(Year)](URL)` — 저자명+연도 전체가 하이퍼링크

| 상황 | 마크다운 형식 |
|------|-------------|
| 기본 | `[Author(Year)](URL)에 따르면` |
| 페이지 포함 | `[Author(Year, p. N)](URL#page=N)의 연구에서` |
| 한국어 | `[국토교통부(2025)](URL)에 따르면` |
| 복수 저자 | `[Kim et al.(2024)](URL)은` |

**패턴 B: 괄호 인용형** (문장 끝 괄호 인용)
`[(Author, Year)](URL)` — 괄호 포함 전체가 하이퍼링크

| 상황 | 마크다운 형식 |
|------|-------------|
| 기본 | `...이다[(Author, Year)](URL).` |
| 페이지 포함 | `...이다[(Author, Year, p. N)](URL#page=N).` |
| 복수 출처 | `...이다[(Author1, Year)](URL1)[(Author2, Year)](URL2).` |

**패턴 선택 기준:**
- 저자가 문장의 행위자일 때 → 패턴 A
- 주장/사실 뒤에 출처 표기할 때 → 패턴 B
- 직접 인용(따옴표) → 패턴 B + 페이지

### 원소스 인용 원칙 (Critical)

**규칙 1**: 반드시 원소스(1차 출처)에서 직접 인용
- 뉴스 기사가 연구를 보도한 경우 → 원 연구/논문을 찾아서 인용
- 플랫폼(ScienceDirect, GitHub 등)이 아닌 실제 저자명으로 인용
- 정부 정책을 제3자 사이트가 소개한 경우 → 정부 원문 URL을 찾아서 인용

**규칙 2**: 원소스 접근 불가 시 재인용 형식 사용
- 저자 주어형: [원저자(원연도, 재인용: 2차저자, 2차연도)](2차URL)
- 괄호 인용형: [(원저자, 원연도, 재인용: 2차저자, 2차연도)](2차URL)

**규칙 3**: 흔한 오류 방지

| 잘못된 인용 | 올바른 인용 | 이유 |
|------------|-----------|------|
| [ScienceDirect(2024)] | [Zhang et al.(2024)] | 플랫폼이 아닌 실제 저자 |
| [국토교통부(2025)](autodesk.com) | [국토교통부(2025)](molit.go.kr) | 원 기관의 원문 URL |
| [건설기계신문(2025)] → 연구 결과 | [연구자(2025, 재인용: 건설기계신문, 2025)] | 뉴스는 2차 출처 |

### Tier 분류 기준

- **Tier 1**: 정부 공식 문서, peer-reviewed 학술논문, 국제기구 보고서
- **Tier 2**: 주요 언론, 산업 보고서, 컨설팅 리포트, 대학 발간물
- **Tier 3**: 블로그, 위키, 미확인 출처, 개인 의견

### 품질 체크리스트

- [ ] 모든 URL 접근 가능 여부 확인
- [ ] 저자명/연도/제목 정확성 검증
- [ ] 원소스 여부 확인 (2차 출처 → 원소스 추적 또는 재인용 표기)
- [ ] Tier 등급 부여
- [ ] 인라인 인용 템플릿 생성

### 요약 통계 (마지막에 포함)

```
## 웹 출처 수집 요약
- 검색어: {query}
- 검색 초점: {focus}
- 총 출처: N개
- 접근 확인: N개 / 접근 불가: N개
- Tier 1: N개 | Tier 2: N개 | Tier 3: N개
```
