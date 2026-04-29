# Career Vault Template

개발자 취업 지원을 위한 **Obsidian + AI CLI + Smart Connections** 통합 시스템.

JD가 주어지면 벡터 검색으로 관련 경험을 찾고 → AI가 자소서/포트폴리오 초안을 작성한다.

```
Obsidian Vault  ←→  Smart Connections (임베딩)
                         ↕ MCP
                    AI CLI (Claude / Codex / Gemini)
```

---

## 준비물 체크리스트

없는 것만 펼쳐서 설치하세요.

- [ ] Git
- [ ] Node.js 18+
- [ ] Obsidian
- [ ] AI CLI (Claude Code / Codex / Gemini 중 택 1)
- [ ] HuggingFace 계정 (무료)

<details>
<summary>Git 설치</summary>

**macOS:** `brew install git` (Homebrew 없으면 → https://brew.sh)

**Windows:** https://git-scm.com/download/win

</details>

<details>
<summary>Node.js 18+ 설치</summary>

**macOS:** `brew install node`

**Windows:** https://nodejs.org → Windows Installer

설치 확인: `node --version` → v18 이상

</details>

<details>
<summary>Obsidian 설치</summary>

https://obsidian.md/download → OS에 맞게 다운로드

</details>

<details>
<summary>AI CLI 설치 (택 1)</summary>

| CLI | 공식 링크 | 설치 |
|---|---|---|
| **Claude Code** | https://claude.ai/code | 공식 페이지 안내대로 |
| **Codex** | https://github.com/openai/codex | `brew install --cask codex` (macOS) / GitHub Releases 바이너리 (Windows) |
| **Gemini** | https://github.com/google-gemini/gemini-cli | `brew install gemini-cli` (macOS) / `npm install -g @google/gemini-cli` (Windows) |

셋 다 MCP 지원. 여러 개 설치해도 됨.

</details>

<details>
<summary>HuggingFace 계정 만들기</summary>

1. https://huggingface.co 가입 (무료)
2. https://huggingface.co/settings/tokens → **New token** → Type: **Read** → 복사
3. 발급된 `hf_...` 토큰을 메모해두기 (아래 MCP 설정에 사용)

> 벡터 검색 모델 파일 다운로드에 필요. 최초 1회만.

</details>

---

## 세팅 순서

### 1. 이 repo 클론 후 Obsidian에서 열기

**macOS:**
```bash
git clone https://github.com/niletheking/career-vault-template.git ~/career-vault
```

**Windows:**
```powershell
git clone https://github.com/niletheking/career-vault-template.git C:\Users\이름\career-vault
```

Obsidian 실행 → **Open folder as vault** → 클론한 폴더 선택

---

### 2. Smart Connections 플러그인 설치

Obsidian → Settings → Community plugins → **Turn on community plugins** → Browse

`Smart Connections` 검색 → Install → Enable

> 설정은 건드리지 않아도 됨. 기본값(`TaylorAI/bge-micro-v2`)으로 자동 임베딩 시작.

---

### 3. MCP 서버 설치 및 빌드

**macOS:**
```bash
git clone https://github.com/msdanyg/smart-connections-mcp ~/smart-connections-mcp
cd ~/smart-connections-mcp && npm install && npm run build
```

**Windows:**
```powershell
git clone https://github.com/msdanyg/smart-connections-mcp C:\Users\이름\smart-connections-mcp
cd C:\Users\이름\smart-connections-mcp
npm install && npm run build
```

---

### 4. MCP 설정

사용하는 AI CLI에 따라 하나만 설정.

<details>
<summary>Claude Code</summary>

**macOS:**
```bash
cd ~/career-vault && cp .mcp.json.example .mcp.json
```

**Windows:**
```powershell
cd C:\Users\이름\career-vault
copy .mcp.json.example .mcp.json
```

`.mcp.json` 열어서 경로와 토큰 수정:

```json
{
  "mcpServers": {
    "smart-connections": {
      "command": "node",
      "args": ["/Users/이름/smart-connections-mcp/dist/index.js"],
      "env": {
        "SMART_VAULT_PATH": "/Users/이름/career-vault",
        "HF_TOKEN": "hf_발급받은_토큰"
      }
    }
  }
}
```

> **Windows** args 경로: `C:\\Users\\이름\\smart-connections-mcp\\dist\\index.js` (역슬래시 2개)

</details>

<details>
<summary>Codex CLI</summary>

`~/.codex/config.json` (Windows: `C:\Users\이름\.codex\config.json`) 에 추가:

```json
{
  "mcpServers": {
    "smart-connections": {
      "command": "node",
      "args": ["/Users/이름/smart-connections-mcp/dist/index.js"],
      "env": {
        "SMART_VAULT_PATH": "/Users/이름/career-vault",
        "HF_TOKEN": "hf_발급받은_토큰"
      }
    }
  }
}
```

</details>

<details>
<summary>Gemini CLI</summary>

`~/.gemini/settings.json` (Windows: `C:\Users\이름\.gemini\settings.json`) 에 추가:

```json
{
  "mcpServers": {
    "smart-connections": {
      "type": "stdio",
      "command": "node",
      "args": ["/Users/이름/smart-connections-mcp/dist/index.js"],
      "env": {
        "SMART_VAULT_PATH": "/Users/이름/career-vault",
        "HF_TOKEN": "hf_발급받은_토큰"
      }
    }
  }
}
```

</details>

---

### 5. AI CLI 실행

vault 폴더에서 실행:

```bash
cd ~/career-vault

claude    # Claude Code — CLAUDE.md 자동 읽힘
codex     # Codex CLI  — AGENTS.md 자동 읽힘
gemini    # Gemini CLI — GEMINI.md 자동 읽힘
```

첫 실행 시 MCP 승인 프롬프트 → **Allow**
첫 `search_notes` 호출 시 모델 다운로드 (~50MB, 1회)

**여기서 온보딩이 자동으로 시작됩니다.**
기존 자료(이력서, 포폴, 자소서 등)가 있으면 `inbox/` 폴더에 넣어두세요.

---

## 사용법

### 자소서/포트폴리오 작성

```
[회사명] JD야. Step 1부터 시작하자.
```

AI가 4단계로 진행: JD 분석 → 무기 매핑 → 초안 작성 → 피드백 시뮬레이션

### 관련 경험 검색

```
동시성 관련 내 경험 찾아줘
```

MCP 벡터 검색으로 관련 노트를 자동으로 찾아줌.

### 새 경험 추가

```
새 무기 추가해줘. [경험 내용]
```

---

## 폴더 구조

```
career-vault/
├── inbox/              ← 온보딩용: 기존 자료 넣는 곳
├── projects/           ← T1~Tn 원자화 무기 노트
├── me/경험/            ← E1~En 비기술 경험 노트
├── companies/          ← 기업별 JD + 지원서 (gitignored)
├── templates/          ← 노트 작성 템플릿
└── _meta/              ← 무기 인덱스, 방법론 문서
```

---

<details>
<summary>문제 해결</summary>

**MCP 연결 확인:**
```bash
claude mcp list
# smart-connections: ✓ Connected 이면 정상
```

**search_notes 에러:**
- `.mcp.json`에 `HF_TOKEN` 있는지 확인
- MCP 서버 경로가 맞는지 확인
- `cd ~/smart-connections-mcp && npm run build` 재빌드

**Smart Connections 임베딩 안 될 때:**
Obsidian → Smart Connections 패널 → Refresh

</details>
