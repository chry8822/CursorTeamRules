# Convention & Type Check

현재 작업한 파일들을 아래 순서대로 점검해줘.

## 중요 규칙
- 모든 문제를 먼저 번호 목록으로 출력
- 목록 출력 후 반드시 입력 안내 메시지 출력
- 내 입력을 받을 때까지 절대 수정하지 말 것
- 수정 완료 후 최종 요약 출력

## 입력 형식
- `1 3 5` 또는 `1,3,5` → 해당 번호만 수정
- `1-3` → 1번부터 3번까지 범위 수정
- `1-3 5` 또는 `1-3,5` → 범위 + 개별 혼합
- `all` → 전체 수정
- `critical` → 🔴 Critical 항목만 전부 수정
- `warning` → 🟡 Warning 항목만 전부 수정
- `done` 또는 `skip` → 수정 없이 종료

---

## STEP 1. 컨벤션 점검

### 1-1. 파일/폴더 위치
- 컴포넌트 파일이 src/components/ 아래 올바른 위치에 있는가?
- 2페이지 이상 사용되는 공통 컴포넌트가 @shared/가 아닌 곳에 있는가?
- 훅 파일이 src/hooks/ 아래 있는가?
- Redux 슬라이스가 src/store/action/[서비스]/[서비스]Slice.ts 구조인가?
- 상수 파일이 src/constants/ 아래 있고 파일명이 대문자인가? (MESSAGE.ts, CONFIG.ts)
- API 서비스 파일이 src/api/[서비스]/ 아래 올바른 구조인가?
  (DailyFeedService.ts, request.ts, response.ts, const.ts)

### 1-2. 네이밍
- 컴포넌트/페이지 파일명이 PascalCase인가?
- 훅 파일명이 use[연결된컴포넌트명].ts 형식인가?
- 폴더명이 소문자인가?
- 공통 폴더에 @ 접두사가 붙어 있는가? (@shared/, @query/)
- 변수명이 camelCase인가?
- 상수가 UPPER_SNAKE_CASE인가?
- 타입/인터페이스명이 PascalCase인가?

### 1-2-1. 파일명 이상 문자 점검 (⚠️ 파일 rename 연쇄 수정 필요)
- 파일명에 공백, 특수문자, trailing space가 포함되어 있는가?
- 해당 파일을 import하는 모든 파일에서 경로 끝에 의도치 않은 공백이나 특수문자가 포함되어 있는가?

> ⚠️ **파일명 수정 시 반드시 아래 절차를 따를 것**
>
> 1. 해당 파일을 import하는 모든 파일을 먼저 검색 (프로젝트 전체 대상)
> 2. 검색된 참조 파일 목록을 출력하고 수정 여부를 확인
> 3. 확인 후 파일명 rename + 모든 참조 import 경로를 동시에 수정
> 4. `npx tsc --noEmit`으로 잔여 에러 없음을 반드시 검증
>
> 파일명만 수정하고 import를 누락하면 프로젝트 전체에서 빌드 에러가 발생할 수 있음

### 1-3. JSDoc 주석
- 페이지 컴포넌트(Main.tsx)에 경로, 권한 주석이 있는가?

```ts
// ✅ 필수 형식
/**
 * 페이지 이름
 * - 경로: /community/feed
 * - 권한: 커뮤니티 프로필 필수
 */
export default function Main() {}
```

- 비즈니스 로직 함수에 @param, @returns 주석이 있는가?

```ts
// ✅ 필수 형식
/**
 * 피드 좋아요 토글
 * @param feedId - 피드 ID
 * @param isLiked - 현재 좋아요 상태
 * @returns Promise<void>
 */
async function toggleLike(feedId: string, isLiked: boolean): Promise<void> {}
```

### 1-4. 코드 스타일
- == 대신 === 를 사용했는가?
- if문에 중괄호가 있는가?
- console.log가 남아 있는가?
- let이 const로 바꿀 수 있는 경우가 있는가?
- 화살표 콜백 함수를 사용했는가?

---

## STEP 2. 타입 점검

터미널에서 `npx tsc --noEmit` 실행 후 에러 목록 확인.

### 2-1. any 사용 여부
- any 타입이 사용된 곳을 모두 찾아 리포트
- 적절한 타입 또는 unknown으로 교체 제안

### 2-2. React 컴포넌트 타입
- React.FC 사용 여부 확인 (사용 시 리포트, Props 인터페이스 직접 명시로 교체 제안)
- 이벤트 핸들러에 React 이벤트 타입이 명시되어 있는가?

### 2-3. 함수 반환 타입
- async 함수와 비즈니스 로직 함수에 반환 타입이 명시되어 있는가?

### 2-4. 타입 위치
- 2페이지 이상 사용되는 타입이 src/types/@shared/가 아닌 곳에 있는가?
- API 요청/응답 타입이 request.ts / response.ts에 있는가?

### 2-5. TanStack Query / Redux 타입
- useQuery, useMutation에 제네릭 타입이 명시되어 있는가?
- useSelector에 RootState 타입이 명시되어 있는가?
- useDispatch가 AppDispatch 타입으로 사용되고 있는가?

### 2-6. 타입 단언 (as)
- as 사용 여부 확인
- 타입 가드로 대체 가능한 경우 제안
- as 사용 시 이유 주석이 있는가?

---

## 목록 출력 형식

점검 완료 후 아래 형식으로 출력.
각 항목은 반드시 아래 구조를 따를 것:
- 번호는 마크다운 리스트(1. 2. 3.) 를 사용하지 않고 **굵은 번호** 형태로 출력
- 번호 바로 다음 줄에 코드 레퍼런스 블록 출력 (코드 블록이 리스트 안에 중첩되면 링크가 깨짐)
- 코드 레퍼런스 블록은 Cursor에서 클릭 시 해당 라인으로 바로 이동 가능

출력 예시:

---

**1.** 🔴 [Critical] `파일명:라인` — 문제 한 줄 요약

```startLine:endLine:src/상대경로/파일명.tsx
// 문제가 되는 코드 라인
```

**2.** 🟡 [Warning] `파일명:라인` — 문제 한 줄 요약

```startLine:endLine:src/상대경로/파일명.tsx
// 문제가 되는 코드 라인
```

---

총 🔴 1건 / 🟡 3건 / 🟢 Pass X개

───────────────────────────────
수정할 항목을 입력하세요:

  1 3 5       → 번호 (띄어쓰기)
  1,3,5       → 번호 (쉼표)
  1-3         → 범위 (1번~3번)
  1-3,5       → 범위 + 개별 혼합
  critical    → 🔴 전체 수정
  warning     → 🟡 전체 수정
  all         → 전체 수정
  done        → 수정 없이 종료
───────────────────────────────

## 수정 완료 후 최종 요약 형식

```
✅ 수정 완료

수정됨 : 1, 3번 (2건)
스킵   : 2, 4번 (2건)

🔴 Critical 0건 잔여
🟡 Warning  2건 잔여
```
