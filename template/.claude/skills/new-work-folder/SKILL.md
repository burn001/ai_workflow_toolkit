---
name: new-work-folder
description: 현재 작업 디렉토리(cwd) 아래에 오늘 날짜 작업 폴더를 "YY.MM.DD. 카테고리. 제목" 형식으로 만들고 _STATUS.md 템플릿을 함께 만든다. "새 작업 폴더", "오늘 폴더 만들기", "작업 폴더 생성", "new work folder" 등의 요청 시 사용.
---

# New Work Folder

현재 작업 중인 프로젝트 루트에 오늘 날짜 작업 폴더를 만드는 스킬.

## 기본 경로

- **생성 위치**: 현재 작업 디렉토리(cwd) 바로 아래
  - 시스템 컨텍스트의 "Primary working directory"를 기준으로 한다
  - 사용자가 다른 경로를 지정하면 그것을 우선한다

## 폴더 명명 규칙

```
YY.MM.DD. 카테고리. 제목
```

**예시:**
- `26.11.10. 리서치. 지역 돌봄 정책 사례 조사`
- `26.11.12. 행정. 연말 실적 보고서 초안`

날짜가 맨 앞에 오므로 파일 탐색기에서 이름순으로 정렬하면 시간 순서대로 보인다.

## 카테고리 참조

- **경로**: 이 스킬 폴더의 `references/categories.md`
- 폴더를 만들기 전에 이 파일을 Read로 읽어 알맞은 카테고리를 고른다
- 알맞은 것이 없으면 사용자에게 새 카테고리를 제안하고, 승인받으면 파일에 추가한다(추가 이력 표에도 한 줄)

## 생성 절차

### 1. 정보 수집

- **날짜**: 기본값 = 오늘
- **카테고리**: `references/categories.md`에서 선택
- **제목**: 작업 내용 한 줄 요약
- **루트 경로**: 기본값 = 현재 cwd

사용자가 제목만 말하면 카테고리는 추천안을 붙여 선택형으로 묻는다.

### 2. 폴더 만들기

Windows PowerShell 예:
```powershell
$date = Get-Date -Format "yy.MM.dd"
$folderName = "$date. 리서치. 예시 제목"
$destPath = Join-Path (Get-Location).Path $folderName
if (Test-Path $destPath) { "ALREADY_EXISTS: $destPath" } else { New-Item -ItemType Directory -Path $destPath | Out-Null; "CREATED: $destPath" }
```

macOS·Linux 예:
```bash
mkdir "$(date +%y.%m.%d). 리서치. 예시 제목"
```

### 3. _STATUS.md 만들기

Write 도구로 만든다(PowerShell `Set-Content`는 인코딩 문제가 생길 수 있다). 프로젝트 CLAUDE.md에 `_STATUS.md` 규칙이 있으면 그것을 따르고, 없으면 아래 기본 템플릿을 쓴다.

```markdown
# {제목}
> 최종 업데이트: {YYYY-MM-DD}

## 상태: 📝 진행중

## 완료
- [ ]

## 남은 작업
- [ ]

## 핵심 메모
-
```

- 제목은 폴더명의 "제목" 부분만(날짜·카테고리 제외)
- 초기 상태는 `📝 진행중`, 날짜는 `YYYY-MM-DD`

### 4. 결과 보고

- 만든 폴더 경로, `_STATUS.md` 생성 여부

## 같은 이름 폴더가 이미 있으면

사용자에게 선택형으로 묻는다: 그대로 쓰기(추천) / 제목 끝에 `(v2)` 붙여 새로 만들기 / 취소.
