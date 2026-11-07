# Agent4D — 에이전트가 일하는 모습을 3D로 보기

**레포**: https://github.com/burn001/agent4d (Apache-2.0, 한국어 안내 [README.ko.md](https://github.com/burn001/agent4d/blob/main/README.ko.md))

Claude Code에 일을 맡기면 메인 에이전트가 서브에이전트를 여러 개 띄워 동시에 검색·읽기·편집을 합니다. 터미널 글자만으로는 지금 누가 무엇을 하는지 한눈에 보기 어렵습니다. Agent4D는 그 모습을 브라우저 3D 화면에 실시간으로 그려 주고, 지난 세션을 처음부터 다시 재생해 볼 수 있게 해 주는 도구입니다.

▶ [30초 소개 영상(MP4)](https://github.com/burn001/agent4d/releases/download/v0.7.5/agent4d-intro-v0.7.5.mp4)

## 무엇을 보여 주나

- **지금 일하는 에이전트 전체** — 메인 에이전트, 서브에이전트, 워크플로 에이전트, 안에서 띄운 `claude -p` 실행, 백그라운드 작업을 하나의 트리로. 각 노드 옆에 지금 쓰는 도구(검색·읽기·편집·웹 등)와 한 줄 요약이 뜹니다.
- **세션 재생** — 지난 세션이나 진행 중인 세션을 0.5～32배속으로 다시 봅니다. 쉬는 구간은 줄이고, 타임라인을 끌어 원하는 지점으로 갈 수 있습니다.
- **네 가지 표현** — 흐름(Flow), Planet, 복셀 오피스, 복셀 연구소. 저사양 PC용 2D 모드도 있습니다.
- **게이지** — 세션별 컨텍스트 사용률, (선택) 사용 한도.
- **모든 프로젝트 한 화면에** — 이 PC에서 돌고 있는 Claude Code 세션을 프로젝트별 섬·건물로 보여 줍니다.

## 안전한가

- **로컬·읽기 전용**입니다. Claude Code가 이미 PC에 남기는 세션 기록(`~/.claude/projects/`)을 읽기만 하고, 그 파일에 아무것도 쓰지 않습니다.
- 선택 기능인 원격 링크를 켜지 않으면 아무것도 외부로 보내지 않습니다.
- 서버·CLI는 Node 내장 모듈만 써서 `npm install` 이 필요 없습니다.
- Anthropic이 만든 공식 도구가 아닌 독립 프로젝트입니다.

## 설치와 실행

준비물: **Node.js 22.18 이상**, Claude Code를 쓰는 PC(현재 Windows에서 검증, macOS·Linux는 아직 미검증).

```bash
git clone https://github.com/burn001/agent4d
node agent4d/bin/agent4d.mjs start
```

화면에 나온 `http://127.0.0.1:7788/?t=...` 링크를 브라우저로 엽니다. 아직 Claude Code 기록이 없으면 클론 폴더 안에서 `npm run demo` 로 예시 화면을 먼저 볼 수 있습니다.

Claude Code에게 "agent4d 열어"라고 말해서 켜고 싶다면 레포에 함께 든 스킬을 설치하세요. 방법은 레포 README의 "Claude Code 에서 쓰기(스킬)" 절에 있습니다.

## 이 툴킷과 같이 쓰면

`skills/research` 의 심층 리서치처럼 서브에이전트를 여럿 띄우는 작업을 맡겨 놓고 Agent4D 화면을 열어 두면, 조사·검증·작성 단계가 어떻게 나뉘어 돌아가는지 눈으로 확인할 수 있습니다. 에이전트에게 일을 어떻게 나눠 맡길지 감을 잡는 데 도움이 됩니다.
