# Meeting Minutes 워크플로우

회의 녹취록/메모를 구조화된 회의록으로 변환.

## 입력 파라미터

| 파라미터 | 필수 | 기본값 | 설명 |
|---------|------|-------|------|
| transcript | O | - | 회의 녹취록 또는 메모 (텍스트 또는 파일 경로) |
| meeting_type | - | 정기회의 | 회의 유형 (정기회의 / 프로젝트 / 브레인스토밍) |
| participants | - | - | 참석자 (선택) |
| output_dir | - | auto | 결과물 저장 경로 |

## 실행 단계

### Step 1: 요약 추출

**Agent 1개 스폰** (model: sonnet):

```
프롬프트:
- 회의 녹취록/메모를 읽고 핵심 내용 추출:
  - 논의된 주요 안건
  - 결정 사항
  - 액션 아이템 (누가, 무엇을, 언제)
  - 미결 사항 / 다음 회의 안건
- 간결하지만 빠짐없이
```

결과 저장: `{output_dir}/_artifacts/01-summary.md`

---

### Step 2: 회의록 작성 + 액션 아이템 추출 (병렬)

**두 Agent를 동시에 스폰:**

**Agent A — 회의록 포맷팅** (model: sonnet):

```
회의록 구조:
## 회의 정보
- 일시:
- 참석자:
- 회의 유형:

## 안건 및 논의 내용
### 1. [안건명]
- 논의 내용
- 결정 사항

## 액션 아이템
| 담당자 | 내용 | 기한 |
|-------|------|------|

## 다음 회의 안건

Korean, formal business style.
```

결과 저장: `{output_dir}/minutes.md` (최종 회의록)

**Agent B — 액션 아이템 추출** (model: sonnet):

```
각 액션 아이템:
- 담당자 (who)
- 내용 (what) — specific and actionable
- 기한 (when) — specific date if mentioned
- 우선순위 (priority) — high/medium/low
- 관련 안건 (context)

소유자/기한 미지정 항목은 플래그 표시
```

결과 저장: `{output_dir}/_artifacts/02-action-items.md`

---

### 완료 후

progress.md 업데이트 및 결과 요약:
- 최종 회의록 경로
- 액션 아이템 수
- 미결 사항 수
