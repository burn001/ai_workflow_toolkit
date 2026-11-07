# Academic Paper 워크플로우

학술 논문 작성 보조. Literature Review + 구조화 + 팩트체크 + 동료평가.

## 입력 파라미터

| 파라미터 | 필수 | 기본값 | 설명 |
|---------|------|-------|------|
| title | O | - | 논문 제목 |
| data_summary | O | - | 데이터/분석 결과 요약 (본인 연구 내용) |
| field | O | - | 연구 분야 (예: 건축공학, HCI, AI) |
| reference_dir | - | none | 기존 참고자료 폴더 경로 |
| output_dir | - | auto | 결과물 저장 경로 |

## 실행 단계

### Phase 0: 사전 수집 (병렬)

**최대 3개 Agent를 동시에 스폰:**

**Agent A** (reference_dir 있을 때만):
- `_common/local-resource-retriever.md` 프롬프트 로드
- model: opus
- 결과 저장: `{output_dir}/_artifacts/00-user-references.md`

**Agent B — 한국 학술논문 검색**:
- `_common/korean-academic-retriever.md` 프롬프트 로드 (공개 학술 DB: KCI·RISS·Google Scholar 웹 검색)
- model: sonnet, count: 15
- 결과 저장: `{output_dir}/_artifacts/00-korean-sources.md`

**Agent C — 국제 학술 출처 수집**:
- `_common/web-retriever.md` 프롬프트 로드
- model: sonnet, count: 15, focus: academic
- 결과 저장: `{output_dir}/_artifacts/01-sources.md`

---

### Phase 1: 출처 검증

**Agent 1개 스폰** (model: opus):
- `_common/fact-checker.md` 프롬프트 로드
- 수집된 출처 전체를 검증
- 결과 저장: `{output_dir}/_artifacts/02-source-verification.md`

---

### Phase 2: Literature Review 작성

**Agent 1개 스폰** (model: opus):

```
프롬프트 핵심:
- 검증된 출처만 사용
- 모든 주장에 하이퍼링크 인용 필수 (인용 없는 문장 = 삭제)

문헌 고찰 구조:
1. 연구 배경 및 이론적 기반 (핵심 개념 정의, 이론 프레임워크)
2. 선행 연구 검토 (주제별 정리, 연구 간 비교/대조)
3. 연구 동향 및 발전 (시간에 따른 변화, 최신 트렌드)
4. 연구 격차 (기존 한계, 본 연구가 채우는 공백)

작성 언어: 학술 한국어, 전문 용어 영문 병기
```

결과 저장: `{output_dir}/_artifacts/03-literature-review.md`

---

### Phase 3: Literature Review 팩트체크

**Agent 1개 스폰** (model: opus):
- `_common/fact-checker.md` 프롬프트 로드
- Phase 2 결과를 검증
- 결과 저장: `{output_dir}/_artifacts/04-lit-review-verification.md`

---

### Phase 4: 방법론 + 구조 설계 (병렬)

**Agent A — 방법론 조언** (model: sonnet):
```
프롬프트: data_summary를 기반으로 연구 설계, 데이터 수집, 분석 방법,
한계점, 윤리적 고려 조언. 방법론 관련 인용 포함.
```
결과 저장: `{output_dir}/_artifacts/05-methodology-advice.md`

**Agent B — 논문 구조 계획** (model: opus):
```
프롬프트: 상세 개요 + 인용 매핑 표 작성.
섹션별 다룰 주제, 인용할 문헌, 예상 분량.
서론 → 이론적 배경 → 연구 방법 → 결과 → 논의 → 결론
```
결과 저장: `{output_dir}/_artifacts/06-paper-outline.md`

---

### Phase 5: 동료 검토

**Agent 1개 스폰** (model: opus):

```
검토 항목:
1. 인용 적절성 (과잉/누락/자기인용 비율)
2. 논리적 구조 (논증 전개, 문헌고찰→연구질문 연결)
3. 방법론 적합성
4. 기여도 명확성
5. 작성 품질

출력: Major Issues / Minor Issues / Suggestions / Strengths
```

결과 저장: `{output_dir}/_artifacts/07-peer-review.md`

---

### Phase 6: 최종 편집

**Agent 1개 스폰** (model: sonnet):

```
프롬프트:
- 팩트체커 수정사항 + 동료평가 피드백 반영
- 모든 표/그림 뒤에 설명 문단 확인
- References 정비 (본문 인용 ↔ References 일치)
- APA 7 형식 최종 검토
- 한국어/영어 문헌 분리 정렬

최종 체크리스트:
- [ ] 모든 주장에 인용 있음
- [ ] 모든 인용이 References에 있음
- [ ] 모든 표/그림에 설명 문단 있음
- [ ] APA 형식 일관성
- [ ] DOI/URL 유효성
- [ ] 저자명 표기 일관성
```

결과 저장: `{output_dir}/literature-review.md` (최종 문헌고찰)

---

### 완료 후

progress.md 업데이트 및 결과 요약:
- 최종 문헌고찰 경로
- 논문 구조 계획 경로
- 수집/검증된 출처 수
- 동료 검토 결과 요약
