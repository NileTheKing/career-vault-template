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

## 빠른 시작

### 1. 이 repo를 Obsidian vault로 열기

```bash
git clone https://github.com/YOUR_USERNAME/career-vault.git
```

Obsidian → Open folder as vault → 클론한 폴더 선택

### 2. Smart Connections 플러그인 설치

Obsidian → Settings → Community plugins → `Smart Connections` 검색 후 설치

설치 후 플러그인 설정에서 embedding model: `TaylorAI/bge-micro-v2` (기본값 유지)

vault 노트가 임베딩될 때까지 대기 (노트 수에 따라 수 분 소요)

### 3. smart-connections-mcp 서버 설치

```bash
git clone https://github.com/msdanyg/smart-connections-mcp
cd smart-connections-mcp
npm install
npm run build
```

### 4. HuggingFace 토큰 발급

https://huggingface.co/settings/tokens → New token → Type: Read → 복사

### 5. MCP 설정

vault 루트의 `.mcp.json.example`을 `.mcp.json`으로 복사 후 수정:

```bash
cp .mcp.json.example .mcp.json
```

```json
{
  "mcpServers": {
    "smart-connections": {
      "command": "node",
      "args": ["/절대경로/smart-connections-mcp/dist/index.js"],
      "env": {
        "SMART_VAULT_PATH": "/절대경로/이-vault-폴더",
        "HF_TOKEN": "hf_발급받은_토큰"
      }
    }
  }
}
```

### 6. Claude Code에서 vault 열기

```bash
cd /path/to/career-vault
claude
```

첫 실행 시 MCP 서버 승인 프롬프트가 뜨면 허용.
첫 `search_notes` 호출 시 모델 파일 다운로드 (~50MB, 1회만).

---

## 온보딩: 내 무기 채우기

vault 구조는 준비됐지만 내용은 비어있다.
아래 프롬프트를 Claude Code에 붙여넣으면 인터뷰를 통해 나의 T1~Tn, E1~En을 채워준다.

👉 [`onboarding-prompt.md`](./onboarding-prompt.md) 참조

---

## 일상적인 사용법

### 자소서/포트폴리오 작성

```
[회사명] JD 붙여넣어줘. Step 1부터 시작하자.
```

Claude가 CLAUDE.md의 4단계 프로세스에 따라 진행.

### 관련 무기 검색 (벡터 검색)

MCP 연결 시 Claude가 자동으로 `search_notes`를 호출해 관련 노트를 찾는다.
수동으로 요청할 때: `"동시성 관련 내 무기 찾아줘"`

### 새 무기 추가

`templates/무기_노트_템플릿.md` 복사 → `projects/` 하위에 저장 → Obsidian이 자동 임베딩

---

## 폴더 구조

```
career-vault/
├── CLAUDE.md                    ← AI 에이전트 작업 가이드 (필독)
├── _meta/
│   ├── 무기_마스터_목록.md      ← 전체 무기 인덱스 (항상 최신 유지)
│   ├── PORTFOLIO_WRITING_RULES.md
│   └── JOB_APPLICATION_METHODOLOGY.md
├── projects/                    ← T1~Tn 원자화 무기 노트
├── me/
│   ├── 경험/                    ← E1~En 비기술 경험 노트
│   └── 스킬/                    ← 기술 스택 요약
├── companies/                   ← 기업별 JD + 지원서 (gitignored)
└── templates/                   ← 노트 작성 템플릿
```

---

## 태그 시스템

노트에 `#역량/비동기메시지`, `#핵심무기` 등 태그를 달면
JD 키워드가 들어올 때 빠르게 필터링할 수 있다.

전체 태그 목록: `CLAUDE.md` 참조

---

## 주의사항

- `companies/` 폴더는 gitignore 처리됨 — 개인 지원 내역은 로컬에만 존재
- Smart Connections 임베딩 파일(`.smart-env/`)도 gitignore — 재생성 가능
- 수치는 반드시 실측값 기준. 추정치는 `~` 표시
