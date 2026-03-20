---
name: cursor-check
description: PR 전 코드 컨벤션과 타입 점검. 파일명·네이밍·JSDoc·코드 스타일·TypeScript 타입 오류를 분석해 번호 목록으로 출력하고, 사용자가 선택한 항목만 수정한다. 사용자가 컨벤션 점검, 타입 점검, PR 전 점검, /check 를 요청할 때 사용한다.
---

# Convention & Type Check

프로젝트에 `.cursor/commands/check.md` 가 있으면 반드시 그 파일을 읽고 절차대로 실행한다.

없을 경우 아래 절차를 따른다.

## 핵심 규칙

- 모든 문제를 먼저 번호 목록으로 출력한다
- 목록 출력 후 반드시 수정할 항목 입력을 대기한다
- 입력을 받기 전까지 절대 수정하지 않는다
- 수정 완료 후 최종 요약을 출력한다

## 점검 순서

### STEP 1. 컨벤션 점검
- 파일/폴더 위치 및 네이밍 (PascalCase, camelCase, UPPER_SNAKE_CASE)
- JSDoc 주석 (페이지 컴포넌트: 경로·권한, 비즈니스 함수: @param·@returns)
- 코드 스타일 (`===`, `const`, 중괄호, `console.log` 잔존 여부)

### STEP 2. 타입 점검
- `npx tsc --noEmit` 실행 후 에러 확인
- `any` 사용, React.FC, 제네릭 타입 누락, `as` 단언 남용 점검
- TanStack Query: `useQuery`·`useMutation` 제네릭 필수
- Redux: `useSelector`에 `RootState`, `useDispatch`에 `AppDispatch` 필수

## 출력 형식

```
**1.** 🔴 [Critical] `파일명:라인` — 문제 요약

**2.** 🟡 [Warning] `파일명:라인` — 문제 요약

총 🔴 N건 / 🟡 N건

──────────────────────────────────
수정할 항목을 입력하세요:
  1 3 5 / 1,3,5 / 1-3 / critical / warning / all / done
──────────────────────────────────
```

## 수정 완료 형식

```
✅ 수정 완료
수정됨 : N건 / 스킵 : N건
🔴 Critical N건 잔여 / 🟡 Warning N건 잔여
```
