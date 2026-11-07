# Competitive Analysis 워크플로우

경쟁사/경쟁 제품 분석 보고서 생성.

## 입력 파라미터

| 파라미터 | 필수 | 기본값 | 설명 |
|---------|------|-------|------|
| subject | O | - | 분석 대상 (우리 제품/서비스) |
| competitors | O | - | 경쟁사/제품 목록 (쉼표 구분) |
| aspects | - | 기능, 가격, UX | 분석 관점 |
| output_dir | - | auto | 결과물 저장 경로 |

## 실행 단계

### Phase 1: 시장 리서치

**Agent 1개 스폰** (model: sonnet):
- `_common/web-retriever.md` 프롬프트를 Read로 로드
- 검색어: "{subject} vs {competitors} {aspects}"
- count: 15, focus: industry
- 결과 저장: `{output_dir}/_artifacts/01-market-research.md`

---

### Phase 2: 시각 자료 수집

**Agent 1개 스폰** (model: opus):
- `_common/visual-collector.md` 프롬프트 로드
- Phase 1 출처를 context로 전달
- VISUALIZE 항목은 HTML 생성 + Playwright PNG 렌더링까지 에이전트 내에서 완료
- 결과 저장: `{output_dir}/_artifacts/02-visuals.md`

---

### Phase 3: 비교 분석

**Agent 1개 스폰** (model: opus):

```
프롬프트:
- Phase 1-2 결과를 Read로 읽게 함
- 비교 매트릭스 작성:
  | Aspect | Subject | Competitor 1 | Competitor 2 | ... |
- 각 관점별 객관적 비교, 강점/약점, 차별화 기회
- 시각자료 활용
- Be balanced and evidence-based
```

결과 저장: `{output_dir}/_artifacts/03-comparison-matrix.md`

---

### Phase 4: 팩트체크

**Agent 1개 스폰** (model: opus):
- `_common/fact-checker.md` 프롬프트 로드
- Phase 3 분석을 검증
- 결과 저장: `{output_dir}/_artifacts/04-factcheck.md`

---

### Phase 5: 전략 보고서

**Agent 1개 스폰** (model: opus):

```
프롬프트:
- 분석 + 팩트체크 결과를 기반으로 전략 제안:
  1. Key differentiators to emphasize
  2. Gaps to address
  3. Market positioning suggestions
  4. Potential threats to monitor
  5. Quick wins vs long-term initiatives
- Be specific and actionable
- 시각적 근거 활용
```

결과 저장: `{output_dir}/report.md` (최종 보고서)

---

### 완료 후

progress.md 업데이트 및 결과 요약:
- 최종 보고서 경로
- 비교 매트릭스 경로
- 수집 출처 수
- 팩트체크 결과 요약
