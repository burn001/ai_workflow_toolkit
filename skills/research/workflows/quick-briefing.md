# Quick Briefing 워크플로우

빠른 브리핑 문서 생성 (5-10분). 핵심 사실 위주의 간결한 요약.

## 입력 파라미터

| 파라미터 | 필수 | 기본값 | 설명 |
|---------|------|-------|------|
| topic | O | - | 브리핑 주제 |
| audience | - | 일반 | 대상 (경영진 / 기술팀 / 일반) |
| length | - | medium | 분량 (short / medium / long) |
| output_dir | - | auto | 결과물 저장 경로 |

## 실행 단계

### Step 1: 빠른 리서치

**Agent 1개 스폰** (model: sonnet):

```
프롬프트 핵심:
- Quick research specialist. Find key facts fast.
- WebSearch 3-5회로 핵심 사실, 공식 출처, 주요 통계 수집
- 5-7개 출처 max
- 인용 기록 필수

출력: 핵심 사실 + 출처 목록
```

결과 저장: `{output_dir}/_artifacts/01-research.md`

---

### Step 2: 브리핑 작성

**Agent 1개 스폰** (model: sonnet):

```
프롬프트 핵심:
- Step 1 리서치 결과를 Read로 읽게 함
- audience에 맞게 전문 용어 수준 조정
- length에 맞게 분량 조정

브리핑 구조:
## 핵심 요약 (3줄)
## 주요 포인트
- Point 1 [(출처)](URL)
- Point 2 [(출처)](URL)
...
## 시사점
## 출처

- Korean preferred, technical terms in English
```

결과 저장: `{output_dir}/_artifacts/02-draft-briefing.md`

---

### Step 3: 팩트체크

**Agent 1개 스폰** (model: opus):
- `_common/fact-checker.md`의 프롬프트를 Read로 로드
- Step 2 브리핑을 검증
- 결과 저장: `{output_dir}/_artifacts/03-factcheck.md`

---

### Step 4: 최종 수정

메인 세션이 직접 수행하거나 Agent 스폰:
- 팩트체커 지적 사항 반영
- 사망 링크 제거/대체
- 최종본 저장: `{output_dir}/briefing.md`

---

### 완료 후

progress.md 업데이트 및 결과 요약을 사용자에게 보고:
- 최종 브리핑 경로
- 수집 출처 수
- 팩트체크 결과 요약
