---
name: research
description: 리서치 및 보고서 작성 워크플로우 라우터. "리서치", "조사", "분석", "보고서", "브리핑", "논문", "회의록" 등의 요청 시 적절한 워크플로우를 선택하여 실행.
---

# Research Skill — 워크플로우 라우터

사용자의 요청을 분석하여 적절한 리서치/보고서 워크플로우를 선택하고 실행합니다.

## 라우팅 규칙

| 키워드/맥락 | 워크플로우 | 파일 |
|------------|-----------|------|
| 심층 리서치, 깊이 조사, deep research, 사례 조사 | Deep Research | `workflows/deep-research.md` |
| 리서치, 조사, 보고서 (일반) | Research with Citations | `workflows/research-with-citations.md` |
| 브리핑, 요약, 빠르게 정리, quick | Quick Briefing | `workflows/quick-briefing.md` |
| 논문, 학술, paper, literature review | Academic Paper | `workflows/academic-paper.md` |
| 경쟁 분석, 비교, 벤치마크, vs | Competitive Analysis | `workflows/competitive-analysis.md` |
| 데이터 분석, 수치 분석, CSV, 통계 | Data Report | `workflows/data-report.md` |
| 회의록, 회의 정리, 녹취, minutes | Meeting Minutes | `workflows/meeting-minutes.md` |

## 실행 절차

### 1. 의도 파악
사용자 요청에서 다음을 추출:
- **주제** (topic): 무엇에 대한 리서치인가
- **워크플로우 유형**: 위 라우팅 테이블에서 매칭
- **출력 경로** (output_dir): 사용자가 지정한 경우, 없으면 작업 디렉토리 기준 자동 생성
- **참고자료 경로** (reference_dir): 사용자가 기존 자료를 언급한 경우
- **추가 옵션**: depth, audience 등 워크플로우별 파라미터

### 2. 워크플로우 로드
선택된 워크플로우 파일을 `Read` 도구로 로드합니다.
- 워크플로우 파일 경로: 이 SKILL.md와 같은 디렉토리의 `workflows/` 폴더
- 공통 모듈 프롬프트: 같은 디렉토리의 `_common/` 폴더

### 3. 실행
로드한 워크플로우의 지시에 따라 단계별로 실행합니다.
- 각 단계는 `Agent` 도구로 서브에이전트를 스폰
- 병렬 가능한 단계는 한 메시지에 여러 Agent 호출
- 중간 결과물은 `output_dir/_artifacts/`에 저장
- 최종 결과물은 `output_dir/`에 저장

### 4. 진행 추적
`output_dir/_artifacts/progress.md`에 진행 상황 기록:
```markdown
# Progress: [워크플로우명]
## [날짜] [주제]

- [x] Step 1: 설명 ✓
- [x] Step 2: 설명 ✓
- [ ] Step 3: 설명 ← 진행 중
- [ ] Step 4: 설명
```

## 공통 모듈

워크플로우에서 공통으로 사용하는 에이전트 프롬프트:

| 모듈 | 파일 | 용도 |
|------|------|------|
| Web Retriever | `_common/web-retriever.md` | 웹 검색 + 출처 표준화 |
| Fact Checker | `_common/fact-checker.md` | 인용 검증 + 팩트체크 |
| Local Resource Retriever | `_common/local-resource-retriever.md` | 로컬 파일 APA 인용 추출 |
| Korean Academic Retriever | `_common/korean-academic-retriever.md` | 한국 학술논문 검색 (공개 학술 DB 웹 검색) |
| Visual Collector | `_common/visual-collector.md` | 시각 자료 수집 + HTML 다이어그램 생성/PNG 렌더링 |

워크플로우에서 "웹 출처 수집" 단계가 나오면 `_common/web-retriever.md`를 Read로 로드하여 해당 프롬프트를 Agent에 전달합니다.

## 라우팅 애매한 경우

여러 워크플로우가 겹치면:
1. 사용자에게 AskUserQuestion으로 확인
2. 기본값: `research-with-citations` (가장 범용적)

## output_dir 자동 생성 규칙

사용자가 경로를 지정하지 않으면:
```
[현재 작업 디렉토리]/[YY.MM.DD]. [주제 요약]/
```
예: `26.03.03. 건축AI 신뢰성 심층리서치/`
