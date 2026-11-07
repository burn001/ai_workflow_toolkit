# AI Workflow Toolkit

**비개발자·연구자를 위한 AI 에이전트 작업 관리 템플릿과 안내서**

Claude Code 같은 AI 에이전트에게 일을 맡기다 보면 "어제 어디까지 했더라", "그 자료 어느 폴더에 뒀지" 하는 일이 금방 생깁니다. 이 레포는 에이전트가 **스스로 기록을 남기고, 다음 세션에서 그 기록을 읽어 이어서 일하도록** 만드는 폴더 구조와 규칙을 담았습니다. 한 연구자가 실제 업무에서 쓰며 다듬은 체계를 누구나 쓸 수 있게 줄인 판입니다.

들어 있는 것:

1. **작업 관리 템플릿** (`template/`) — 폴더째 복사해 내 작업 폴더로 씁니다. 일별 로그·진행중 작업표·폴더별 상태 파일, 그리고 이를 자동으로 관리하는 스킬 2개.
2. **리서치 스킬** (`skills/research/`) — 웹 출처 수집 → 사실 확인 → 보고서 작성을 여러 에이전트로 나눠 돌리는 워크플로 모음.
3. **연동 도구 안내** (`docs/api-keys.md`) — 한글 문서(HWP), 국가통계, 국회, 법령, 특허 데이터를 에이전트에 붙이는 공개 도구와 API 키 받는 법.
4. **Agent4D 소개** (`docs/agent4d.md`) — 에이전트가 일하는 모습을 3D로 보는 도구.

---

## 빠른 시작

### 1. Claude Code 설치

공식 안내(https://code.claude.com/docs/ko/overview)를 따라 설치합니다. Claude 유료 구독(Pro 이상) 또는 Anthropic Console 계정이 필요합니다.

- Windows PowerShell: `irm https://claude.ai/install.ps1 | iex`
- macOS·Linux: `curl -fsSL https://claude.ai/install.sh | bash`
- 터미널이 낯설면 데스크톱 앱(Claude 앱의 **Code** 탭)으로도 같은 기능을 쓸 수 있습니다.

설치 뒤 새 터미널에서 `claude --version` 으로 확인하고, 작업 폴더에서 `claude` 를 실행하면 처음에 로그인 창이 뜹니다. Windows에서는 [Git for Windows](https://git-scm.com/downloads/win) 설치를 권장합니다.

### 2. 템플릿 복사

이 레포를 내려받습니다(초록색 **Code** 버튼 → **Download ZIP**, 또는 `git clone https://github.com/burn001/ai_workflow_toolkit`).
그다음 `template` 폴더 안의 내용을 **통째로** 내 작업 폴더(예: `문서\Works 2026`)에 복사합니다. `.claude` 폴더는 숨김 폴더라 탐색기에서 안 보일 수 있으니 "숨긴 항목 표시"를 켜고 복사하세요.

리서치 스킬도 쓰려면 `skills/research` 폴더를 내 작업 폴더의 `.claude/skills/` 아래에 넣습니다. 모든 폴더에서 쓰고 싶으면 사용자 폴더의 `.claude/skills/`(Windows: `C:\Users\<내 계정>\.claude\skills\`)에 넣으세요.

### 3. 첫 세션에서 할 말

작업 폴더에서 터미널을 열고 `claude` 를 실행한 뒤, 이렇게 말해 보세요.

```
CLAUDE.md를 읽고, 나에 대한 소개 부분을 같이 고쳐 쓰자.
나는 ○○ 일을 하고, 주로 ○○ 문서를 만든다.
```

```
/done
```
→ 오늘 날짜를 확인하고 진행중 작업표(ACTIVE)를 브리핑합니다.

```
새 작업 폴더 만들어 줘. 지역 돌봄 정책 사례를 조사할 거야.
```
→ `26.11.10. 리서치. 지역 돌봄 정책 사례 조사` 같은 폴더와 `_STATUS.md` 가 생깁니다.

```
이 폴더에서 국내외 지역 돌봄 정책 사례를 심층 리서치해 줘. 출처를 꼭 달아 줘.
```
→ 리서치 스킬이 출처 수집·검증·보고서 작성을 단계별로 진행합니다.

일을 마칠 때 다시 `/done` 이라고 하면, 오늘 한 일을 일별 로그에 적고 진행중 작업표를 갱신합니다. 다음 날 `/done` 으로 시작하면 어제 기록을 읽고 이어 갑니다.

---

## 폴더 구조

```
ai_workflow_toolkit/
├── template/                         ← 내 작업 폴더로 통째로 복사
│   ├── CLAUDE.md                     ← 에이전트가 매번 읽는 작업 규칙(고쳐 쓰기)
│   ├── _STATUS.md                    ← 작업 폴더 전체 현황
│   ├── _docs/
│   │   ├── WORK_LOG.md               ← 기록 규칙(일별 로그·월간 정리)
│   │   └── log/
│   │       ├── ACTIVE.md             ← 진행중·예정 작업표
│   │       └── 2026-11-10.md         ← 일별 로그 예시
│   ├── 26.11.10. 리서치. 지역 돌봄 정책 사례 조사/
│   │   └── _STATUS.md                ← 작업 폴더별 상태 파일 예시(실사용 때 지우기)
│   └── .claude/skills/
│       ├── done/                     ← /done: 세션 시작 브리핑·마무리 기록
│       └── new-work-folder/          ← "새 작업 폴더 만들어 줘"
├── skills/
│   └── research/                     ← 리서치·보고서 워크플로 스킬
│       ├── SKILL.md                  ← 요청에 맞는 워크플로 고르기
│       ├── workflows/                ← 심층 리서치·브리핑·논문·경쟁 분석·데이터 보고서·회의록
│       └── _common/                  ← 웹 출처 수집·한국 학술논문 검색·사실 확인·시각 자료
├── docs/
│   ├── api-keys.md                   ← 연동 도구와 API 키 받는 법
│   └── agent4d.md                    ← Agent4D 소개
├── .env.example                      ← API 키 넣는 자리(값은 비워 둠)
└── LICENSE                           ← Apache-2.0
```

### 기록 체계 한 장 요약

| 파일 | 무엇을 적나 | 언제 갱신하나 |
|------|------------|--------------|
| `CLAUDE.md` | 나와 일하는 규칙(말투, 결정 묻는 방식, 기록 규칙) | 규칙이 바뀔 때 |
| `_docs/log/ACTIVE.md` | 진행중·예정 작업과 마감 | `/done` 할 때마다 |
| `_docs/log/YYYY-MM-DD.md` | 그날 한 일 | 세션 끝날 때 `/done` |
| `작업폴더/_STATUS.md` | 그 작업의 완료·남은 일·메모 | 그 폴더에서 일할 때 |

**결정은 선택형 + 추천으로** — 템플릿의 CLAUDE.md는 에이전트가 "~할까요?"라고 글로 묻는 대신, 추천안을 첫 번째에 둔 선택지 창을 띄우도록 정해 두었습니다. 클릭 한 번으로 결정하고 넘어갈 수 있어 긴 작업을 맡길 때 편합니다.

---

## 연동 도구 링크

| 분야 | 도구(공개 레포) | 키 | 안내 |
|------|----------------|----|------|
| 한글 문서(HWP·HWPX·PDF) | [chrisryugj/kordoc](https://github.com/chrisryugj/kordoc) · [npm kordoc](https://www.npmjs.com/package/kordoc) | 불필요 | [1절](docs/api-keys.md#1-kordoc--한글-문서-도구-키-불필요) |
| 국가통계(KOSIS) | [chrisryugj/korean-stats-mcp](https://github.com/chrisryugj/korean-stats-mcp) | [KOSIS 공유서비스](https://kosis.kr/openapi/) | [2절](docs/api-keys.md#2-통계청-kosis--국가통계) |
| 국회(의안·회의록·표결) | [hollobit/assembly-api-mcp](https://github.com/hollobit/assembly-api-mcp) | [열린국회정보](https://open.assembly.go.kr) | [3절](docs/api-keys.md#3-국회--열린국회정보) |
| 국회예산정책처 | 〃 (선택 키) | [nabo.go.kr](https://www.nabo.go.kr) | [3-1절](docs/api-keys.md#3-1-국회예산정책처nabo--선택) |
| 국회입법조사처 보고서 | 공공데이터포털 | [data.go.kr](https://www.data.go.kr) | [3-2절](docs/api-keys.md#3-2-국회입법조사처--별도-키-없음공공데이터포털-경유) |
| 법령·판례(법제처) | [chrisryugj/korean-law-mcp](https://github.com/chrisryugj/korean-law-mcp) | [국가법령정보 공동활용](https://open.law.go.kr) (OC) | [4절](docs/api-keys.md#4-법제처--국가법령정보-공동활용) |
| 특허(KIPRIS) | [chrisryugj/korean-patent-mcp](https://github.com/chrisryugj/korean-patent-mcp) | [KIPRIS Plus](https://plus.kipris.or.kr) | [5절](docs/api-keys.md#5-특허--kipris-plus) |
| 에이전트 시각화 | [burn001/agent4d](https://github.com/burn001/agent4d) | 불필요 | [docs/agent4d.md](docs/agent4d.md) |

위 도구는 각 개발자가 공개한 오픈소스입니다. 이 레포는 링크와 사용 안내만 제공하며, 도구 자체의 설치·사용법은 각 레포 README를 따르세요.

---

## Agent4D — 에이전트가 일하는 모습 보기

[Agent4D](https://github.com/burn001/agent4d)는 Claude Code의 메인 에이전트와 서브에이전트가 지금 무엇을 하는지 브라우저 3D 화면에 실시간으로 그려 주고, 지난 세션을 처음부터 다시 재생할 수 있게 해 주는 로컬·읽기 전용 도구입니다(Apache-2.0). 리서치 스킬처럼 여러 에이전트가 나눠 일하는 작업을 맡겨 놓고 화면을 열어 두면 일의 흐름이 눈에 들어옵니다.

```bash
git clone https://github.com/burn001/agent4d
node agent4d/bin/agent4d.mjs start     # 출력된 링크를 브라우저로 열기
```

자세한 내용은 [docs/agent4d.md](docs/agent4d.md)를 보세요.

---

## 보안 주의

- **API 키·비밀번호는 문서나 대화에 붙여 넣지 마세요.** `.env` 파일이나 별도 비밀 폴더(예: 사용자 폴더 아래 `secrets/`)에 두고, CLAUDE.md에는 "어디에 있다"만 적습니다. 이 레포의 `.gitignore` 는 `.env`·`secrets/`·`credentials/` 를 git에서 제외합니다.
- **키가 든 파일을 git에 올리지 마세요.** 실수로 올렸다면 파일을 지우는 것으로 끝나지 않습니다. 발급 사이트에서 키를 폐기·재발급하세요.
- **AI가 낸 숫자는 반드시 검증하세요.** 통계 수치, 금액, 법령 조문 번호, 인용 출처는 원 출처를 열어 직접 확인한 뒤 씁니다. 템플릿 CLAUDE.md도 확인하지 못한 내용에 "확인 필요"를 붙이도록 정해 두었습니다.
- **개인정보가 든 파일**(민원·설문 원자료 등)을 다룰 때는 어떤 서비스로 보내지는지 먼저 확인하세요. kordoc의 개인정보 가리기 기능 같은 도구를 활용할 수 있습니다.
- 에이전트가 파일을 지우거나 메일을 보내는 등 되돌리기 어려운 일을 할 때는 실행 전에 확인을 받도록 CLAUDE.md에 적어 두는 것을 권합니다.

---

## 라이선스

[Apache License 2.0](LICENSE) · Copyright 2026 burn001 (Dr. Cho, Sangkyu)

링크한 외부 도구는 각자의 라이선스를 따릅니다. 이 레포는 Anthropic의 공식 자료가 아닙니다. "Claude", "Claude Code"는 Anthropic, PBC의 상표입니다.
