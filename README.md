# Cursor AI Team Workflow

> React / TypeScript 프로젝트를 위한 Cursor AI 팀 자동화 워크플로우

팀 컨벤션을 AI가 이해할 수 있는 Rules로 문서화하고,
커스텀 Slash Commands로 반복 작업을 자동화했습니다.
`.cursor/` 폴더를 Git으로 공유해 팀 전체가 동일한 AI 환경에서 작업합니다.

---

## 구조

```
.cursor/
├── rules/
│   ├── index.mdc          # 프로젝트 기본 컨벤션 (항상 적용)
│   ├── typescript.mdc     # TypeScript 규칙 (ts/tsx 파일 자동 적용)
│   └── ai-behavior.mdc    # AI 행동 규칙 (항상 적용)
└── commands/
    ├── check.md           # 컨벤션 + 타입 점검
    ├── create-api.md      # API 서비스 전체 구조 생성
    ├── add-api.md         # 단일 API 엔드포인트 추가
    └── commit.md          # Git 커밋 자동화
```

---

## Rules

### index.mdc — 프로젝트 기본 컨벤션

모든 대화에 항상 적용되는 프로젝트 기본 규칙입니다.

- 디렉토리 구조 규칙 (API, 쿼리키, 컴포넌트, 타입, 페이지, 훅, 슬라이스, 상수)
- 네이밍 규칙 (PascalCase, camelCase, UPPER_SNAKE_CASE, @ 접두사)
- 타입 규칙 (`any` 절대 금지, 타입 위치 기준)
- 코드 규칙 (`const` 강제, `===` 강제, `console.log` 커밋 금지)
- JSDoc 필수 대상 (페이지 컴포넌트, 비즈니스 로직 함수)

### typescript.mdc — TypeScript 규칙

`src/**/*.ts`, `src/**/*.tsx` 파일 작업 시 자동 적용됩니다.

- TanStack Query `useQuery`, `useMutation` 제네릭 타입 필수 명시
- Redux `useSelector`에 `RootState`, `useDispatch`에 `AppDispatch` 타입 필수
- `as` 타입 단언 최소화 → 타입 가드 함수로 대체
- 불가피한 `as` 사용 시 반드시 이유 주석 명시

### ai-behavior.mdc — AI 행동 규칙

모든 대화에 항상 적용되는 AI 응답 방식 규칙입니다.

| Rule | 내용 |
|------|------|
| RULE 1 | 답변 전 반드시 파일을 직접 읽고 근거 코드 라인 기반으로만 답변 |
| RULE 2 | 문제 파일 → 의존 파일 순서로 전체 흐름 분석 |
| RULE 3 | 실행 흐름을 코드 시뮬레이션으로 추적 후 원인 도출 |
| RULE 4 | 분석 결과를 `[분석 대상] [흐름] [시뮬레이션] [원인] [해결] [검증]` 형식으로 보고 |
| RULE 5 | 순서 있는 작업은 단계별 진행, 사용자 확인 후 다음 단계 진행 |
| RULE 6 | 파일 수정 전 `[변경 대상] [이유] [순서] [영향]` 사전 고지 |
| RULE 6+ | 파일명/import 경로 변경 시 전체 참조 파일 검색 후 동시 수정, `npx tsc --noEmit` 검증 필수 |
| RULE 7 | 한 번 분석한 내용 근거 없이 번복 금지, 재분석 시 근거 함께 제시 |
| RULE 8 | 수정 전 "왜 이렇게 작성됐는가" 먼저 파악 (lint 우회, 빌드 설정 등) |
| RULE 9 | 새 파일/함수 생성 전 기존 코드베이스 중복 여부 검색 필수 |
| RULE 10 | 의견을 물으면 의견만 답하고, 명시적 컨펌 후에만 코드 작업 진행 |

---

## Commands

### /check — PR 전 컨벤션 & 타입 점검

`/check @파일명.tsx`

컨벤션, 네이밍, JSDoc, 타입 오류를 순서대로 점검합니다.
모든 문제를 번호 목록으로 먼저 출력하고, 수정 여부는 개발자가 직접 선택합니다.

![/check 커맨드 설명](assets/check-command-tooltip.png)

분석 완료 후 Critical / Warning 으로 분류해 번호 목록을 출력합니다.

![/check 결과 — 문제 목록 출력](assets/check-result-list.png)

선택한 항목에 대해 수정 내용을 상세히 보여줍니다.

![/check 결과 — 상세 수정 내용](assets/check-result-detail.png)

수정 완료 후 최종 요약을 출력합니다.

![/check 결과 — 수정 완료](assets/check-result-done.png)

**입력 형식**

| 입력 | 동작 |
|------|------|
| `1 3 5` 또는 `1,3,5` | 해당 번호만 수정 |
| `1-3` | 범위 수정 |
| `critical` | 🔴 Critical 전체 수정 |
| `warning` | 🟡 Warning 전체 수정 |
| `all` | 전체 수정 |
| `done` | 수정 없이 종료 |

---

### /create-api — API 서비스 전체 구조 생성

`/create-api [서비스명]` 또는 `/create-api dailytalk user group` (복수 동시 생성)

새 서비스의 API 파일 구조 전체를 한 번에 생성합니다.
생성 전 파일 목록을 먼저 출력하고, 생성 여부는 개발자가 결정합니다.

![/create-api 커맨드 설명](assets/create-api-tooltip.png)

컨벤션에 맞게 필요한 파일 목록을 출력합니다.

![/create-api 결과 — 생성된 파일 목록](assets/create-api-result-files.png)

생성된 파일에는 TanStack Query `queryOptions` 패턴이 적용됩니다.

![/create-api 결과 — 생성된 코드 예시](assets/create-api-result-code.png)

**생성 파일 구조**

```
src/api/[서비스명]/
├── [ServiceName]Service.ts   # 메인 서비스 함수
├── request.ts                # 요청 타입
└── response.ts               # 응답 타입

src/api/@query/
└── [서비스명].ts             # TanStack Query queryOptions
```

---

### /add-api — 단일 API 엔드포인트 추가

`/add-api` 또는 `/add-api [엔드포인트 정보]`

엔드포인트 정보를 입력하면 API 함수 / 타입 / Query 옵션을 기존 파일에 추가하거나 새 파일을 생성합니다.
수정 전 생성될 코드를 미리보기로 출력하고, 확인 후에만 파일을 수정합니다.

> `/create-api` 가 서비스 폴더 전체를 새로 만드는 커맨드라면,
> `/add-api` 는 기존 또는 새 파일에 단일 엔드포인트를 추가하는 커맨드입니다.

**호출 방식**

```
# 방식 1 — 정보 없이 호출 → 입력 안내 출력 후 대기
/add-api

# 방식 2 — 정보와 함께 호출 → 바로 진행
/add-api
GET
/carenation/community/alarm/unread-count
안읽은 알림 개수 조회
{ unread_count: number }
src/api/main/main.ts
```

**입력 형식**

| 항목 | 설명 |
|------|------|
| 메서드 | `GET` / `POST` / `PUT` / `DELETE` |
| 엔드포인트 | API 경로 |
| 설명 | 함수 설명 한 줄 |
| 요청 파라미터 | `{ 필드: 타입 }` — 없으면 생략 |
| 응답 타입 | `{ 필드: 타입 }` — `data` 내부 구조만 입력 |
| 파일 | 추가할 파일 경로 — 없으면 생략 (위치 선택 안내) |

**진행 순서**

1. 입력 분석 → 함수명 / 타입명 자동 도출
2. 파일 경로 미입력 시 → 기존 파일 추가 또는 새 파일 생성 선택
3. 생성될 코드 미리보기 출력 → 확인 후 파일 수정
4. API 함수 → Request 타입 → Response 타입 → Query 옵션 순서로 파일 수정
5. 수정 완료 파일 목록 출력

**무한 스크롤이 필요한 경우** `infinite: true` 를 추가하면 `infiniteQueryOptions` 로 생성됩니다.

---

### /commit — Git 커밋 자동화

**기본 사용 (타입 선택 → 메시지 자동 생성)**

```
/commit
```

**제목 직접 입력 (즉시 커밋)**

```
/commit fix: 로그인 토큰 갱신 오류 수정
/commit [ABEH-2966] - 동행 공고 프로필 이미지 미노출 수정
```

제목을 직접 입력하면 타입 선택·메시지 생성 단계를 건너뛰고 바로 커밋을 실행합니다.

**진행 순서 (기본 사용)**

1. `git status` + `git diff HEAD` 로 변경 사항 파악
2. 커밋 타입 선택 안내 출력 (`feat` / `fix` / `refactor` / `style` / `chore` / `docs` / `test`)
3. diff 기반으로 커밋 메시지 초안 생성 → 사용자 확인
4. 확인 시 커밋 실행 (PowerShell 환경 대응)
5. 커밋 완료 후 push 여부 별도 확인

> 응답 속도를 높이려면 빠른 모델로 전환 후 사용하세요. (claude-3.5-haiku, gpt-4o-mini 등)

---

## 설치

### 자동 설치 (권장)

```bash
npx cursor-setup
```

대화형 메뉴에서 적용할 파일을 선택하면 `.cursor/` 폴더에 자동으로 설치됩니다.

```
──────────────────────────────
🎯  Cursor 설정 셋업
──────────────────────────────
── Rules ──────────────────
❯ ◯ index.mdc            — 프로젝트 기본 컨벤션
  ◯ typescript.mdc       — TypeScript 규칙
  ◯ ai-behavior.mdc      — AI 행동 규칙
── Commands ────────────────
  ◯ check.md             — 컨벤션 + 타입 점검
  ◯ commit.md            — Git 커밋 자동화
  ◯ create-api.md        — API 서비스 전체 구조 생성
  ◯ add-api.md           — 단일 API 엔드포인트 추가
```

업데이트된 파일을 다시 받으려면 동일하게 `npx cursor-setup` 을 재실행하면 됩니다.

---

### 수동 설치

이 레포를 clone해서 `.cursor/` 폴더를 프로젝트에 직접 복사합니다.

```bash
git clone https://github.com/chry8822/CursorTeamRules.git
cp -r CursorTeamRules/.cursor [내 프로젝트 경로]/
```

원하는 파일만 선택해서 복사하거나, 내용을 직접 수정해서 사용할 수 있습니다.

---

### Rules vs Commands 동작 차이

| | Rules (`.mdc`) | Commands (`.md`) |
|--|--|--|
| 위치 | `.cursor/rules/` | `.cursor/commands/` |
| 적용 방식 | 매 대화에 **자동 주입** | `/커맨드명` 으로 **직접 호출** |
| AI 준수율 | 배경 컨텍스트로 작동 (드리프트 가능) | 파일을 직접 읽고 절차대로 실행 (강제성 높음) |
| 사용 목적 | 코딩 컨벤션, 네이밍, 타입 규칙 등 | 반복 작업 자동화 (점검, 생성, 커밋 등) |

> 💡 **팁** — AI가 규칙을 자주 무시한다면 커맨드로 만드세요.
> 커맨드는 AI가 파일을 직접 읽고 절차대로 실행하기 때문에 Rules보다 훨씬 강하게 동작합니다.

---

## 기술 스택

| 분류 | 기술 |
|------|------|
| 빌드 | Vite 4 |
| UI | React 18 / TypeScript 5 |
| 전역 상태 | Redux Toolkit 2 |
| 서버 상태 | TanStack Query v5 |
| 폼 | React Hook Form 7 |
| 유틸 | date-fns / lodash-es |
