# Research with Citations 워크플로우

APA 인용 포함 리서치 보고서 생성. deep-research보다 간결하지만 출처 검증 포함.

## 입력 파라미터

| 파라미터 | 필수 | 기본값 | 설명 |
|---------|------|-------|------|
| topic | O | - | 연구 주제 |
| depth | - | detailed | 리서치 깊이 (quick / detailed / comprehensive) |
| reference_dir | - | none | 기존 참고자료 폴더 경로 |
| output_dir | - | auto | 결과물 저장 경로 |

## 실행 단계

### Phase 0: 사전 수집 (병렬, reference_dir 있을 때)

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

### Phase 1: 웹 출처 수집

**Agent 1개 스폰** (model: sonnet):
- `_common/web-retriever.md`의 프롬프트를 Read로 로드
- depth별 count: quick=5, detailed=10, comprehensive=15
- 결과 저장: `{output_dir}/_artifacts/01-sources.md`

---

### Phase 2: 시각 자료 수집

**Agent 1개 스폰** (model: opus):
- `_common/visual-collector.md`의 프롬프트를 Read로 로드
- Phase 1 출처를 context로 전달
- VISUALIZE 항목은 HTML 생성 + Playwright PNG 렌더링까지 에이전트 내에서 완료
- 결과 저장: `{output_dir}/_artifacts/02-visuals.md`

---

### Phase 3: 보고서 작성

**Agent 1개 스폰** (model: opus):

```
프롬프트 핵심:
- 수집된 모든 출처 + 시각자료를 context로 전달
- 모든 사실적 주장에 하이퍼링크 인용 필수
- 인용 없는 주장 = 삭제

인용 형식:
- 패턴 A (저자 주어형): [Author(Year)](URL)에 따르면...
- 패턴 B (괄호 인용형): ...이다[(Author, Year)](URL).
- 페이지 포함: [(Author, Year, p. N)](URL#page=N)

서론 스타일: 블로그 스타일, 빠르게 본론으로
(이 보고서는 큰 보고서의 일부 챕터로 사용될 수 있음)

표/그림 규칙:
- 순차 번호 (표 1, 표 2... 그림 1, 그림 2...)
- 모든 표/그림 뒤에 설명 문단 필수
- VISUALIZE는 이미 PNG → ![caption](path) 형태로 삽입

References 형식:
- 국내 문헌 (가나다순)
- 해외 문헌 (Alphabetical)
```

결과 저장: `{output_dir}/_artifacts/03-draft.md`

---

### Phase 4: 팩트체크

**Agent 1개 스폰** (model: opus):
- `_common/fact-checker.md`의 프롬프트를 Read로 로드
- Phase 3 초안을 검증
- 결과 저장: `{output_dir}/_artifacts/04-factcheck.md`

---

### Phase 5: 최종 편집

**Agent 1개 스폰** (model: sonnet):

```
프롬프트 핵심:
- 초안 + 팩트체크 결과를 Read로 읽음
- 팩트체커 지적 사항 모두 수정
- 사망 링크 제거/대체
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
