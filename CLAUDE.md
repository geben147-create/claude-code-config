# Global Claude Code Context

## Workspace Overview
This is the home directory. Main projects:
- `kimrag/` - Side-Car RAG engine for legal/medical compliance (FastAPI + LangGraph + Qdrant)
- `ai-workspace/` - AI experimentation workspace
- `n8n_selfhost/` - Self-hosted n8n automation
- `mcp-http-bridge/` - MCP HTTP bridge
- `.n8n-mcp/` - n8n MCP server config

## General Preferences
- Language: Korean for communication, English for code/comments
- Package managers: uv (Python), bun (JS/TS)
- Containerization: Docker Compose for all services
- AI tools: Claude Code, n8n, MCP servers

## MCP 서버 목록 (설치됨)
| 서버 | 역할 | 사용 시점 |
|------|------|----------|
| `context7` | 라이브러리 공식 문서 실시간 조회 | 패키지 API 모를 때, 최신 문서 확인 |
| `n8n-mcp` | n8n 워크플로우 생성/수정/실행 | n8n 자동화 작업 시 (localhost:5678 연결) |

## 모델 선택 기준
| 작업 | 모델 | 이유 |
|------|------|------|
| 설계/아키텍처 결정, 대본 작성, 핵심 품질 결정 | **Opus** | 비용 높지만 이 단계가 최종 품질의 80% 결정 |
| 일반 개발, 코드 생성, 리팩터링 | **Sonnet** | 기본값, 속도/품질 균형 |
| 단순 변환, 빠른 반복, 기계적 작업 | **Haiku** | 속도·비용 절감 |

## Communication Rules
- User action required items: 🔴 필수 / 🟡 선택 labels
- Be concise, go straight to action
- Auto-execute everything possible via CLI without asking
- Only ask when user input (API keys, credentials, choices) is truly needed

## Voice Notification (음성 알림 — 필수!)
- **모든 작업 완료 시** 반드시 음성 알림을 실행할 것
- 포맷: "N번째 채팅, XXX 작업 완료!"  (N = 해당 대화의 턴 번호)
- 실행 방법:
  1. `~/scripts/notify_msg.txt`에 메시지 Write
  2. `powershell -ExecutionPolicy Bypass -File ~/scripts/notify.ps1 -MessageFile ~/scripts/notify_msg.txt` 실행
- 작업 내용을 구체적으로 한국어로 설명할 것
- 예시: "3번째 채팅, FastAPI 엔드포인트 추가 및 테스트 통과 완료!"

## Plan First, Code Later (울트라띵크 기본 적용)
- **모든 개발 작업에 울트라띵크 방법론 자동 적용** (명령어 호출 불필요)
- 사용자가 뭔가 만들어달라고 하면 → 바로 코딩 시작 금지
- 반드시 이 순서를 따를 것:
  1. **Tech Spec 먼저**: 목표, 안할것, 요구사항 정리 → 사용자 승인
  2. **Plan 작성**: 마일스톤 분해 → 사용자 승인
  3. **Execute**: 테스트 먼저 → 최소 코드 → 검증 → 커밋
- 한 마일스톤 = 한 세션 = 한 커밋, 변경 ~100줄 이하
- After planning, self-review: "시니어 엔지니어라면 이 계획의 허점은?"
- Only proceed to implementation after plan is approved
- 큰 프로젝트: /gsd:plan-phase, 중간: 울트라띵크 자동, 작은거: /gsd:fast
- 예외: 한 줄 수정, 간단한 질문, 버그 핫픽스는 바로 진행 OK

## Verification Loop (검증 루프 — 가장 중요!)
- → 울트라띵크 Execute 단계에 통합됨 (테스트 먼저 → 검증 → 반복)
- Never mark a task complete without verification
- Use /gsd:verify-work for conversational UAT

## Mistake Recording (실수 기록)
- When a bug or mistake occurs, record the lesson in project CLAUDE.md
- Format: `## Lessons Learned` section with date and what to avoid
- This prevents the same mistake from repeating across sessions

## Token Saving Rules
- NEVER read entire codebase at once. Only read files you need to modify.
- Use Glob/Grep to find specific files instead of reading directories recursively.
- Use subagents for independent tasks — each gets its own context window.
- When exploring unfamiliar code, use Agent(subagent_type="Explore") instead of reading every file.
- Prefer Edit over Write for existing files — sends only the diff.
- Keep responses concise. No trailing summaries unless asked.
- Use /compact when conversation gets long (auto-reminded by hooks).
- For simple tasks, use /gsd:fast or /gsd:quick instead of full GSD workflow.
- Batch independent tool calls in parallel to reduce round trips.

## Multi-Project Workflow
Each project lives in its own directory with its own CLAUDE.md:
```
~/project-name/
├── CLAUDE.md           ← project-specific rules, architecture, conventions
├── .planning/          ← GSD planning artifacts (auto-managed)
├── src/                ← source code
└── ...
```
When switching projects:
- Always read that project's CLAUDE.md first
- Use /gsd:progress to check where we left off
- Use /gsd:resume-work for context restoration

## Git Branch Strategy
- **main 브랜치에 직접 커밋 절대 금지**
- 새 기능 개발 시 반드시 `feature/기능명` 브랜치 생성 후 작업
- 버그 수정은 `fix/버그명` 브랜치에서 작업
- 의미 있는 단위마다 커밋 (한꺼번에 대량 커밋 금지)
- 작업 완료 후 PR 생성 → main에 merge
- 브랜치 네이밍: `feature/`, `fix/`, `refactor/`, `ui/` 접두사 사용
- 실험적 작업은 `experiment/` 브랜치에서 — 실패 시 브랜치만 삭제

## Parallel Work (병렬 작업)
- Use multiple terminals/worktrees for independent tasks
- Split large features into parallel-executable sub-tasks
- Use Agent tool with subagents for independent research/implementation
- Use /gsd:workstreams for managing parallel work

## Skill & Automation Rules
- If a task is repeated 2+ times per day → make it a custom skill
- Skills accumulate over time = long-term productivity asset
- Keep skills in ~/.claude/skills/ (global) or project/.claude/commands/ (project)

## Development Standards
- Git: commit after each meaningful change, descriptive messages in English
- Testing: write tests for critical paths, use /gsd:add-tests for phase completion
- Error handling: validate at system boundaries only, trust internal code
- No premature abstractions — build what's needed now
- Docker Compose for all service orchestration

## Atomic Task Principle
- → 울트라띵크 마일스톤 단위에 통합됨 (1 마일스톤 = 1 커밋, ~100줄, TDD)
- Use CLAUDE.md in each project for project-specific context

## Harness Engineering (하네스 엔지니어링)

> 전체 가이드: `~/ai-workspace/harness-engineering/`
> 비교표 + 스토리: `~/ai-workspace/harness-engineering/COMPARISON.md`
> GitHub 백업: https://github.com/geben147-create/harness-engineering
> 비개발자 사용가이드: `~/ai-workspace/harness-engineering/USAGE-GUIDE.md`

### 하네스 v1 — 팀/납품 프로젝트용
핵심: **멱등성** — Claude든 GPT든 Gemini든 → 동일한 코드/문서 아웃풋
6단계: 요구사항 → 플랜 → CPS문서 → 설계 → 코드+린터 → 이밸루에이션
린터: ruff(Python)/biome(TS), pre-commit hook 자동 검사

### 하네스 v2 — AI 에이전트 시스템 운영
핵심: AI의 2가지 근본 실패를 구조로 막음
- **컨텍스트 불안**: 대화 길어지면 AI가 앞 내용 망각 → 컨텍스트 리셋 훅
- **자기평가 편향**: AI가 자기 실수를 "괜찮다"고 합리화 → 별도 에이전트 검증

#### GAN 구조 (기획/제작/검증 분리)
```
Planner   → 브리프를 상세 스펙으로 변환
Generator → 스프린트 단위 구현
Evaluator → 별도 에이전트가 테스트 (Playwright MCP)
           → 실패 시 Generator 재작업 → 반복
```

#### AI 에이전트 운영 규칙
- **보안 훅 필수**: salary/급여/개인정보 키워드 → 즉시 차단
- **완료 선언 차단**: "완료했습니다" 발동 시 → 별도 섹션에서 재검증
- **브라우저 라우팅**: localhost → Playwright MCP / 외부URL → browser-use
- **에이전트 자동 라우팅**: 키워드 감지 → 해당 전문 에이전트 자동 호출
  - 세금/부가세 → 재무 에이전트 / 계약서 → 법무 에이전트 / 채용 → HR 에이전트
- **품질 키워드**: Evaluator 기준에 "museum quality" 등 추가 시 아웃풋 수준 급상승

#### 핵심 원칙 3가지 (Anthropic 공식)
1. 기획 / 제작 / 검증을 반드시 분리한다
2. AI가 하면 안 되는 것을 규칙(훅)으로 막는다
3. 피드백 루프를 만든다 (만들기 → 테스트 → 수정 → 반복)

#### 평가 기준표 (루브릭) 원칙 — 실전 핵심
- **AI 약점 = 높은 비중**: 디자인 40%, 독창성 30%, 기술 15%, 기능 15%
- **기준표는 Generator도 읽는다**: 채점 기준 = Generator의 목표 설정
- **기준표 문구가 결과를 바꾼다**: "AI Slop 불합격" 명시 → AI가 처음부터 다르게 만듦
- **AI Slop 불합격 기준**: Bootstrap 기본, 뻔한 히어로, 회색 사각형 → 자동 불합격

#### 승인 게이트 (Approval Gate) — 창작 단계 인간 개입
```
기계적 단계 (계산, 변환, 코딩)    → 자동 Evaluator (규칙 기반)
창작적 단계 (글쓰기, 디자인, 방향) → 승인 게이트 (사람 확인)
```
- 완전 자동이 항상 좋은 게 아님 — 창작 작업은 의도적 반자동이 품질 높음
- 에러 복구 원칙: **실패 단계부터만 재시작** (처음부터 X)

#### 하네스 구성요소 5요소 (실전 체크리스트)
| 요소 | 내용 |
|------|------|
| **역할 정의** | 각 에이전트에게 주는 책임과 역할 범위 |
| **입출력 형식** | 입력받는 형태 + 출력하는 형태 표준화 |
| **DO/DON'T 목록** | 해야 할 것 / 하지 말아야 할 것 명시 |
| **승인 게이트** | 어느 시점에 사람이 개입할지 정해놓은 체크포인트 |
| **캐릭터 가이드** | 이 채널/서비스의 톤, 화법, 정체성 |

#### 캐릭터 가이드 원칙
- 에이전트에게 톤/화법/정체성 명시 → 아웃풋 일관성 급상승
- DO/DON'T 리스트로 작성 (예: 근거 없는 위기감 조성 금지)
- 전문 용어 번역 규칙 포함 (예: API → "주문 전달 경로")

#### 컨텍스트 리셋 실천법
```
/clear  ←  대화가 길어지기 전에 치기
CLAUDE.md에 "현재 상태: ~완료, 다음: ~" 기록  ←  인수인계 메모
```
이게 "교대 근무 패턴" — AI가 항상 맑은 정신으로 시작하게 만든다

#### CLAUDE.md = 오케스트레이터
이 파일 자체가 Planner→Generator→Evaluator 파이프라인을 지휘하는 지휘관

#### 모델 발전에 따른 단순화 원칙
새 모델이 나올수록 하네스는 더 단순해진다 — 보조 장치를 하나씩 제거
But: 더 어려운 과제 도전 시 → 새로운 하네스 항상 필요 (하네스 공간이 이동할 뿐)

### 하네스 v3 이론 — AI 환경 설계 철학
핵심: **"모델이 CPU라면 하네스는 OS다"**
- AI의 2가지 근본 실패: 컨텍스트 불안증 + 자기평가 편향 → 구조로 해결
- 황금률: 가장 단순하게 시작, 실패 지점에서만 규칙 추가
- 오답노트 원칙: AI 실수 → 채팅 잔소리 ❌ → CLAUDE.md 규칙 추가 ✅

### 하네스 v4 — 영속성 에이전트 원칙

#### 핵심 개념: AI의 2가지 근본 한계
| 한계 | 증상 | 해결 |
|------|------|------|
| **기억의 휘발성** | 창 닫으면 문제 해결 방법론 자체 초기화 | 절차적 스킬 문서로 성공 패턴 저장 |
| **실행 환경 단절** | AI가 코드 짜줘도 적용은 인간이 직접 | 터미널 직접 제어 + Docker 샌드박스 |

#### React 루프 — 디버깅/실행 원칙
에러 발생 시 이 순서를 따른다:
```
1. 관찰(Observe): 에러 로그 전체 읽기
2. 추론(Reason):  원인 파악 ("pandas 버전 충돌이다")
3. 실행(Act):     명령어 직접 실행 (pip install --upgrade pandas)
4. 반복:          결과 관찰 → 다음 추론 → ...
```
- 에러 만나자마자 포기하거나 사용자에게 묻지 않는다
- 관찰→추론→실행 루프를 최소 3회 돌린 후에 막히면 물어본다

#### 스킬 문서 = 나의 진짜 자산
> "수십억 달러 모델보다 내 경험을 담은 스킬 문서가 나에게는 더 가치 있다"

- 모델은 언제든 교체 가능 (GPT→Claude→Gemini), 스킬 문서는 대체 불가
- 성공한 작업 → 스킬로 문서화 (재현 가능하게)
- 실패한 작업 → Lessons Learned에 기록 (반복 방지)
- CLAUDE.md 자체가 이 스킬 문서의 집합체

#### 자기 진화 루프 (GPA 원칙)
```
작업 실행 → 결과 분석
    ↓ 실패        ↓ 성공
오답 노트 기록  스킬 문서 작성
    ↓               ↓
다음에 안 반복   다음에 재사용
```

#### Telegram 게이트웨이 패턴
- 이미 `~/telegram-claude/` 프로젝트 있음
- 활용: 외출 중 → 텔레그램으로 에이전트에게 작업 지시 → 결과 보고 수신
- 적합한 작업: 서버 모니터링, 백그라운드 태스크 실행, 빌드/배포 트리거

#### Docker 샌드박스 원칙
- AI가 직접 코드 실행 시 → 반드시 Docker 컨테이너 안에서
- 핵심 데이터/서버는 컨테이너 외부로 마운트 금지
- 실험적 작업 = 컨테이너 안 놀이터, 프로덕션 = 컨테이너 바깥

#### 3대 에이전트 비교
| 에이전트 | 설정파일 | 특징 |
|---------|---------|------|
| Claude Code | CLAUDE.md | Hook 기반, 팀 협업 최적 |
| OpenAI Codex | AGENTS.md | Wiggum Loop (무한 반복) |
| Cursor | .cursorrules/ | 비동기 백그라운드, 방해 없음 |

#### 실증 데이터 (하네스만 최적화, 모델 그대로)
- 동일 모델 + 하네스 최적화 → 정확도 +13.7점 (LangChain 벤치마크)
- 포맷 단순화만으로 → 성공률 6.7% → 67% (10배)
- 도구 15개→2개 축소 → 정확도 100%, 비용 37% 절감

### 언제 무엇을 쓰나
```
개인 단일 작업          → 울트라띵크
AI 에이전트 운영         → 하네스 v2 (GAN 구조)
팀/납품 프로젝트         → 하네스 v1 (린터 + 문서화)
이론/철학 이해           → 하네스 v3
영속성 에이전트 / 자동화  → 하네스 v4 (React 루프 + 스킬 자산화)
모든 상황               → 황금률: 단순하게 시작 → 실패 보면서 규칙 추가
```

### 스킬 명령어 (한국어)
```
/자막-학습        ← 새 자막 학습 → 자동 분석/통합/저장/GitHub 푸시  ★자막 받으면 바로
/개발-방식        ← 새 프로젝트 시작 전 어떤 방법 쓸지 대화로 결정  ★모르면 여기서 시작
/하네스-시작      ← 새 프로젝트 하네스 초기화
/하네스-6단계     ← 현재 단계 진단 + 다음 단계 안내  ★내가 지금 몇 단계인지
/하네스-오답노트  ← AI 실수를 규칙으로 기록 (가장 중요!) ★잔소리 대신 규칙 추가
/하네스-상태확인  ← 현재 하네스 설정 상태 확인
/하네스-현황      ← 전체 하네스 현황 한눈에 보기
/하네스-품질검사  ← 아웃풋 품질 검사 실행
/에이전트-GAN설계 ← Planner/Generator/Evaluator 구조 설계
```

### 스킬 은퇴 원칙 (Skills Retirement)
- **은퇴 조건**: 모델이 업그레이드되어 스킬 없이도 동일한 결과 → 해당 스킬 삭제
- **채점 기준표 (LLM-as-a-Judge)**: 스킬 품질 검증 시 4가지 기준으로 점수화
  - 결과 정확성 (Accuracy)
  - 형식 준수 (Format compliance)
  - 효율성 (Minimum steps)
  - 올바른 도구 사용 (Correct tool selection)
- **원칙**: 스킬은 자산이지만 영구 자산이 아니다 — 모델 발전에 따라 주기적으로 필요성 재검토

---

## Context Management (컨텍스트 관리 — 3파일 시스템)

긴 세션이나 복잡한 작업 시, AI의 "금붕어 기억력"을 보완하기 위해 3개의 파일을 강제로 유지한다.

### 필수 3파일
| 파일 | 역할 |
|------|------|
| `PLAN.md` | 전체 아키텍처 및 설계도 |
| `CONTEXT.md` | 기술적 결정 이유 + 참고 자료 + API 스펙 |
| `TODO.md` | 현재 진행 상태 (`[ ]` 미완 / `[x]` 완료) |

### 사용 규칙
- **세션 시작 시**: 3파일 읽고 현재 상태 파악 후 작업 시작
- **세션 종료 시**: TODO.md 업데이트 + CONTEXT.md에 결정 사항 기록
- **컨텍스트 리셋 전**: 반드시 3파일 저장 후 `/clear`
- GSD `.planning/` 폴더를 쓸 때는 이 원칙이 자동 적용됨 (중복 생성 불필요)

---

## Ultrathink Methodology (울트라띵크 방법론)
For any non-trivial project, follow this 3-phase approach:

### Phase 1: Tech Spec (기술 사양서) — `/ultrathink-spec`
- Define Goals, Non-Goals, Requirements with acceptance criteria
- Use best available models to discuss and refine (multi-model council)
- Human coordinates between models until consensus
- Tech spec must be APPROVED before moving to planning

### Phase 2: Implementation Plan — `/ultrathink-plan`
- Break requirements into milestones (1 milestone = 1 session = 1 commit)
- Each milestone has subtasks with exact files, tests, criteria
- Keep changes under ~100 lines per milestone
- Plan reviewed by coding agent for feasibility

### Phase 3: Execute — `/ultrathink-go`
- Read techspec.md + PLAN.md before any work
- Write test FIRST (TDD), then minimum code to pass
- Run tests, iterate until pass
- Mark completed in PLAN.md, write handoff notes
- One commit per milestone/subtask

### Quick Reference
- `/ultrathink-init` — Initialize new project with Ultrathink structure
- `/ultrathink-spec` — Create tech spec with model council
- `/ultrathink-plan` — Create implementation plan from tech spec
- `/ultrathink-go` — Execute next uncompleted milestone
- Templates: `~/ai-workspace/ultrathink-templates/`

---

## 갓모드 리서치 시스템 (God Mode Research Pipeline)
> 핵심: 한 줄 프롬프트로 리서치→분석→시각화→저장까지 6분 완성

### 4기둥 아키텍처
| 기둥 | 역할 | 비유 |
|------|------|------|
| **Claude Code** | 전체 지휘 + 자연어 명령 해석 + 실행 방향 결정 | CEO / 중앙처리장치 |
| **Skill Creator** | 맞춤형 백엔드 스킬 즉석 자동 생성 (코딩 불필요) | 천재 개발자 |
| **NotebookLM** | 방대한 데이터 심층 분석 (구글 서버 → 토큰 비용 0) | 데이터 분석가 |
| **Obsidian** | 결과물+맥락을 마크다운으로 영구 저장 + 시각화 | 지식 금고 / 기록 담당 |

### 스킬 즉석 생성 3단계 (코딩 없음)
```
1. 터미널: plugin 명령어 1개로 skill-creator 설치
2. 자연어 지시: "YT-DLP로 유튜브 검색 후 결과 구조화해서 반환하는 스킬 만들어줘"
3. Claude Code가 백엔드 코드 자동 작성 → 즉시 실행 가능
```
→ AI가 자기 한계를 극복할 도구를 스스로 만들어낸다

### 슈퍼스킬 파이프라인 (1프롬프트 → 도미노 실행)
```
자연어 프롬프트 1개
    ↓
[수집] YT-DLP → 유튜브/웹 데이터 자동 수집
    ↓
[분석] NotebookLM → 구글 서버에서 심층 분석 (토큰 0원)
    ↓
[포맷] 인포그래픽 / PPT / 요약본 자동 생성
    ↓
[저장] Obsidian 마크다운으로 보관
```
실행 예: "탑5 MCP 서버 영상 찾고 조회수 요인 분석 후 인포그래픽 생성" → **6분 완성**

### 비용 최적화 — NotebookLM 무료 연산
- 문제: AI가 대량 텍스트 읽을 때 토큰 비용 폭탄 (서울→부산 택시 수준)
- 해결: NotebookLM 비공식 CLI 설치 → 무거운 연산을 구글 서버에 하청(오프로드)
- 효과: 수십 개 영상/논문 분석해도 토큰 비용 = 0

### Obsidian 이중 가독성 (인간 + AI 모두 읽음)
- **인간용**: 백링크 + 그래프 뷰 → 지식 간 연결 3D 시각화
- **AI용**: Claude Code가 볼트 내 모든 마크다운 직접 검색/읽기 → 과거 작업 맥락 즉시 파악
- **특장점**: 순수 마크다운 = 특정 앱 종속 없음 → 영구 호환성, 블랙박스 없음

### 레고 블록 유연성 — 입출력 교체만으로 무한 용도
```
소스 교체:  유튜브 → PDF 논문 → 웹 기사 → 사내 문서
출력 교체:  인포그래픽 → 임원 1페이지 요약 → PPT → 오디오 스크립트
파이프라인: 동일하게 유지
```
- 마케터: 경쟁사 분석 → 전략 슬라이드 6분
- 연구원: 논문 → 3D 마인드맵 6분
- 경영진: 사내 데이터 → 전략 보고서 6분

### 필요 도구
- **YT-DLP**: 유튜브 자막/정보 수집 (디지털 진공청소기)
- **NotebookLM CLI**: 구글 인증 후 무료 분석 엔진 사용
- **Obsidian**: 로컬 마크다운 지식 관리 앱 (무료)

---

## Lessons Learned (오답노트)
> AI 실수 발생 시 여기 기록 — 같은 실수 반복 방지
> 포맷: `### [날짜] 실수 제목` → 원인 → 다음엔 어떻게

<!-- 실수가 발생하면 아래에 추가 -->
