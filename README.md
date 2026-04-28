# Career Vault Template

개발자 취업 지원을 위한 Obsidian + Claude Code + Smart Connections 통합 시스템.

JD가 주어지면 벡터 검색으로 관련 무기를 찾고 → AI가 자소서/포트폴리오 초안을 작성한다.

---

## 시스템 구성

```
Obsidian Vault (노트 저장소)
    ↕ Smart Connections 플러그인 (임베딩 생성·저장)
    ↕ smart-connections-mcp (MCP 서버, 벡터 검색 API)
    ↕ Claude Code (자소서/포트폴리오 작성 에이전트)
```

---

## 설치 (macOS 기준)

### 1. 필수 도구 설치

**Homebrew** (없으면):
```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

**Node.js 18+**:
```bash
brew install node
```

**Claude Code**:
```bash
npm install -g @anthropic-ai/claude-code
```

**Obsidian**: https://obsidian.md/download

---

### 2. 이 repo를 Obsidian vault로 열기

```bash
git clone https://github.com/YOUR_USERNAME/career-vault-template.git ~/career-vault
```

Obsidian 실행 → **Open folder as vault** → `~/career-vault` 선택

---

### 3. Smart Connections 플러그인 설치

Obsidian → Settings (⌘,) → Community plugins → **Turn on community plugins** → Browse

`Smart Connections` 검색 → Install → Enable

플러그인 설정: embedding model은 기본값(`TaylorAI/bge-micro-v2`) 유지

vault 노트가 자동으로 임베딩됨 (우측 패널에서 진행 상황 확인)

---

### 4. smart-connections-mcp 서버 설치

```bash
git clone https://github.com/msdanyg/smart-connections-mcp ~/smart-connections-mcp
cd ~/smart-connections-mcp
npm install
npm run build
```

---

### 5. HuggingFace 토큰 발급

https://huggingface.co/settings/tokens → **New token** → Type: **Read** → 복사

> 무료 계정으로 충분. 모델 파일 다운로드에 필요 (1회).

---

### 6. MCP 설정 파일 생성

```bash
cd ~/career-vault
cp .mcp.json.example .mcp.json
```

`.mcp.json` 열어서 경로와 토큰 수정:

```json
{
  "mcpServers": {
    "smart-connections": {
      "command": "node",
      "args": ["/Users/YOUR_NAME/smart-connections-mcp/dist/index.js"],
      "env": {
        "SMART_VAULT_PATH": "/Users/YOUR_NAME/career-vault",
        "HF_TOKEN": "hf_발급받은_토큰"
      }
    }
  }
}
```

> **경로 확인**: `echo ~/smart-connections-mcp/dist/index.js` 로 절대경로 확인

---

### 7. Claude Code 실행

```bash
cd ~/career-vault
claude
```

처음 실행하면 MCP 서버 승인 프롬프트 → **Allow** 선택

첫 `search_notes` 호출 시 bge-micro-v2 모델 다운로드 (~50MB, 1회만).

---

## 온보딩: 내 무기 채우기

vault 구조는 준비됐지만 내용은 비어있다.
`onboarding-prompt.md`의 프롬프트를 Claude Code에 붙여넣으면
인터뷰를 통해 나의 T1~Tn, E1~En을 자동으로 채워준다.

👉 [`onboarding-prompt.md`](./onboarding-prompt.md)

---

## 일상적인 사용법

### 자소서/포트폴리오 작성

```
[회사명] JD 붙여넣어줘. Step 1부터 시작하자.
```

Claude가 `CLAUDE.md`의 4단계 프로세스대로 진행:
1. JD & 회사 심층 조사
2. 무기 매핑 (키워드만, 텍스트 금지)
3. 초안 작성
4. 면접관/HR/서류심사자 입장 피드백

### 관련 무기 검색

MCP 연결 시 Claude가 자동으로 벡터 검색 수행.
수동으로 요청할 때:

```
동시성 관련 내 무기 찾아줘
```

### 새 무기 추가

```
새 무기 추가해줘. [경험 내용 설명]
```

---

## 폴더 구조

```
career-vault/
├── CLAUDE.md                    ← AI 에이전트 작업 가이드 (필독)
├── onboarding-prompt.md         ← 최초 1회: 내 무기 채우기
├── _meta/
│   ├── 무기_마스터_목록.md      ← 전체 무기 인덱스 (항상 최신 유지)
│   ├── PORTFOLIO_WRITING_RULES.md
│   └── JOB_APPLICATION_METHODOLOGY.md
├── projects/                    ← T1~Tn 원자화 무기 노트
├── me/
│   ├── 경험/                    ← E1~En 비기술 경험 노트
│   └── 스킬/                    ← 기술 스택 요약
├── companies/                   ← 기업별 JD + 지원서 (gitignored, 로컬에만)
└── templates/                   ← 노트 작성 템플릿
```

---

## 주의사항

- `companies/` — gitignore 처리됨. 지원 내역은 로컬에만 존재
- `.smart-env/` — 임베딩 파일, gitignore 처리됨. Obsidian 열면 자동 재생성
- `.mcp.json` — gitignore 처리됨. HF_TOKEN 포함되므로 절대 커밋 금지
- 수치는 반드시 실측값 기준. 추정치는 `~` 표시

---

## 문제 해결

**MCP가 연결 안 될 때**:
```bash
claude mcp list   # 연결 상태 확인
```
→ `smart-connections: ✓ Connected` 이면 정상

**search_notes 에러 시**:
- HF_TOKEN이 `.mcp.json`에 있는지 확인
- `~/smart-connections-mcp/dist/index.js` 경로가 맞는지 확인
- `cd ~/smart-connections-mcp && npm run build` 재빌드

**Smart Connections 임베딩이 없을 때**:
Obsidian에서 Smart Connections 패널 열기 → Refresh 클릭
