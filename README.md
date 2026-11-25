# Claude 런처

**한국어** · [English](https://github.com/mingilee97/claude-cli-launcher_english)

Windows에서 [Claude Code](https://claude.com/claude-code) 세션을 프로젝트별로 모아 보고, 고른 세션을 창 안의 터미널 탭에서 바로 여는 프로그램입니다.

> Anthropic이 만든 공식 프로그램이 아닙니다. 개인이 만든 보조 도구입니다. Windows 전용입니다.

![티저: 폴더 트리, 폴더별 세션 탭, 에이전트 팀 구성](docs/teaser.gif)

<sub>화면의 프로젝트·세션은 시연용 가짜 자료입니다. [고화질 영상(MP4)](docs/teaser.mp4)</sub>

![세션 목록](docs/screenshot-list.png)

Claude Code를 오래 쓰면 세션이 수십 개로 늘어나서 "그때 그 대화"를 다시 찾기 어렵습니다. `claude --resume` 목록을 뒤지는 대신, 프로젝트별로 정리된 목록에서 골라 바로 이어 갈 수 있습니다.

![내장 터미널](docs/screenshot-terminal.png)

<sub>오른쪽 '플랜' 칸은 별도 Claude Code 모드가 있을 때 채워집니다(아래 '알아 둘 점').</sub>

## 할 수 있는 일

- 프로젝트 폴더(기본: `바탕화면\Claude`) 아래 프로젝트와 Claude Code 세션 목록 보기, 검색
- 세션 이어서 열기, 새 세션 시작(창 안 터미널 탭 또는 Windows Terminal)
- 열린 세션 탭을 폴더별로 묶어 보기(윗줄 폴더, 아랫줄 그 폴더의 세션)
- 에이전트 팀 구성 보기: 모델은 색, 추론 수준은 낮음 파랑 → 최대 보라로 구분
- 모델·추론 수준 골라서 열기
- 5시간·7일 사용 한도 표시, 자동 압축 시점 설정
- 인계: 지금 세션을 정리해 새 세션에서 이어 가기(`handoff\` 폴더에 기록)

## 필요한 것

| 항목 | 설치 |
|---|---|
| Windows 10/11 (64비트) | .NET Framework 4.8은 기본으로 들어 있음 |
| Claude Code (`claude`) | `irm https://claude.ai/install.ps1 \| iex` |
| Node.js | `winget install OpenJS.NodeJS.LTS` (상태줄·한도 표시용) |
| WebView2 런타임 | Windows 11에는 기본 포함. 없으면 `winget install Microsoft.EdgeWebView2Runtime` |
| Windows Terminal (선택) | 바깥 창에서 열 때 씀 |

## 설치

### 방법 1: 내려받아 바로 실행

[Releases](https://github.com/mingilee97/claude-cli-launcher/releases/latest)에서 `ClaudeLauncher-*-win-x64.zip`을 받아 압축을 풀고 `ClaudeLauncher.exe`를 실행합니다.

### 방법 2: 한 줄 설치

PowerShell에서:

```powershell
irm https://raw.githubusercontent.com/mingilee97/claude-cli-launcher/main/install.ps1 | iex
```

`%LOCALAPPDATA%\ClaudeLauncher`에 받아서 빌드하고, 바탕화면과 시작 메뉴에 바로가기를 만듭니다.

### 방법 3: 저장소를 받아서

```powershell
git clone https://github.com/mingilee97/claude-cli-launcher.git
cd claude-cli-launcher
.\setup.cmd        # 또는 powershell -ExecutionPolicy Bypass -File install.ps1
```

설치 스크립트가 하는 일:

1. 필요한 프로그램이 있는지 확인하고, 없으면 설치 명령을 알려 줍니다.
2. `build.ps1`로 빌드합니다. Windows에 들어 있는 `csc.exe`를 쓰므로 Visual Studio나 .NET SDK가 필요 없습니다. WebView2 SDK DLL은 NuGet에서 받아 옵니다.
3. 프로젝트 폴더를 묻고 `launcher-config.json`에 적습니다.
4. 바로가기를 만듭니다.

## 설정

- **프로젝트 폴더**: `launcher-config.json`의 `root`. 정하는 순서는 `launcher-config.json` → 환경 변수 `CLAUDE_LAUNCHER_ROOT` → `바탕화면\Claude`입니다.

  ```json
  { "root": "D:\\work\\Claude" }
  ```

- **Claude 설정 폴더**: 환경 변수 `CLAUDE_CONFIG_DIR`가 있으면 그곳, 없으면 `%USERPROFILE%\.claude`.
- `launcher-settings.json`은 런처가 켤 때마다 자기 폴더 경로에 맞춰 새로 씁니다. 손으로 고치지 않아도 됩니다.

## 알아 둘 점

- 기본은 **일반 권한**으로 실행됩니다. 런처 안 세션에 관리자 권한이 필요하면 `.\install.ps1 -Admin` 또는 `.\build.ps1 -Admin`으로 빌드하세요(켤 때마다 UAC 창이 뜹니다).
- 런처 폴더 안에 실행 자료(`status\`, `cache\`, `handoff\`, `plans\`, `webdata-admin\`)가 생깁니다. 저장소에는 올라가지 않습니다.
- 오른쪽 '플랜'·'팀 구성' 칸은 Claude Code 모드(korean-hud)가 보내는 정보로 채워집니다. 이 모드는 이 저장소에 들어 있지 않아서, 없으면 그 칸은 비어 있습니다.

## 다시 빌드

```powershell
.\build.ps1          # ClaudeLauncher.exe
.\build.ps1 -Admin   # 관리자 권한으로 실행되는 판
.\build.ps1 -Test    # 시험용 test.exe도 함께
```

## 폴더 구성

| 경로 | 내용 |
|---|---|
| `build/Program.cs` | 프로그램 전체(C#, WPF) |
| `build/main.xaml`, `build/res.xaml` | 화면 |
| `build/app.manifest`, `build/app-admin.manifest` | 실행 권한(일반/관리자)·DPI 설정 |
| `web/` | 내장 터미널(xterm.js)과 D2Coding 글꼴 |
| `status-tee.js` | Claude Code 상태줄 정보를 런처로 넘기는 스크립트 |
| `assets/app.ico` | 아이콘 |

## 라이선스

MIT. 함께 들어 있는 제3자 자료:

- [xterm.js](https://github.com/xtermjs/xterm.js): MIT (`web/xterm-LICENSE.txt`)
- [D2Coding](https://github.com/naver/d2codingfont) 글꼴: SIL OFL 1.1 (`web/D2Coding-OFL.txt`)
- WebView2 SDK: 빌드할 때 NuGet에서 받으며, 저장소에는 들어 있지 않습니다.
