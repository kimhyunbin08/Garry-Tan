여기서는 파일을 직접 만들 수 없어서, 바로 쓸 수 있는 가이드 내용을 아래에 드릴게요. 파일로 저장하거나 영상 요약 페이지 2개와 함께 묶는 건 메인 대화에서 요청해 주세요.

```markdown
# gstack + gbrain 시작 가이드 (새 Mac 기준)

Garry Tan(YC 대표)의 방식대로, Claude Code를 "내 맥락을 아는 개인 에이전트 + AI 개발팀"으로 만드는 가이드예요.
함께 주는 영상 캡처 폴더 2개("Own Your Intelligence", "How to Make Claude Code Your AI Engineering Team")를 먼저 보세요.

## 핵심 개념 (5줄 요약)
- **라이브러리(brain):** 내 사람·프로젝트·결정을 담은 md 폴더 + 검색 DB (gbrain)
- **스킬 파일 = 직원 한 명:** 똑똑한 인턴이 따라 할 수 있게 쓴 한 페이지짜리 업무 설명서
- **RESOLVER.md = 조직도:** 일이 들어오면 어느 폴더·스킬이 맡을지 정함
- **gstack:** 기획→리뷰→개발→QA→배포를 스킬로 만든 개발팀 (`/office-hours`, `/autoplan`, `/review`, `/qa`, `/ship` 등)
- **원칙:** 같은 걸 두 번 시키면 실패다. 작업이 끝날 때마다 스킬로 만든다(`/skillify`)

## 0. 준비물
- Claude Code (Pro 이상 구독)
- Homebrew
- GitHub 계정

## 1. 기본 도구 설치 (터미널, 한 줄씩)
    brew install git gh oven-sh/bun/bun
    bun --version          # 1.4.0 이상이어야 함. 낮으면: bun upgrade
    gh auth login -h github.com -p https -w   # 브라우저에서 Authorize 클릭

## 2. gbrain 설치 (터미널)
    bun install -g github:garrytan/gbrain#latest-stable
    gbrain --version
⚠️ `npm install gbrain`은 전혀 다른 패키지예요. 반드시 위 명령으로 설치하세요.

## 3. gstack 설치 (터미널, 한 줄씩)
    git clone https://github.com/garrytan/gstack ~/.claude/skills/gstack
    cd ~/.claude/skills/gstack && ./setup
이미 있으면 업데이트:
    cd ~/.claude/skills/gstack && git pull && ./setup

## 4. 에이전트 전용 폴더 만들기
홈 폴더에 gbrain 전용 폴더를 하나 만들고, **에이전트 작업은 항상 이 폴더에서 Claude를 여세요.**
    mkdir ~/agent && cd ~/agent && claude
- 이 폴더 = 에이전트의 몸(정체성·기억·스킬·brain)
- 다른 프로젝트(예: Meditor 코드)는 원래 위치 그대로 두면 됨

## 5. 부트스트래핑 (Claude 안에서 진행)
`~/agent`에서 연 Claude에 이렇게 요청하세요:
> "~/node_modules/gbrain/BOOTSTRAP_FOR_AGENTS.md (또는 gbrain repo의 BOOTSTRAP_FOR_AGENTS.md) 를 읽고 그대로 부트스트랩 해줘"

진행 단계 (`gbrain bootstrap status`로 언제든 확인 가능):
1. preflight: git, bun, gh 확인
2. engine: DB 생성. **처음엔 기본값(PGLite)으로 충분**
3. interview: 질문 12개에 직접 답함 (에이전트 이름, 내 소개, 목적, 반복 업무, 말투, 원칙 등). **에이전트가 대신 지어내지 못하게 되어 있음.** 솔직하고 구체적으로 답할수록 좋음
4. render: SOUL.md / USER.md / AGENTS.md / MEMORY.md 등 정체성 파일 생성
5. skills: gbrain 스킬 세트 생성
6. wire: MCP + hook 연결
   - **MCP 범위는 project 추천:** 이 폴더에서 연 세션만 brain에 접근 → 다른 repo의 악성 지시문이 내 정보를 못 읽음
7. repo: **비공개(private)** GitHub repo 생성 후 push
8. verify: 전체 점검. PASS가 나와야 완료

## 6. brain 폴더 구조 (Garry 추천)
`~/agent/brain/` 안에:
- `RESOLVER.md` (어디에 저장할지 결정 트리), `schema.md`, `index.md`, `log.md`
- `people/` `companies/` `projects/` `meetings/` `deals/` `ideas/` `concepts/` `writing/` `programs/` `org/` `personal/` `inbox/` `archive/` 등
- 모든 폴더에 `README.md` (여기에 뭘 넣고 뭘 안 넣는지)
- 페이지 형식: 위쪽 = 현재 요약(계속 갱신), `---` 아래 = 타임라인(날짜·출처·사건, 추가만)
- 첫 주 목표: 진행 중인 프로젝트마다 1페이지, 같이 일하는 사람마다 1페이지

## 7. 그다음 (Garry의 5단계 중 3~5)
3. 매주 제일 싫은 반복 업무 하나를 스킬로 만들기
4. 그 스킬을 정기 작업으로 걸기 (예: 매일 아침 7시 브리핑)
5. 작업 끝날 때마다 `/skillify`
→ 1주차는 장난감, 4주차부터 체감, 12주차엔 "치트키" 느낌. 대부분 2주차에 포기함

## 주의사항 (직접 겪은 것들)
- **Claude 창을 여러 개 동시에 열지 말 것:** brain DB는 쓰는 주체가 하나여야 함. 오래 열어둔 다른 창이 brain을 붙잡으면 설정 단계가 막힘
- **PGLite는 MCP가 켜져 있으면 터미널 gbrain 명령이 잠김(`pglite_busy`)** → 불편해지면 로컬 Postgres로 이전 가능 (`gbrain migrate --to postgres`)
- **Gmail 연결 시:** Google Cloud에서 OAuth 동의 화면 "외부(External)" + **반드시 '앱 게시'** (안 하면 7일마다 연결 끊김). 클라이언트 유형은 "데스크톱 앱"
- **API 키는 채팅에 붙여넣지 말 것:** 별도 터미널에서 입력
- 에이전트가 읽은 내용은 Anthropic 서버로 감 → 민감한 데이터는 로컬 모델 처리 고려
- 웹 브라우징은 gstack의 `/browse` 사용

## 자주 쓰는 명령
    gbrain doctor               # 상태 점검
    gbrain bootstrap status     # 부트스트랩 진행 상황
    gbrain bootstrap verify     # 전체 검증
    gbrain sources push         # 비공개 repo로 백업
```

**참고**
- 사용자님 환경에서 확인된 사실 위주로 썼어요. 다만 이건주님 맥에서 기본값인 PGLite로 부트스트랩하는 경로는 우리가 직접 돌려 보진 않았어요. 문서상 기본 경로예요.
- 전역 규칙상 문서는 영어가 원칙이에요. 이건주님이 읽기 편하도록 한국어로 썼으니, 영어본이 필요하면 메인 대화에서 요청해 주세요.
- 영상 캡처 폴더를 넘길 때 함께 주면 좋은 것이 있어요. 지금 `~/agent/brain/concepts/`에 있는 요약 페이지 2개(`own-your-intelligence.md`, `gstack-ai-engineering-team.md`)예요. 개인정보가 없어서 그대로 공유해도 돼요.