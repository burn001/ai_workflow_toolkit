# Data Report 워크플로우

데이터 분석 결과를 체계적인 보고서로 정리.

## 입력 파라미터

| 파라미터 | 필수 | 기본값 | 설명 |
|---------|------|-------|------|
| data_path | O | - | 분석 데이터 파일 경로 |
| context | O | - | 분석 배경 및 목적 |
| audience | - | 내부 | 보고 대상 (내부 / 외부 / 학술) |
| output_dir | - | auto | 결과물 저장 경로 |

## 실행 단계

### Step 1: 데이터 읽기

**Agent 1개 스폰** (model: sonnet):

```
프롬프트:
- data_path 파일을 Read 도구로 읽음
- 데이터 구조 (열, 행) 파악
- 핵심 통계 (평균, 중앙값, 범위)
- 주목할 패턴이나 이상치
- 데이터 품질 이슈
```

결과 저장: `{output_dir}/_artifacts/01-data-summary.md`

---

### Step 2: 분석

**Agent 1개 스폰** (model: opus):

```
프롬프트:
- Step 1 결과를 context에 맞게 해석
  1. 핵심 발견 식별
  2. 수치의 의미 설명
  3. 벤치마크 비교 (가능한 경우)
  4. 분석의 한계 언급
  5. 후속 분석 제안
- 수치 정확성 최우선
```

결과 저장: `{output_dir}/_artifacts/02-analysis.md`

---

### Step 3: 시각화 제안

**Agent 1개 스폰** (model: sonnet):

```
프롬프트:
- 각 핵심 발견에 맞는 차트 유형 제안
  - Chart type (bar, line, scatter 등)
  - 축 설정
  - 강조할 비교 포인트
  - 색상/포맷 제안
- ASCII 스케치 또는 설명 제공
```

결과 저장: `{output_dir}/_artifacts/03-visualizations.md`

---

### Step 4: 보고서 초안

**Agent 1개 스폰** (model: opus):

```
프롬프트:
- Step 1-3 결과 + context를 종합하여 보고서 작성
- audience에 맞게 격식 수준 조정

보고서 구조:
## 요약 (Executive Summary)
## 분석 배경
## 데이터 개요
## 주요 발견
## 시사점 및 제언
## 부록: 상세 데이터

표/그림 규칙:
- 모든 표/그림 뒤에 설명 문단 필수
- 핵심 내용, 주요 시사점, 본문 연결 포함
- 설명 없는 독립 배치 절대 금지
```

결과 저장: `{output_dir}/_artifacts/04-draft.md`

---

### Step 5: 팩트체크

**Agent 1개 스폰** (model: opus):
- `_common/fact-checker.md` 프롬프트 로드
- 초안의 수치 정확성 검증
- 결과 저장: `{output_dir}/_artifacts/05-factcheck.md`

---

### Step 6: 최종 보고서

메인 세션 또는 Agent:
- 팩트체크 결과 반영
- 최종본 저장: `{output_dir}/report.md`

---

### 완료 후

progress.md 업데이트 및 결과 요약:
- 최종 보고서 경로
- 분석된 데이터 항목 수
- 핵심 발견 요약
- 팩트체크 결과
