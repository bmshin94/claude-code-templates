# Claude Code Templates 전수조사 분석 보고서 (한국어)

> 작성일: 2026-10-08
> 대상 저장소: **https://github.com/davila7/claude-code-templates** (원본)
> 분석 대상(포크): **https://github.com/bmshin94/claude-code-templates**
> 공식 사이트: **https://aitmpl.com** · 문서: **https://docs.aitmpl.com**
> npm: **https://www.npmjs.com/package/claude-code-templates** (v1.29.6)
> 라이선스: MIT

---

## 목차

1. [프로젝트 정체](#1-프로젝트-정체)
2. [폴더 전수조사](#2-폴더-전수조사)
3. [부품 6종 상세](#3-부품-6종-상세)
4. [부가 도구 5종](#4-부가-도구-5종)
5. [내부 동작 원리](#5-내부-동작-원리)
6. [운영 인프라 및 품질 관리](#6-운영-인프라-및-품질-관리)
7. [쉬운 비유로 이해하기](#7-쉬운-비유로-이해하기)
8. [설치 및 사용법](#8-설치-및-사용법)
9. [플러그인인가 스킬인가 MCP인가](#9-플러그인인가-스킬인가-mcp인가)
10. [API 토큰 필요 여부](#10-api-토큰-필요-여부)
11. [AI 에이전트 구축에 도움이 되는가](#11-ai-에이전트-구축에-도움이-되는가)
12. [React / PHP로 만들 수 있는가](#12-react--php로-만들-수-있는가)
13. [유튜브 강의 영상 제작 가능성](#13-유튜브-강의-영상-제작-가능성)
14. [수익화 아이디어 10선](#14-수익화-아이디어-10선)
15. [실행 로드맵](#15-실행-로드맵)
16. [주의사항 및 리스크](#16-주의사항-및-리스크)
17. [참고 링크 모음](#17-참고-링크-모음)

---

## 1. 프로젝트 정체

**한 줄 요약**

> Claude Code(앤트로픽 CLI 코딩 도구)를 "설정하는 수고" 자체를 없애주는
> **2,000개짜리 부품 창고 + 한 줄 명령어 설치 도구 + 부품 구경용 웹사이트**,
> 이 세 개가 한 덩어리로 들어있는 프로젝트.

- **본체 성격**: npm CLI 도구 (`claude-code-templates`, 별칭 `cct`)
- **누적 다운로드**: 134만 회 / 190개국
- **국가별 순위**: 🇺🇸 미국 19.7만 → 🇧🇷 브라질 8.6만 → **🇰🇷 한국 5.5만(3위)** → 🇪🇸 스페인 → 🇹🇷 터키
- **후원**: Bright Data, Neon, Z.AI, Claude for OSS, Vercel OSS

---

## 2. 폴더 전수조사

```
claude-code-templates/
├── cli-tool/          96MB  ← 심장부. 부품 창고 + 설치 엔진
│   ├── components/          ← 부품 2,000여 개
│   ├── src/                 ← 설치 엔진 (index.js 157KB, analytics.js 83KB)
│   ├── templates/           ← 언어별 초기 세팅 (JS/TS, Python, Go, Rust, Ruby)
│   └── tests/               ← Jest 테스트
├── dashboard/         30MB  ← aitmpl.com (Astro 5 + React + Tailwind v4)
│   └── src/pages/api/       ← 다운로드 집계 등 API 엔드포인트
├── docs/             9.2MB  ← 구 정적 사이트 + 블로그 + 카탈로그 JSON
├── cli-rust/         216KB  ← 설치 엔진 Rust 재작성판 (실험적 v0.1.0)
├── cloudflare-workers/324KB ← 크론 작업 4종
├── scripts/          244KB  ← 카탈로그 자동 생성 Python 스크립트
├── database/          16KB  ← SQL 마이그레이션 2개
├── .claude/                 ← 이 저장소 자체의 Claude 설정 (관리 에이전트 15개)
└── CLAUDE.md          33KB  ← Claude에게 주는 프로젝트 지침서
```

### 부품 재고 현황 (`dashboard/public/counts.json` 실측)

| 부품 종류 | 개수 | 카테고리 | 설치 위치 | 정체 |
|---|---:|---:|---|---|
| 🎨 **Skills** | **877** | 31 | `.claude/skills/` | 작업 노하우 묶음 (PDF, 엑셀, 디자인…) |
| 🤖 **Agents** | **422** | 28 | `.claude/agents/` | 전문가 페르소나 프롬프트 |
| ⚡ **Commands** | **287** | 25 | `.claude/commands/` | 슬래시 명령어 |
| 🔌 **MCPs** | **103** | 13 | `.mcp.json` | 외부 서비스 연결 설정 |
| ⚙️ **Settings** | **72** | 13 | `.claude/settings.json` | 타임아웃·권한·상태줄 |
| 🪝 **Hooks** | **62** | 12 | `.claude/hooks/` | 자동 트리거 (강제 실행) |
| 🧩 **Mods** | **25** | 8 | `.claude/skills/` | 터미널 UI 개조 플러그인 (초기 접근) |
| 🔁 **Loops** | **18** | 3 | `.claude/loops/` | 자율 반복 워크플로 |
| 📦 **Templates** | **14** | — | 프로젝트 루트 | 언어별 완성 세팅 |
| | **총 2,019** | | | |

---

## 3. 부품 6종 상세

### ① Agents — "전문가 한 명을 고용"

실제 `agents/development-team/frontend-developer.md` 구조:

```markdown
---
name: frontend-developer
description: "Use when building complete frontend applications across
             React, Vue, and Angular... <example>...</example>"
tools: Read, Write, Edit, Bash, Glob, Grep
---

You are a senior frontend developer specializing in modern web
applications with deep expertise in React 19+, Vue 3.5+, Angular 20+...
```

**핵심**: 코드가 아니라 **글(프롬프트)**. 메모장으로도 만들 수 있다.
- `tools:` → 그 에이전트가 쓸 수 있는 도구 제한 (권한 최소화)
- `<example>` → "언제 이 에이전트를 부를지"를 Claude에게 학습시킴

**인기 TOP 3**: frontend-developer(일 3,444) / code-reviewer(2,641) / ui-ux-designer(2,224)

### ② Commands — "원터치 버튼"

```markdown
---
allowed-tools: Read, Write, Edit, Bash
argument-hint: [file-path] | [component-name]
description: Generate a complete test file for a specified source file...
---
Generate comprehensive test suite for: $ARGUMENTS

- Test framework: !`cat package.json | grep -E '"jest"|"vitest"' | head -3`
- Existing tests: !`find . -name "*.test.*" | head -5`
```

**숨은 기술 2개**
- `$ARGUMENTS` → 사용자 입력 인자 치환
- **`` !`명령어` `` → 셸 명령을 실제 실행해 결과를 프롬프트에 주입.**
  → 프로젝트가 Jest인지 Vitest인지 AI가 스스로 파악. 템플릿이 "똑똑해 보이는" 이유.

**인기**: generate-tests(1,025) / ultra-think(783) / create-architecture-documentation(519)

### ③ Hooks — "강제 규칙" (가장 중요한 차별점)

```json
{
  "description": "Prevent direct pushes to protected branches (main, develop)...",
  "hooks": {
    "PreToolUse": [{
      "matcher": "Bash",
      "hooks": [{ "type": "command",
        "command": "python3 \"$CLAUDE_PROJECT_DIR\"/.claude/hooks/prevent-direct-push.py" }]
    }]
  }
}
```

| 구분 | 성격 | AI가 무시 가능? |
|---|---|---|
| Agent / Command / Skill | "이렇게 해주세요" (부탁) | ✅ 가능 |
| **Hook** | **엔진이 강제 실행** | ❌ **불가** |

→ **절대 어기면 안 되는 규칙은 반드시 Hook으로 만들어야 한다.**

동작 흐름:
```
Claude: "git push origin main 실행할게"
   ↓ PreToolUse 훅이 가로챔
Python 스크립트: "main이네? 거부!"
   ↓
Claude: 실행 못 함. 다른 방법 탐색
```

### ④ MCPs — "외부 세계로 나가는 문"

```json
{ "mcpServers": { "dbhub": {
    "command": "npx", "args": ["-y", "@bytebase/dbhub@latest"],
    "env": { "DATABASE_DSN": "<your-database-dsn>" } }}}
```

실제 키는 들어있지 않고 `<your-...>` **플레이스홀더만** 제공 (보안 설계).
**인기**: context7(1,398) / memory-integration(703) / playwright(574) / postgresql(418)

### ⑤ Loops — 이 저장소의 가장 독특한 부분

```markdown
---
interval: 30m
stop-condition: Every public API, command and config option is documented
                and the docs build passes with no warnings.
components: [agent:documentation/documentation-engineer,
             command:documentation/update-docs,
             command:git-workflow/create-pr]
---
## Iteration steps
1. Perceive — 문서와 소스 비교
2. Reason   — 어긋난 부분 목록화
3. Plan     — 이번 회차 최소 단위 선택
4. Act      — 문서 수정
5. Observe  — 빌드 통과 시 PR 생성, 아니면 반복
```

**패러다임**: 사람이 프롬프트를 계속 넣는 모델이 아니라,
**목표 + 주기 + 종료조건을 주고 AI가 스스로 돌게 하는** 모델.
`components:` 필드의 다른 부품을 설치 시 함께 끌어온다.

**들어있는 자율 에이전트 설계 패턴 18종 (발췌)**
| 루프 | 패턴 |
|---|---|
| `builder-reviewer-loop` | 생성자/검토자 분리 |
| `adversarial-review-loop`, `devils-advocate-loop` | 적대적 자기검증 |
| `human-approval-loop` | 인간 승인 게이트 |
| `completion-contract-loop` | 완료 조건을 계약으로 명시 |
| `anti-spin-build-loop` | 무한루프/제자리걸음 방지 |
| `build-test-fix-loop` | 빌드→테스트→수정 자동 반복 |
| `quality-streak-loop` | 연속 품질 통과 요구 |

### ⑥ Mods — 터미널 UI 자체를 개조 (EARLY ACCESS)

```
mods/{카테고리}/{이름}/
├── .claude-plugin/plugin.json   (이름, 설명, userConfig)
├── hooks/hooks.json             (모듈 목록)
├── hooks/*.ts                   (register(on, options) 함수 훅)
└── README.md
```

`register(on, options)` 안에서 엔진 이벤트를 `($, e, next)` 미들웨어로 가로챈다.

| Mod | 기능 |
|---|---|
| `ui/tool-timing-badge` | 도구 실행 시간 배지 |
| `security/secret-redactor` | 비밀값 자동 마스킹 |
| `observability/universal-audit-log` | 전 행동 감사 로그 |
| `enterprise/admin-capability-lockdown` | 관리자 기능 잠금 |
| `productivity/webfetch-cache` | 웹 조회 캐싱 |
| `integrations/websearch-to-exa` | 검색 엔진 교체 |
| `games/snake`, `pacman`, `minesweeper`, `flappy`, `cc-arcade` | 터미널 게임 🎮 |

⚠️ `CLAUDE_CODE_ENABLE_FUNCTION_HOOKS=1` + Claude Code ≥ 2.1.259 필요. API 변경 가능.

---

## 4. 부가 도구 5종

| 명령어 | 기능 |
|---|---|
| `--analytics` | 실시간 분석 대시보드 (세션 상태·토큰·비용) — `analytics.js` 83KB |
| `--chats` / `--chats-mobile` | 모바일 대화 모니터. `--tunnel` 로 Cloudflare 터널 외부 접속 |
| `--health-check` | 설치/설정 종합 진단 — `health-check.js` 46KB |
| `--plugins` / `--skills-manager` / `--teams` | 플러그인·스킬·멀티에이전트 세션 관리 대시보드 |
| `--sandbox e2b` / `--studio` | E2B 격리 샌드박스 실행 |
| `--create-agent` | 전역 에이전트 생성 (Claude Agent SDK) |
| `--clone-session <url>` | 공유된 Claude 세션 가져오기 |
| `--2025` | 연간 사용 통계 리뷰 |

---

## 5. 내부 동작 원리

`cli-tool/src/index.js:491` 등에서 확인한 실제 흐름:

```
1. npx claude-code-templates@latest --agent development-team/frontend-developer
                            ↓
2. GitHub raw URL 조립
   https://raw.githubusercontent.com/davila7/claude-code-templates/main/
   cli-tool/components/agents/development-team/frontend-developer.md
                            ↓
3. 다운로드 → .claude/agents/frontend-developer.md 저장
   (MCP는 .mcp.json 병합, Settings는 settings.json 병합)
                            ↓
4. 익명 통계를 Supabase 전송 (CCT_NO_TRACKING=true 로 비활성화 가능)
```

**핵심 설계**: npm 패키지에는 **설치 프로그램만** 들어있고, 부품은 **GitHub를 CDN처럼 써서 실시간 fetch**.
- 장점: 부품 추가 시 npm 재배포 불필요, 항상 최신
- 단점: 오프라인 불가, 네트워크 의존

**설치 후 내 폴더**
```
내프로젝트/
├── src/
├── package.json
├── .claude/              ← 새로 생김
│   ├── agents/frontend-developer.md
│   ├── commands/ · hooks/ · skills/ · loops/
│   └── settings.json
└── .mcp.json             ← MCP 설치 시
```
전부 **프로젝트 폴더 안**. 시스템 미변경. 파일 삭제만으로 제거 완료.
**`.claude/`를 git 커밋하면 팀 전체가 동일 AI 환경 공유** ← 실무 최강 활용법.

---

## 6. 운영 인프라 및 품질 관리

### 인프라
| 영역 | 스택 |
|---|---|
| 대시보드 | Astro 5 + React + Tailwind v4 → **Cloudflare Pages** SSR |
| DB | Supabase(다운로드 집계) + Neon(Claude Code 버전 감시) |
| 인증 | Clerk |
| 에러 추적 | Sentry — 공식 SDK 미사용, **의존성 0 자체 클라이언트 3개** |
| 크론 Workers | 버전감시(30분) / 헬스체크(1시간) / 주간 KPI 텔레그램(일 14시) / 주간 뉴스레터 Resend(일 16시) |
| CI/CD | GitHub Actions → main 푸시 시 자동 배포 |
| 보안 스캔 | **NVIDIA SkillSpector** (64개 취약점 패턴). PR마다 검사, HIGH/CRITICAL이면 머지 차단 |

### 자기참조 품질 관리 — AI로 AI 저장소를 운영

`.claude/agents/` 에 이 저장소 관리 전용 에이전트 **15개**:

```
component-reviewer    새 부품 품질/보안 검수
component-migrator    외부 저장소에서 부품 이식
component-improver    개선 후 PR 자동 생성
component-researcher  개선점 웹 리서치
catalog-generator     카탈로그 재생성
blog-writer           블로그 자동 작성
build-checker / deployer / linear-tracker / agent-expert
command-expert / mcp-expert / cli-ui-designer / docusaurus-expert / frontend-developer
```

→ **"기여 → AI검수 → AI개선 → AI배포"** 파이프라인.
`CLAUDE.md`에 "모든 부품 변경은 component-reviewer 에이전트로 검수 필수"가 규칙으로 명문화.

### 보안 대응 실적
`CHANGELOG.md` v1.29.4 — **실제 CVE급 취약점 패치**
`GHSA-79wm-x847-7cvg` / CWE-78, CWE-306, CWE-352 / **CVSS 8.8**
`--studio` 서버의 인증 없는 OS 명령어 주입(RCE). 외부 제보자(@spartan8806) 크레딧 기록.
→ 보안 대응 체계가 실제로 돌아가는 프로젝트라는 신호.

### 기여 규칙 (중요)
| 워크플로 | 생성 JSON(`docs/components.json`, `dashboard/public/`) 커밋? |
|---|---|
| 메인테이너 (직접 작업) | ✅ 재생성 후 함께 커밋 |
| **외부 기여자 (포크 PR)** | ❌ **금지** — `cli-tool/components/` 아래 파일만. 생성 파일은 머지 후 자동 재생성 |

이유: 생성 JSON은 한 줄 blob이라 두 PR이 동시에 건드리면 **항상 충돌**.
`generated-files-guard.yml` 워크플로가 위반 PR을 자동 차단.

---

## 7. 쉬운 비유로 이해하기

### "요리사 Claude에게 주는 레시피 북 + 주방 도구 세트"

Claude Code는 아주 똑똑하지만 **이 집 주방을 처음 보는 요리사**다.
매번 "간은 싱겁게", "칼은 이거", "설탕은 저 서랍"을 설명하는 게 귀찮다.
이 저장소는 그 설명서를 **2,000장 미리 만들어 둔 것**이다.

| 부품 | 주방 비유 |
|---|---|
| Agent | 분야별 전문 요리사 명함 ("파스타 20년차") |
| Command | 원터치 버튼 ("자동 볶음밥") |
| Skill | 기술 교본 ("생선 손질법 매뉴얼") |
| MCP | 외부 연결선 (마트·냉장고 직결) |
| Hook | 주방 안전장치 (가스 과열 시 자동 차단) |
| Setting | 기본 설정 (불 세기, 타이머) |
| Loop | 자동 반복 ("30분마다 육수 확인, 맑아지면 멈춤") |
| Mod | 주방 자체 개조 (인덕션 교체, 조명 설치) |
| Template | 밀키트 (재료+레시피 한 박스) |

### 가장 혼동되는 3가지 구분

```
Command  =  내가 "지금 해!" 버튼 누름            → 능동 호출
Agent    =  Claude가 "전문가 불러야겠다" 판단     → 반자동 위임
Skill    =  Claude가 "이 매뉴얼 필요하네" 참조    → 자동 참조
```

실제 예시
- `/generate-tests src/a.ts` → **Command** (내가 명시 실행)
- "이 PR 리뷰해줘" → Claude가 알아서 `code-reviewer` **Agent** 호출
- PDF 파일 제공 → Claude가 알아서 `pdf-processing` **Skill** 참조

### 전체 구조도

```
     [ 기여자들 ]              [ 자동화 ]              [ 사용자 ]
          │                        │                       │
   .md 파일 PR 제출 ──→  GitHub Actions                    │
                          ├─ SkillSpector 보안검사 ────┐   │
                          ├─ component-reviewer 검수   │   │
                          └─ Python 카탈로그 재생성     │   │
                                    ↓                  │   │
                      ┌─ docs/components.json ─────────┘   │
                      └─ dashboard/public/*.json           │
                                    ↓                      │
                         Cloudflare Pages 자동배포          │
                                    ↓                      │
                            aitmpl.com ←───────── 구경하고 복사
                                                           │
                            GitHub raw ←──── npx로 직접 다운로드
                                    ↓
                            Supabase ←──── 익명 설치 통계
                                    ↓
                       Cloudflare Workers 크론 4종
```

---

## 8. 설치 및 사용법

### 사전 준비 (2개)

```bash
# 1) Node.js 18+
node -v

# 2) Claude Code 본체 (이 저장소는 '확장'이라 본체가 먼저 필요)
npm install -g @anthropic-ai/claude-code
claude --version
```

> ⚠️ Claude Code 본체 사용에는 **Claude Pro/Max 구독** 또는 **Anthropic API 키**가 필요.
> 이는 이 저장소와 무관한 별개 사안.

### 설치 방법 4가지

**① 대화형 (입문자 추천)**
```bash
cd 내프로젝트
npx claude-code-templates@latest
```

**② 웹에서 골라 복사 (실무 추천)**
```
https://aitmpl.com → 검색/필터 → Install 클릭 → 클립보드 복사 → 터미널 붙여넣기
```
다운로드 수가 표시되므로 **검증된 부품 선별 가능**.

**③ 명령어 직접**
```bash
# 1개
npx claude-code-templates@latest --agent development-team/frontend-developer --yes

# 여러 개 (쉼표 구분, 타입 혼합 가능)
npx claude-code-templates@latest \
  --agent development-team/frontend-developer,development-tools/code-reviewer \
  --command testing/generate-tests,git-workflow/commit \
  --skill development/senior-frontend \
  --mcp devtools/context7 \
  --hook git/prevent-direct-push \
  --setting statusline/context-monitor \
  --yes

# 미리보기만 (처음엔 이걸로 확인)
npx claude-code-templates@latest --agent development-team/frontend-developer --dry-run
```

**④ 전역 설치 + 짧은 별칭**
```bash
npm install -g claude-code-templates
cct --agent development-team/frontend-developer --yes
```

### 전체 옵션 치트시트 (CLI 소스 전수 추출)

```bash
### 부품 설치 ###
--agent <경로>      에이전트          --skill <경로>     스킬
--command <경로>    슬래시 명령어      --loop <경로>      자율 루프(+참조부품 동시설치)
--mcp <경로>        외부 연결          --mod <경로>       Mod (초기접근)
--hook <경로>       자동 훅            --template <이름>  프로젝트 템플릿
--setting <경로>    설정
--yes               확인 생략          --dry-run          미리보기만
--directory <경로>  대상 폴더 지정     --verbose          디버그 로그

### 대시보드 / 도구 ###
--analytics         실시간 분석 (토큰·비용·세션)
--chats             모바일 대화 모니터   --tunnel → 외부 접속
--health-check      종합 진단 (= --health, --check, --verify)
--plugins           플러그인·마켓플레이스 관리
--skills-manager    설치된 스킬 탐색
--teams             멀티에이전트 세션 리뷰
--2025              연간 사용 통계

### 분석 / 최적화 ###
--command-stats     기존 명령어 분석 후 최적화 제안
--hook-stats        기존 훅 분석
--mcp-stats         기존 MCP 설정 분석

### 고급 ###
--create-agent <이름>   전역 에이전트 생성 (API 키 필요)
--list-agents / --remove-agent / --update-agent
--sandbox e2b           E2B 격리 샌드박스 (API 키 필요)
--studio                로컬/클라우드 Studio UI
--clone-session <URL>   공유 세션 가져오기
--prompt "<프롬프트>"   설치 후 즉시 실행
--workflow <해시>       워크플로 일괄 설치
```

### 실전 시나리오

**A. 신규 React 프로젝트 (3분)**
```bash
cd my-react-app
npx claude-code-templates@latest \
  --agent development-team/frontend-developer,development-tools/code-reviewer \
  --skill development/senior-frontend,creative-design/frontend-design \
  --command testing/generate-tests,git-workflow/commit \
  --setting statusline/context-monitor --yes

claude
> /generate-tests src/components/Button.tsx
> "이 컴포넌트 접근성 검토해줘"     ← frontend-developer 자동 호출
```

**B. 팀 공통 환경 배포**
```bash
# 팀 리드 1회
npx claude-code-templates@latest --agent ... --hook ... --setting ... --yes
git add .claude/ .mcp.json
git commit -m "chore: 팀 공통 Claude Code 환경 추가"
git push

# 팀원
git pull      # 끝. 동일 AI 환경 획득
```

**C. 문제 진단**
```bash
npx claude-code-templates@latest --health-check
```

### 제거 / 통계 끄기
```bash
rm .claude/agents/frontend-developer.md    # 파일만 삭제
rm -rf .claude/                            # 전부 초기화

export CCT_NO_TRACKING=true                # 익명 통계 비활성화
# 또는 CCT_NO_ANALYTICS=true, CI=true
```

---

## 9. 플러그인인가 스킬인가 MCP인가

**정답: 셋 다 아님. "그 셋을 모두 배포하는 패키지 매니저".**

```
         ┌──────────────────────────────────────────┐
         │   claude-code-templates                  │
         │   = 패키지 매니저 + 카탈로그 (npm 같은 것)  │
         └──────────────────┬───────────────────────┘
                            │ 설치해 줌
        ┌───────┬───────┬───┴───┬───────┬───────┐
        ↓       ↓       ↓       ↓       ↓       ↓
     Skill   Agent  Command   MCP    Hook   Mod/Plugin
     (877)   (422)   (287)   (103)   (62)    (25)
```

비유: **npm은 "패키지"가 아니라 "패키지를 설치하는 도구"**. 이것도 같다.

| 질문 | 답 | 근거 |
|---|---|---|
| 플러그인인가? | ❌ 본체는 아님. **플러그인/Mod를 배포** | `components/mods/` 25개, `.claude-plugin/marketplace.json` |
| 스킬인가? | ❌ 본체는 아님. **877개 스킬 창고** | `components/skills/` 31카테고리 |
| MCP인가? | ❌ 본체는 아님. **103개 MCP 설정 배포** | `components/mcps/` 13카테고리 |
| 정체는? | ✅ **npm CLI 도구 + 웹 카탈로그** | `package.json` → `bin: { claude-code-templates, cct }` |

**덧붙임**: `.claude-plugin/marketplace.json`이 있으므로
**Claude Code 마켓플레이스로 등록해 쓰는 경로**도 열려 있다.

---

## 10. API 토큰 필요 여부

**결론: 기본 사용에는 전혀 필요 없음.**

| 기능 | 토큰 | 종류 | 근거 |
|---|---|---|---|
| **부품 설치** (agent/command/mcp/hook/setting) | ❌ 불필요 | — | GitHub raw 공개 URL (`index.js:491`) |
| aitmpl.com 둘러보기 | ❌ 불필요 | — | 정적 사이트 |
| `--analytics` `--chats` `--health-check` `--plugins` | ❌ 불필요 | — | 로컬 로그만 읽음 |
| `--skill` `--mod` 설치 | ⚠️ 보통 불필요 | (GitHub) | `api.github.com` **비인증** → **시간당 60회 제한**. 초과 시 403 (`index.js:1474`) |
| **Claude Code 본체 실행** | ✅ 필요 | 구독 또는 `ANTHROPIC_API_KEY` | 저장소와 별개 |
| `--create-agent` | ✅ 필요 | `ANTHROPIC_API_KEY` | `index.js:2954` |
| `--sandbox e2b` / `--studio` | ✅ 필요 | `E2B_API_KEY` + `ANTHROPIC_API_KEY` | `index.js:2957`, `3632` |
| 설치한 **MCP 실제 사용** | ✅ 필요 | 해당 서비스 키 (`GITHUB_TOKEN`, DB DSN, Stripe 키…) | MCP JSON은 플레이스홀더만 제공 |

### 요약
```
부품 다운로드          → 토큰 0개 ✅
Claude Code 쓰기       → 구독 or API 키 (원래 필요한 것)
MCP로 외부 서비스 연결 → 그 서비스 키 (당연)
샌드박스/전역에이전트  → API 키 (고급 기능)
```

### rate limit 회피
```bash
export GITHUB_TOKEN=ghp_...   # 60 → 5,000회/시간
# (CLI가 직접 쓰진 않음. 웹에서 파일 직접 받아 복사해도 됨)
```

### 보안 체크
- MCP 설정에 **실제 키 없음** (`<your-database-dsn>` 플레이스홀더만)
- `CLAUDE.md`에 "비밀값 하드코딩 절대 금지" 명문화 + SkillSpector PR 검사
- 설치 후 본인이 `.mcp.json`에 키를 넣게 되므로 → **`.gitignore` 추가 또는 환경변수 참조** 필수

---

## 11. AI 에이전트 구축에 도움이 되는가

**매우 큰 도움. 단, "설치 편의" 때문이 아니다.**

### ① 422개 모범답안 교재 ⭐⭐⭐⭐⭐ (최대 가치)

실사용 검증된 프롬프트 422개가 전문 공개. 추출한 재사용 패턴:

| 패턴 | 내용 |
|---|---|
| **권한 최소화** | `tools: Read, Write, Edit, Bash, Glob, Grep` — 에이전트별 도구 제한 |
| **호출 조건 학습** | `<example>` + `<commentary>` — 긍정 예시와 "왜"를 같이 제공 → 자동 호출 정확도 상승 |
| **환경 자동 인식** | `` !`cat package.json \| grep jest` `` — 셸 결과를 프롬프트 주입 |
| **컨텍스트 선수집** | "Always begin by requesting project context from the context-manager" — 작업 전 코드베이스 파악 강제 |
| **실행 흐름 고정** | `1. Context Discovery → 2. Planning → 3. Implementation → 4. Verification` — 단계 못 박아 중간 생략 방지 |

### ② 자율 에이전트 설계 패턴 (Loops 18개) ⭐⭐⭐⭐⭐

에이전트 구축의 최난관은 **"언제 멈출지"**. `loops/`가 그 답.
```
목표(Goal) + 주기(Interval) + 종료조건(Stop Condition)
  + Perceive → Reason → Plan → Act → Observe
```
→ LangGraph·CrewAI·AutoGen 등 어떤 프레임워크에도 적용되는 **개념 설계서**.

### ③ 멀티에이전트 팀 구성 실례 ⭐⭐⭐⭐
```
deep-research-team / mcp-dev-team / ffmpeg-clip-team
podcast-creator-team / ocr-extraction-team / obsidian-ops-team
```
역할 분리 기준, 핸드오프 방식, 컨텍스트 전달 프로토콜이 코드로 존재.

### ④ 안전장치(Guardrail) 구현 실례 ⭐⭐⭐⭐

프로덕션 에이전트의 진짜 난관은 "똑똑하게"가 아니라 **"사고 안 치게"**.
```
hooks/quality-gates/   품질 미달 차단
hooks/security/        위험 명령 차단
hooks/pre-tool/        도구 실행 전 검증
mods/security/secret-redactor            비밀값 자동 마스킹
mods/observability/universal-audit-log   전 행동 감사 로그
mods/enterprise/admin-capability-lockdown 권한 잠금
```
→ **AI 에이전트 거버넌스**를 코드로 구현하는 법. 기업 납품 시 필수 요구사항.

### ⑤ AI로 AI를 운영하는 메타 구조 ⭐⭐⭐
`.claude/agents/` 15개 (6장 참조). 운영 자동화 설계를 그대로 참고 가능.

### ⚠️ 한계 (솔직하게)

| 한계 | 대응 |
|---|---|
| **Claude Code 종속** — LangChain 등엔 직접 사용 불가 | **프롬프트 내용·설계 패턴만** 추출 이식 |
| 프로그래밍 학습엔 한계 — 대부분 .md 텍스트 | 런타임은 Claude Agent SDK / LangGraph 공식 문서로 |
| 품질 불균일 — 877 스킬 중 다수가 외부 이식본 | **다운로드 수를 품질 지표로** 활용 |

### 결론
- "에이전트 **프레임워크** 교재"로는 ★★★☆☆
- "에이전트 **프롬프트/설계 패턴** 교재"로는 ★★★★★

에이전트 구축의 80%는 프롬프트 설계와 안전장치. 그 부분에서 **공개 자료 중 최상급 규모**.

---

## 12. React / PHP로 만들 수 있는가

### ① "비슷한 웹 카탈로그/플랫폼" → ✅ 가능, 오히려 쉽다

부품이 전부 텍스트 파일(.md/.json)이라 웹 쪽은 **평범한 CRUD 사이트**.

```
[부품 저장] .md 파일 → 폴더 or DB
[목록 API]  GET /api/components?type=agent
[검색]      제목/태그/본문 검색 (Meilisearch or pg_trgm)
[상세]      마크다운 → HTML 렌더
[설치]      "클립보드 복사" 버튼 (실제 설치는 사용자 터미널)
[통계]      설치 집계 테이블
```

**React/Next.js 안** (원본 Astro보다 쉬움 — 섬 구조·Cloudflare SSR 제약 없음)
```
Next.js 15 (App Router)
  ├─ app/components/page.tsx       목록 (서버 컴포넌트)
  ├─ app/component/[type]/[slug]   상세 (react-markdown)
  ├─ app/api/track/route.ts        설치 집계
  ├─ Prisma + PostgreSQL           메타 + 다운로드 카운트
  ├─ Meilisearch or pg_trgm        검색
  ├─ gray-matter                   프론트매터 파싱 ★핵심
  └─ Vercel 배포
```

**PHP/Laravel 안** (수익화 붙일 거면 오히려 유리)
```php
Route::get('/components', [ComponentController::class, 'index']);
Route::get('/component/{type}/{slug}', [ComponentController::class, 'show']);
Route::post('/api/track', [TrackController::class, 'store']);
// php artisan components:sync   ← .md 스캔 → DB 적재
```
```
Laravel 11 + Blade/Livewire or Inertia(+React)
  ├─ MySQL/PostgreSQL              부품 + 통계
  ├─ Laravel Scout + Meilisearch   검색
  ├─ spatie/yaml-front-matter      프론트매터 파싱 ★핵심
  ├─ league/commonmark             마크다운 렌더
  ├─ Laravel Cashier (Stripe)      결제 ★수익화
  └─ Breeze/Jetstream              인증·권한
```
> **Laravel 장점**: 결제·회원·권한·관리자 패널이 생태계에 다 있음.
> 수익화 모델을 붙일 거면 Astro보다 빠르다.

**난이도 (MVP)**
| 부분 | 난이도 |
|---|---|
| 목록/상세/검색 | 🟢 쉬움 |
| 프론트매터 파싱 | 🟢 쉬움 |
| 다운로드 통계 | 🟢 쉬움 |
| 설치 CLI (npm) | 🟡 보통 (Node 필수) |
| **콘텐츠 2,000개 확보** | 🔴 **가장 어려움 — 기술이 아니라 큐레이션 문제** |

### ② "CLI 설치 도구를 React/PHP로" → ⚠️ 가능하나 비권장

- Claude Code 사용자는 **100% Node.js 보유** (Claude Code 자체가 npm 패키지) → `npx` 마찰 0
- PHP CLI는 **PHP + Composer 추가 설치** 요구 → 아무도 안 씀
- React는 브라우저/UI 라이브러리로 CLI 부적합
- 원본도 성능 때문에 **Rust 포팅**(`cli-rust/`)을 시도. PHP/React 방향은 전혀 고려 안 함

**권장 조합**
```
설치 CLI    →  Node.js (npx 배포)          ← 이건 Node가 정답
웹 카탈로그  →  React/Next.js 또는 PHP/Laravel  ← 자유 선택
```

### ③ 가장 현실적인 전략 — CLI 없이 MVP

```bash
# 우리 사이트 "설치" 버튼이 복사해주는 명령어
curl -fsSL https://우리도메인.com/install/frontend-pro | bash
# 또는 더 단순하게
curl -o .claude/agents/frontend-pro.md https://우리도메인.com/raw/agents/frontend-pro.md
```
**MVP는 CLI 없이 성립.** 웹 + 다운로드 URL만 있으면 됨. 트래픽 확보 후 Node CLI 제작.

---

## 13. 유튜브 강의 영상 제작 가능성

**★★★★★ 매우 적합.** 지금 만들기 가장 좋은 소재 중 하나.

### 왜 적합한가

| 근거 | 내용 |
|---|---|
| ✅ 즉각적 Before/After | 명령 한 줄 → AI가 테스트 코드 생성. **영상화 최적** |
| ✅ 한국 수요 실증 | 다운로드 **한국 세계 3위(5.5만)** |
| ✅ 한국어 콘텐츠 희소 | aitmpl.com·문서·README 전부 영어 = **블루오션** |
| ✅ 무한한 소재 | 부품 2,000개 |
| ✅ 설치 장벽 0 | `npx` 한 줄 → 시청자 이탈 적음 |
| ✅ MIT 라이선스 | 화면에 띄우고 설명 자유 (출처 표기 권장) |
| ✅ 성장 토픽 | Claude Code 자체가 급성장 |

### 시리즈 구성안

**시즌 1 — 입문 (각 8~12분)**
```
EP1  "Claude Code 설정, 아직도 손으로 하세요?" — 훅 영상 (3분 Before/After)
EP2  설치부터 첫 에이전트까지 — npx 한 줄로 끝내기
EP3  Agent / Command / Skill, 뭐가 다른데?
EP4  다운로드 TOP 10 부품 전부 써보기
EP5  Hook으로 AI 사고 막기 — main 푸시 차단 실습
EP6  MCP 연결 — AI가 내 DB를 직접 보게 하기
EP7  팀 전체 AI 환경 통일 — .claude/ 커밋 전략
```

**시즌 2 — 실전 (각 15~20분)**
```
EP8   React 프로젝트 3분 세팅 (실시간 타이머)
EP9   Python/FastAPI 세팅
EP10  --analytics 로 토큰·비용 모니터링
EP11  --chats --tunnel 로 폰에서 작업 확인
EP12  --health-check 로 설정 진단
```

**시즌 3 — 심화 (전환율 최고 구간)**
```
EP13  422개 에이전트 분석: 프롬프트 패턴 5가지   ← ★최고 가치
EP14  Loop 엔지니어링: AI 자율 운영
EP15  나만의 에이전트 만들기 (메모장 10분)
EP16  오픈소스 기여: 내 부품을 2,000개 목록에 올리기  ← ★참여형
EP17  Mods로 터미널 개조 (+ 터미널 팩맨 🎮)         ← ★바이럴
EP18  우리 회사 전용 부품 창고 만들기 (Next.js/Laravel) ← ★개발자 타깃
```

### 제작 팁

**첫 15초 공식**
```
[0:00] 빈 터미널
[0:03] npx claude-code-templates@latest --agent ... --yes
[0:08] ✅ 설치 완료
[0:10] "이 코드 리뷰해줘" → AI 즉시 상세 리뷰
[0:15] "자, 이걸 어떻게 했는지 알려드립니다"
```

**가독성** — 폰트 18pt+, 다크 테마, D2Coding/JetBrains Mono. 모바일 시청 60%+ → 작은 글씨는 이탈 직결. `--analytics` 화면은 시각적으로 화려해 **썸네일 소재**로 좋음.

**썸네일 후킹**
```
"Claude Code 설정 2,000개 공짜"
"AI 코딩 세팅 1분 끝"
"한국이 세계 3위인 그 저장소"       ← 한국 특화
"터미널에서 팩맨이 돌아간다고?"      ← 바이럴
```

**수익화 연계**
```
무료 영상(유입) → 설명란 명령어 + 내 부품 창고 링크
               → "제가 쓰는 세팅 모음" 무료 배포 (이메일 수집)
               → 유료 강의 / 템플릿 팩 / 컨설팅 전환
```

### 주의사항
| 리스크 | 대응 |
|---|---|
| 버전 변화 빠름 | 영상에 날짜+버전 표기, "최신 명령어는 고정 댓글" |
| 영어 저장소 | 한국어 자막 + 설명란에 명령어 전문 |
| Mods 초기 접근 | "실험 기능, 변경 가능" 명시 |
| 선행 지식 | **EP0 "Claude Code 본체 설치 + 구독" 필수.** 없으면 "안 되는데요" 댓글 폭주 |
| 라이선스 | MIT. **원저작자(davila7) + aitmpl.com 출처 표기** 권장 |

---

## 14. 수익화 아이디어 10선

> 전제: 원본 **MIT 라이선스** → 상업적 이용·수정·재배포 자유.
> 단 **LICENSE 포함 + 저작권 고지 유지** 필수. 외부 이식 부품은 **각자 원 라이선스**(Apache 2.0, CC0 등) 개별 확인 필요.

### 한눈에 비교

| # | 아이디어 | 난이도 | 초기투자 | 수익화 속도 | 천장 | 추천 |
|---|---|---|---|---|---|---|
| 1 | 한국어 콘텐츠 (유튜브+강의) | 🟢 | 거의 0 | **빠름 1~3개월** | 중 | ⭐⭐⭐⭐⭐ |
| 2 | 버티컬 템플릿 팩 판매 | 🟡 | 낮음 | 빠름 1~2개월 | 중상 | ⭐⭐⭐⭐⭐ |
| 3 | 기업 AI 코딩 도입 컨설팅 | 🟡 | 0 | **매우 빠름** | **상** | ⭐⭐⭐⭐⭐ |
| 4 | 사내 프라이빗 부품 창고 (SaaS/납품) | 🔴 | 중간 | 3~6개월 | **상** | ⭐⭐⭐⭐ |
| 5 | AI 거버넌스/감사 도구 | 🔴 | 중간 | 느림 | **상** | ⭐⭐⭐⭐ |
| 6 | 팀 관리 대시보드 SaaS | 🔴 | 중상 | 6개월+ | **상** | ⭐⭐⭐ |
| 7 | 유료 마켓플레이스 (수수료) | 🔴 | 중간 | 느림 | 중상 | ⭐⭐⭐ |
| 8 | 부품 제작 대행 / 외주 | 🟢 | 0 | **즉시** | 중 | ⭐⭐⭐⭐ |
| 9 | 서비스 연계 어필리에이트 | 🟢 | 낮음 | 중간 | 중 | ⭐⭐⭐ |
| 10 | 오픈소스 스폰서십 | 🟢 | 0 | 느림 | 하 | ⭐⭐ |

---

### 🥇 1위. 한국어 교육 콘텐츠 → 유료 강의 퍼널

**왜 1위**: 한국 다운로드 3위(수요 실증) + 한국어 해설 공백 + 초기 투자 0

```
[무료] 유튜브 시리즈 (유입)
   ↓ 설명란 → 랜딩 페이지
[무료] "제가 쓰는 세팅 모음" PDF/명령어집   ← 이메일 수집
   ↓
[유료1] 인프런/클래스101/Udemy 강의        5~15만원 × N명
[유료2] Gumroad/노션 템플릿 팩             3~10만원
[유료3] 유료 멤버십 (주간 부품 업데이트 +
        디스코드 Q&A + 월 1회 라이브)      월 1~3만원 × N명
   ↓
[고액] 기업 사내 교육 / 컨설팅             회당 100~500만원
```

**보수적 시나리오**
```
3개월차 : 구독 1,000명, 강의 출시 전 → 0~50만원 (애드센스)
6개월차 : 강의 200명 × 7만원 = 1,400만원 (수수료 제외 ~1,000만원)
12개월차: 멤버십 150명 × 월 2만원 = 월 300만원 경상 + 기업 교육 문의
```

**차별화 필수**: 단순 "사용법"은 금방 레드오션.
**"422개 에이전트를 전수 분석한 프롬프트 설계 패턴"**처럼
**이 저장소를 데이터로 쓴 분석 콘텐츠**가 방어선. 아무도 2,000개를 다 읽지 않는다.

---

### 🥇 2위. 버티컬(업종 특화) 템플릿 팩 판매

**인사이트**: 원본 2,000개는 **범용**. "우리 업종엔 안 맞는다"는 불만이 반드시 생긴다. 그 틈이 시장.

```
📦 "전자상거래 개발 팩"           149,000원
   에이전트 8 (결제연동/재고/상품검색/추천/주문플로우)
   명령어 12 (/checkout-audit, /inventory-sync, /pg-test)
   훅 5 (결제코드 변경 시 강제 리뷰, PCI-DSS 체크)
   MCP (Stripe/토스/아임포트/PortOne) + CLAUDE.md + 가이드 PDF

📦 "금융/핀테크 규제 준수 팩"     399,000원  ← 고단가
   전자금융감독규정 체크 에이전트 / 개인정보 감사 훅(강제 차단)
   감사로그 Mod(금융권 필수) / 보안 리뷰 체크리스트 + 증빙

📦 "한국 공공/SI 프로젝트 팩"     249,000원  ← 국내 특화, 경쟁 無
   전자정부 프레임워크 대응 / KWCAG 접근성 검사
   산출물 자동 생성(제안서/설계서/테스트시나리오) / 감리 대응

📦 "React/Next.js 프로 팩"        99,000원
📦 "Laravel/PHP 레거시 현대화 팩" 149,000원
📦 "데이터 엔지니어링 팩"         179,000원
```

**가격 정당화**: 개발자 1인 하루 인건비 20~40만원.
**"이틀 세팅 시간 절약"이면 15만원은 즉시 회수.**

**채널**: Gumroad / Lemon Squeezy / 자체 사이트(Laravel Cashier) / 크몽 / 노션 마켓

**실행 순서**: 유튜브로 신뢰 → 자신있는 버티컬 1개만 먼저 검증 → 무료 샘플 3개 + 유료 20개 묶음 → 확장

---

### 🥇 3위. 기업 AI 코딩 도입 컨설팅 (단가 최강)

**현실**: 많은 기업이 "AI 코딩 도입하라" 지시만 받고 방법을 모른다.
- 보안팀: "소스코드 유출하면?" → **Hooks/Mods 차단 증빙 필요**
- 경영진: "효과가 있나?" → **--analytics 데이터 증빙 필요**
- 개발팀: "뭘 어떻게?" → **표준 세팅 + 교육 필요**

**이 저장소가 세 질문의 답을 모두 갖고 있다. 그것이 컨설팅 상품이 된다.**

```
🔹 진단 (1주)              300~500만원
   프로세스 분석 / --health-check 환경 진단 / 보안 리스크 + 로드맵

🔹 구축 (2~4주)            1,000~3,000만원
   사내 전용 부품 창고 (4위와 결합)
   회사 컨벤션 → 에이전트/CLAUDE.md 코드화
   보안 훅 세팅 (비밀값·금지명령 차단, 감사 로그) / CI/CD 연동

🔹 교육 (1~2일)            200~500만원/회
   개발자 실습 워크샵 / 팀 리드 운영 교육

🔹 운영 지원 (월 구독)      월 100~300만원
   부품 업데이트, Q&A, 분기 리뷰
```

**영업 근거 자료 (저장소 내 실물)**
- `hooks/security/`, `hooks/quality-gates/` → "AI 행동 강제 통제 가능" 증빙
- `mods/observability/universal-audit-log` → "전 행동 감사 로그" (금융·공공 필수)
- `mods/enterprise/admin-capability-lockdown` → "권한 잠금 가능"
- `CHANGELOG.md` CVE 패치 이력 → 생태계 신뢰도
- SkillSpector 파이프라인 → "부품 보안 검증 체계" 설명

**초기 투자 0원. 유튜브로 전문성 증명 → 인바운드 문의.
현실적으로 가장 빠르게 큰 돈이 되는 경로.**

---

### 🥈 4위. 사내 프라이빗 부품 창고 (구축 납품 + SaaS)

**핵심**: 기업은 **사내 노하우를 공개 저장소에 올릴 수 없다.** 하지만 사내 표준을 AI에게 가르치려면 창고가 필요하다 → **프라이빗 수요 확실**.

```
[사내 Git] 회사 전용 에이전트/명령어/훅 (.md)
     ↓
[우리 제품] 카탈로그 웹 + 검색 + 설치 CLI + 사용 통계
     ↓
[개발자] cct-회사명 --agent our-standards/api-guide --yes
```

**가치**: 컨벤션·아키텍처 결정·도메인 지식을 AI가 자동 준수 / 신입 온보딩 단축(측정 가능 ROI) / 팀별 통계로 도입 효과 증빙

```
구축 납품형: 초기 2,000~5,000만원 + 유지보수 연 20%
SaaS 형    : Starter 10인 월 30만원
             Team    50인 월 100만원
             Enterprise 온프레미스 연 3,000만원+
```

**스택**
```
React 안 : Next.js 15 + Prisma + PostgreSQL + Meilisearch + Clerk
PHP 안   : Laravel 11 + Livewire + Scout + Cashier + Jetstream
           ← 결제·권한·관리자패널 완비. SaaS엔 Laravel이 빠름
설치 CLI : Node.js (npm 사설 레지스트리 or GitHub Packages)
```

**MVP 범위 (3개월)**: .md 스캔→DB 적재 / 목록·검색·상세 / 설치 명령어 복사 / 집계 테이블. **CLI는 나중** (curl 스크립트 대체).

---

### 🥈 5위. AI 거버넌스 / 감사 도구 (가장 저평가된 기회)

**시장 논리**: AI 코딩이 퍼지면 **반드시** 규제·감사 요구가 따라온다.
금융·의료·공공은 이미 "AI가 코드를 고쳤다면 누가 승인했나?"를 묻기 시작했다.

```
📦 "AI 코딩 감사·통제 스위트"
   ├─ 전 행동 감사 로그 (누가/언제/어떤 AI가/무엇을)
   ├─ 비밀값 유출 실시간 차단 (secret-redactor 확장)
   ├─ 금지 명령 차단 (rm -rf, 프로덕션 DB, 외부 전송)
   ├─ 변경 승인 워크플로 (human-approval-loop 확장)
   ├─ 규제 매핑 리포트 (ISMS-P / 전자금융감독규정 / SOC2)
   └─ 관리자 대시보드 (팀별 사용 현황 + 위반 이력)
```

**가격**: 연 2,000만~1억원 (규제 산업 컴플라이언스는 고단가)
**기회**: 경쟁자 거의 없음 + **Hook/Mod 구현체가 이미 오픈소스로 참고 가능** → 0부터 안 만들어도 됨
**리스크**: 영업 사이클 6~18개월, 레퍼런스 필요 → **3위 컨설팅으로 첫 고객 확보 후 제품화**가 안전

---

### 🥈 6위. 팀 관리 대시보드 SaaS

원본 `--analytics`, `--teams`는 **개인용 로컬 도구**. **팀/조직 집계가 공백** → 제품 기회.

```
개인 CLI(로컬) → [우리 SaaS: 팀 집계 서버]
                  ├─ 팀/프로젝트별 토큰·비용 집계 + 예산 알림
                  ├─ 개발자별 AI 활용도
                  ├─ 어떤 에이전트가 효과적인가 (A/B)
                  ├─ 비용 최적화 제안
                  └─ Slack/Jira 연동
```
**가격**: 좌석당 월 1~3만원
**난관**: 수집 에이전트 배포, 프라이버시 동의, 기업 보안 심사 → 6개월+
**⚠️ 주의**: "개발자 감시 도구" 포지셔닝은 내부 저항으로 실패.
**"비용 최적화 + 생산성 지원"** 프레이밍 필수.

---

### 🥈 7위. 유료 마켓플레이스 (제작자 수수료)

```
제작자 유료 부품 업로드 → 우리 플랫폼 판매 → 수수료 20~30%
                                       ├─ 결제/정산
                                       ├─ 품질·보안 검증 (SkillSpector)
                                       └─ 라이선스 관리 (좌석제)
```
**장점**: 플랫폼이 되면 천장 높음
**난관**: **치킨-에그** + "무료 2,000개가 있는데 왜 사야 하나" 극복 필요
→ **2위(자체 유료 팩)로 "유료 부품이 팔린다"를 먼저 증명한 후 개방**

---

### 🥉 8위. 부품 제작 대행 / 외주 (수익화 속도 1위)

```
크몽 / 위시켓 / Upwork / 링크드인

"귀사 코딩 컨벤션을 Claude Code 에이전트로 만들어 드립니다"
  에이전트 1개 제작                      30~80만원
  팀 표준 세트 (에이전트+훅+CLAUDE.md)   200~500만원
  레거시 분석 후 맞춤 세팅                500만원+
```
**투자 0원, 오늘 당장 가능.** 3위 컨설팅의 축소판이자 레퍼런스 입구.

### 🥉 9위. 서비스 연계 어필리에이트

원본도 이미 수행 (Bright Data 스폰서, Neon·Z.AI 배지).
```
콘텐츠/템플릿에 연계 서비스 추천
 → Supabase, Neon, Vercel, E2B, Bright Data, Resend, Clerk
 → 가입당 또는 매출 20~30% 수수료
```
**단, 신뢰가 핵심.** 실제 쓰는 것만 추천 + **어필리에이트 명시**.

### 🥉 10위. 오픈소스 스폰서십

자체 창고를 공개 운영 → GitHub Sponsors / Buy Me a Coffee / Open Collective.
금액은 작지만 **신뢰 자산**이 되어 1~3위 수익을 끌어올린다.

---

## 15. 실행 로드맵

```
━━ 0~3개월: 신뢰 자본 (투자 거의 0) ━━
① 유튜브 한국어 시리즈 시작 (주 1편)
   └ EP13 "422개 에이전트 프롬프트 패턴 분석" 같은 차별화 콘텐츠 필수
② 무료 "내 세팅 모음" 배포 → 이메일 리스트 구축
③ 자체 부품 20~30개 제작 (GitHub 공개 → 포트폴리오)
④ 크몽/링크드인 제작 대행 등록 (8위) → 첫 현금
   💰 월 0~300만원

━━ 3~6개월: 제품화 ━━
⑤ 버티컬 팩 1개 완성 → Gumroad 판매 (2위)
⑥ 강의 1개 출시 (인프런/클래스101)
⑦ 유튜브 → 컨설팅 인바운드 대응 (3위)
   💰 월 300~1,500만원

━━ 6~12개월: 스케일 ━━
⑧ 버티컬 팩 3~5개 확장
⑨ 기업 컨설팅 2~3건 수주 → 레퍼런스 확보
⑩ 프라이빗 부품 창고 MVP (4위, Next.js or Laravel)
   💰 월 1,000~5,000만원

━━ 12개월+: 플랫폼 ━━
⑪ 프라이빗 창고 SaaS 전환
⑫ 거버넌스 스위트 제품화 (5위) ← 규제 산업 고단가
⑬ (선택) 마켓플레이스 개방 (7위)
```

### 최종 권고

**가장 안전하고 빠른 길: 1위(콘텐츠) → 8위(대행) → 2위(템플릿 팩) → 3위(컨설팅)**

이유
- 초기 투자 거의 없어 **실패 비용이 작다**
- 각 단계가 다음 단계의 **신뢰 자본**이 된다 (유튜브 → 문의 → 레퍼런스 → 고단가)
- 기술 리스크(Anthropic 정책 변경)에 **덜 취약하다**

**비권장**: 6위(SaaS)·7위(마켓플레이스)를 처음부터 시작하는 것.
투자가 크고, 트래픽·신뢰 없이는 치킨-에그에 걸린다.

---

## 16. 주의사항 및 리스크

### 사용 시
| 항목 | 내용 |
|---|---|
| **Claude Code 전용** | Cursor·Copilot·ChatGPT에서 그대로 쓸 수 없음 (프롬프트 텍스트는 참고 가능) |
| **익명 통계 기본 켜짐** | `CCT_NO_TRACKING=true` 로 비활성화 |
| **품질 불균일** | 877 스킬 중 상당수 외부 이식본. **다운로드 수가 사실상 품질 지표** |
| **Mods는 초기 접근** | API 변경 가능. 프로덕션 의존 주의 |
| **`--studio` 보안** | 과거 RCE 취약점(CVSS 8.8) → **최신 버전 유지 필수** |
| **MCP 키 관리** | 외부 MCP의 API 키는 본인 발급·관리. `.mcp.json`은 `.gitignore` 또는 환경변수 참조 |
| **오프라인 불가** | 부품은 설치 시점에 GitHub에서 fetch |
| **GitHub rate limit** | `--skill`/`--mod` 대량 설치 시 시간당 60회 초과하면 403 |

### 수익화 시
| 항목 | 내용 |
|---|---|
| **라이선스** | 원본 MIT → 상업적 이용 OK. **LICENSE + 저작권 고지 반드시 포함.** 외부 이식 부품(Apache 2.0, CC0 등)은 **개별 확인** |
| **출처 표기** | "davila7/claude-code-templates 기반" 명시. 법적 의무이자 신뢰 자산 |
| **무료 부품 재판매 금지** | 공개 부품 그대로 판매는 평판 즉사. **새로 만든 것 + 큐레이션 + 가이드**를 판매 |
| **보안** | 고객 환경에 비밀값 하드코딩 절대 금지. SkillSpector 같은 스캔 도입 |
| **버전 추적** | Claude Code가 빠르게 변함. 유료 제품은 **업데이트 보장 기간 명시** |
| **의존 리스크** | Anthropic이 공식 마켓플레이스를 강화하면 "설치 편의" 가치가 줄어듦 → **콘텐츠·컨설팅·거버넌스처럼 "사람과 노하우" 기반 모델이 더 안전** |

---

## 17. 참고 링크 모음

### 본 저장소
| 항목 | 링크 |
|---|---|
| **원본 저장소** | https://github.com/davila7/claude-code-templates |
| **분석 대상 포크** | https://github.com/bmshin94/claude-code-templates |
| 공식 사이트 (부품 탐색) | https://aitmpl.com |
| 공식 문서 | https://docs.aitmpl.com |
| npm 패키지 | https://www.npmjs.com/package/claude-code-templates |
| GitHub Discussions | https://github.com/davila7/claude-code-templates/discussions |
| GitHub Issues | https://github.com/davila7/claude-code-templates/issues |
| 기여 가이드 | https://github.com/davila7/claude-code-templates/blob/main/CONTRIBUTING.md |

### 관련 생태계
| 항목 | 링크 |
|---|---|
| Claude Code 공식 | https://github.com/anthropics/claude-code |
| Claude Code 문서 | https://code.claude.com/docs |
| Anthropic 공식 스킬 | https://github.com/anthropics/skills |
| Claude Mods 레퍼런스 | https://github.com/anthropics/claude-code/tree/main/mods |
| Mods 논의 이슈 | https://github.com/anthropics/claude-code/issues/91870 |
| NVIDIA SkillSpector (보안 스캐너) | https://github.com/NVIDIA/skillspector |

### 주요 출처 부품 (원 저작자)
| 출처 | 링크 | 라이선스 |
|---|---|---|
| K-Dense AI 과학 스킬 139개 | https://github.com/K-Dense-AI/claude-scientific-skills | MIT |
| obra/superpowers 워크플로 14개 | https://github.com/obra/superpowers | MIT |
| alirezarezvani 직무 스킬 36개 | https://github.com/alirezarezvani/claude-skills | MIT |
| wshobson 에이전트 48개 | https://github.com/wshobson/agents | MIT |
| awesome-claude-code 명령어 21개 | https://github.com/hesreallyhim/awesome-claude-code | CC0 1.0 |
| awesome-claude-skills | https://github.com/mehdi-lamrani/awesome-claude-skills | Apache 2.0 |

---

## 부록: 자주 쓰는 명령어 빠른 참조

```bash
# 설치 (대화형)
npx claude-code-templates@latest

# 인기 TOP 조합 한 번에
npx claude-code-templates@latest \
  --agent development-team/frontend-developer,development-tools/code-reviewer,development-team/backend-architect \
  --skill creative-design/frontend-design,development/code-reviewer,development/senior-frontend \
  --command testing/generate-tests,utilities/ultra-think,git-workflow/commit \
  --mcp devtools/context7 \
  --hook automation/simple-notifications \
  --setting statusline/context-monitor \
  --yes

# 진단
npx claude-code-templates@latest --health-check

# 분석 대시보드
npx claude-code-templates@latest --analytics

# 모바일에서 확인
npx claude-code-templates@latest --chats --tunnel

# 통계 수집 끄기
export CCT_NO_TRACKING=true
```

---

*이 문서는 2026-10-08 기준 저장소 전수조사 결과입니다.
Claude Code 생태계는 빠르게 변하므로, 최신 정보는 위 공식 링크를 확인하세요.*
