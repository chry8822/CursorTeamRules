# 개발 노트

> 이 프로젝트의 의사결정 과정, 현재 상태, 진행 방향을 기록합니다.

---

## 현재 구성 (완료)

### Cursor 설정 파일

| 종류 | 파일 | 설명 |
|------|------|------|
| Rules | `index.mdc` | 프로젝트 기본 컨벤션 (항상 적용) |
| Rules | `typescript.mdc` | TypeScript 규칙 (ts/tsx 자동 적용) |
| Rules | `ai-behavior.mdc` | AI 행동 규칙 13개 (항상 적용) |
| Commands | `check.md` | 컨벤션 + 타입 점검 |
| Commands | `commit.md` | Git 커밋 자동화 |
| Commands | `create-api.md` | API 서비스 전체 구조 생성 |
| Commands | `add-api.md` | 단일 API 엔드포인트 추가 |
| Skills | `ai-analysis-behavior` | 코드 분석 · 디버깅 시 AI 행동 규칙 |
| Skills | `ai-modification-behavior` | 파일 수정 · 구현 시 AI 행동 규칙 |
| Skills | `ai-general-behavior` | 모든 대화에 적용되는 기본 AI 규칙 |
| Skills | `ai-accuracy-behavior` | UI/경로 추측 단정 방지 규칙 |

### cursor-setup CLI (`cursor-setup@1.1.0`)

```bash
npx cursor-setup          # Rules / Commands / Skills 파일 설치
npx cursor-setup readme   # README.md 생성
```

---

## Rules vs Commands vs Skills 차이

| | Rules | Commands | Skills |
|--|-------|----------|--------|
| 적용 방식 | 매 대화 자동 주입 | `/커맨드명` 직접 호출 | AI가 관련성 판단 후 로드 |
| AI 준수율 | 낮음 (드리프트 발생) | 높음 (파일 직접 읽음) | 중간 (description 트리거) |
| 용도 | 코딩 컨벤션, 네이밍 | 반복 작업 자동화 | AI 행동 패턴 강제 |

### Skills 트리거 원리

- Skills의 `description`을 AI가 항상 읽음
- 현재 대화와 관련 있다고 판단하면 전체 SKILL.md 로드
- **description에 실제 사용자 말투 문구를 3개 이상 넣으면 트리거 확률 증가**

---

## Cursor vs Claude Code 비교

| | Cursor | Claude Code |
|--|--------|------------|
| 실행 환경 | IDE (GUI 채팅) | 터미널 |
| 설정 파일 | `.cursor/` 폴더 | `CLAUDE.md` |
| Skills | SKILL.md | CLAUDE.md에 직접 작성 |
| Hooks | ❌ 미지원 | ✅ 네이티브 지원 |
| Sub-agents | 순차 실행만 가능 | 진짜 병렬 실행 |
| MCP | ✅ 지원 | ✅ 지원 |
| 로컬 스크립트 실행 | CLI 배포 필요 | 파일 생성 후 즉시 실행 |

### Claude Code 방식의 팀 공유

Claude Code로 로컬 CLI 스크립트를 만들면 npm 배포 없이 팀과 공유 가능:

```
방법 1: 같은 프로젝트 레포에 scripts/ 폴더로 추가 → Git으로 자동 공유
방법 2: 별도 스크립트 레포 → git pull로 업데이트
방법 3: npm 패키지 (현재 방식) → npx로 어느 프로젝트에서나 사용
```

**팀 전체 범용 도구라면 npm 방식이 적합. 프로젝트 전용이라면 로컬 스크립트가 유리.**

---

## README 생성 CLI (cursor-setup readme)

### 현재 구현

- 기본 구성 (프로젝트 타입별 프리셋 4종) / 커스텀 (블록 직접 선택) 두 가지 모드
- 블록 12종: 개요, 스크린샷, 기능, 기술스택, 시작하기, 환경변수, 구조, API문서, 개발가이드, 배포, 기여, 라이선스
- `package.json` 자동 읽어 프로젝트명 · 설명 · 기술스택 감지
- 기존 README.md 있으면 덮어쓰기 여부 확인

### 진행 방향

**① `npx cursor-setup` 단일 진입점으로 통합**

현재는 `readme` 서브커맨드를 별도로 입력해야 하는데,
`npx cursor-setup` 실행 시 먼저 메뉴로 선택하게 변경 예정:

```
? 무엇을 하시겠습니까?
❯ 파일 설치   — Rules / Commands / Skills 설치
  README 생성 — 프로젝트 README.md 생성
```

---

## 다음 검토 항목

### MCP 설정 (체감 효과 큰 것부터)

| MCP | 효과 |
|-----|------|
| `context7` | 라이브러리 최신 공식 문서 실시간 제공 → AI 환각 감소 |
| `github` | GitHub 이슈/PR을 Cursor 안에서 조회·생성 |
| `playwright` | E2E 테스트 직접 실행 |

### Git Hooks (Claude Code Hooks 대안)

- `pre-commit`: 커밋 전 타입 체크 + lint 자동 실행
- `commit-msg`: 커밋 메시지 형식 검증
- 스크립트를 `.cursor/hooks/` 에 넣고 cursor-setup으로 배포 가능

### 개발 편의 스킬 후보

- `code-review-checklist`: PR 리뷰 시 중복/에러처리/타입 항목 체크
- `refactor-safety`: 리팩토링 전 동작 유지 검증 강제
- `task-decomposer`: 복잡한 작업을 서브태스크로 분해 후 순차 실행
