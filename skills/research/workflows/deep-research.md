# Deep Research 워크플로우

출처 검증 기반 심층 리서치. 초기 스캔 → 연구 계획 → 심층 조사 → 팩트체크 → 반복 보완.

## 입력 파라미터

| 파라미터 | 필수 | 기본값 | 설명 |
|---------|------|-------|------|
| topic | O | - | 연구 주제 |
| depth | - | 2 | 반복 조사 횟수 (1-3 권장) |
| reference_dir | - | none | 기존 참고자료 폴더 경로 |
| output_dir | - | auto | 결과물 저장 경로 |

## 실행 단계

### Phase 0+0b: 사전 수집 (병렬)

**두 Agent를 동시에 스폰:**

**Agent A — 로컬 참고자료 처리** (reference_dir이 있을 때만):
- `_common/local-resource-retriever.md`의 프롬프트를 Read로 로드
- model: opus
- 결과 저장: `{output_dir}/_artifacts/00-user-references.md`

**Agent B — 한국 학술논문 검색**:
- `_common/korean-academic-retriever.md`의 프롬프트를 Read로 로드 (공개 학술 DB: KCI·RISS·Google Scholar 웹 검색)
- model: sonnet
- 검색어: {topic}
- 결과 저장: `{output_dir}/_artifacts/00-korean-sources.md`

---

### Phase 1: 초기 정찰 (Scout)

**Agent 1개 스폰** (model: sonnet):

```
프롬프트 핵심:
- Quick reconnaissance on: {topic}
- web_search 3-5회로 핵심 영역, 용어, 주요 출처/기관 식별
- Phase 0 결과물 경로를 context로 전달하여 읽게 함
- 출처 URL 기록 필수

출력 형식:
## 주제 개요
## 핵심 영역 (5-7개)
## 주요 용어
## 주요 출처/기관
## 초기 발견된 쟁점
```

결과 저장: `{output_dir}/_artifacts/01-scout-scan.md`

---

### Phase 2+3: 연구 계획 + 웹 심층 수집 (병렬)

**두 Agent를 동시에 스폰:**

**Agent A — 연구 계획** (model: opus):
```
프롬프트 핵심:
- Phase 1 scan 결과를 Read로 읽게 함
- 우선순위별 조사 영역 구조화
- 영역별 질문, 출처 유형, 조사 깊이 설계
- 조사 순서와 출처 품질 기준 제시
```
결과 저장: `{output_dir}/_artifacts/02-research-plan.md`

**Agent B — 웹 출처 수집** (model: sonnet):
- `_common/web-retriever.md`의 프롬프트를 Read로 로드
- 검색 횟수: 5-8회 (한국어/영어 혼합)
- 목표: 15개+ 출처
- 결과 저장: `{output_dir}/_artifacts/03-deep-findings.md`

---

### Phase 4: 1차 팩트체크

**Agent 1개 스폰** (model: opus):
- `_common/fact-checker.md`의 프롬프트를 Read로 로드
- Phase 3 결과물 경로를 전달하여 읽게 함
- 결과 저장: `{output_dir}/_artifacts/04-factcheck-r1.md`

---

### Phase 5: 1차 종합 및 갭 분석

**Agent 1개 스폰** (model: opus):

```
프롬프트 핵심:
- Phase 1~4의 모든 결과물 경로를 전달
- 패턴, 모순, 합의점 식별
- 갭 분석: 부족한 영역, 추가 필요 출처 수
- 판정: "연구 충분 - 최종 정리로 진행" 또는 갭 목록 + 다음 반복 질문

출력 형식:
## 핵심 발견 (출처 포함)
## 출처 현황 (Tier별)
## 일치하는 관점
## 상충하는 관점
## 발견된 갭
## 다음 반복을 위한 질문
```

결과 저장: `{output_dir}/_artifacts/04-synthesis.md`

---

### Phase 6: 반복 조사 (depth 횟수만큼)

**메인 세션이 판단:**
- synthesis 결과에 "연구 충분"이 포함되면 → Phase 7로 건너뜀
- 아니면 → 갭 타겟 추가 조사 실행

**각 반복마다:**
1. Agent (sonnet): 갭 영역 타겟 웹 검색 (10개+ 추가 출처)
   → `{output_dir}/_artifacts/05-additional-rN.md`
2. Agent (opus): 추가 출처 팩트체크
   → `{output_dir}/_artifacts/05-factcheck-rN.md`
3. Agent (opus): 재종합 + 갭 재평가
   - 이전 synthesis 파일 경로를 전달하여 이어서 평가
   → `{output_dir}/_artifacts/04-synthesis.md` (덮어쓰기)

---

### Phase 7: 시각 자료 수집

**Agent 1개 스폰** (model: opus):
- `_common/visual-collector.md`의 프롬프트를 Read로 로드
- 수집된 전체 출처를 context로 전달
- 목표: COLLECT 2-4개, CREATE 2-3개, VISUALIZE 2-4개
- VISUALIZE 항목은 HTML 생성 + Playwright PNG 렌더링까지 에이전트 내에서 완료
- 결과 저장: `{output_dir}/_artifacts/05-visuals.md`

---

### Phase 8: 초안 작성

**Agent 1개 스폰** (model: opus):

```
프롬프트 핵심:
- 모든 수집 결과물 경로를 전달
- 모든 사실적 주장에 하이퍼링크 인용 필수
- 인용 없는 주장 = 삭제

인용 형식:
- 패턴 A (저자 주어형): [Author(Year)](URL)에 따르면...
- 패턴 B (괄호 인용형): ...이다[(Author, Year)](URL).
- 페이지 포함: [(Author, Year, p. N)](URL#page=N)

보고서 구조:
1. 서론 (블로그 스타일, 빠르게 본론으로)
2. 본론 (영역별 분석, 모든 주장에 인용)
3. 논의 (종합 분석, 시사점)
4. 결론 (핵심 요약, 향후 과제)
5. References (국내 가나다순 / 해외 알파벳순)

표/그림 규칙:
- 순차 번호 (표 1, 표 2... 그림 1, 그림 2...)
- 모든 표/그림 뒤에 설명 문단 필수
- VISUALIZE는 이미 PNG → ![caption](path) 형태로 삽입
```

결과 저장: `{output_dir}/_artifacts/06-draft.md`

---

### Phase 9: 최종 팩트체크

**Agent 1개 스폰** (model: opus):
- `_common/fact-checker.md`의 프롬프트
- Phase 8 초안을 검증
- 결과 저장: `{output_dir}/_artifacts/07-final-factcheck.md`

---

### Phase 10: 최종 편집

**Agent 1개 스폰** (model: sonnet):

```
프롬프트 핵심:
- 초안 + 최종 팩트체크 결과를 Read로 읽음
- 팩트체커 지적 사항 모두 수정
- 사망 링크 제거/대체
- 과장/부정확 주장 수정/삭제
- References ↔ 본문 인용 일치 확인
- 표/그림 설명 문단 확인
- 출판 가능 수준의 최종본 출력
```

결과 저장: `{output_dir}/report.md` (최종 보고서)

---

### 완료 후

progress.md 업데이트 및 결과 요약을 사용자에게 보고:
- 최종 보고서 경로
- 수집 출처 수 (Tier별)
- 팩트체크 결과 요약
- 반복 조사 횟수
