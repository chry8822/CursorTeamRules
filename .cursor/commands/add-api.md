# Add API

엔드포인트 정보를 받아 API 함수 / 타입 / Query 옵션을 생성한다.

## 호출 방식

**방식 1 — 정보 없이 호출**
`/add-api` 만 입력하면 아래 입력 안내 메시지를 출력하고 대기한다.

```
──────────────────────────────────
API 정보를 입력해 주세요.

형식 A (키 포함):
  메서드: GET
  엔드포인트: /carenation/community/...
  설명: 함수 설명 한 줄
  요청 파라미터: { 필드: 타입 }  ← 없으면 생략
  응답 타입: { 필드: 타입 }
  파일: src/api/...  ← 없으면 생략

형식 B (순서대로):
  GET
  /carenation/community/...
  함수 설명 한 줄
  { unread_count: number }
  src/api/main/main.ts
──────────────────────────────────
```

**방식 2 — 정보와 함께 호출**
`/add-api` 뒤에 바로 정보를 이어서 입력하면 안내 출력 없이 STEP 1부터 바로 진행한다.

```
/add-api
GET
/carenation/community/alram/unread-count
안읽은 알림 개수 조회
{ unread_count: number }
src/api/main/main.ts
```

## 입력 형식

아래 두 가지 형식 모두 허용한다. 파일 경로는 선택 사항이다.

### 형식 A — 키 포함 (명시적)

```
메서드: GET
엔드포인트: /carenation/community/alram/unread-count
설명: 안읽은 알림 개수 조회
요청 파라미터: { userType: 'protector' | 'caregiver' }
응답 타입: { unread_count: number }
파일: src/api/main/main.ts
```

### 형식 B — 키 없이 순서대로 (빠른 입력)

순서: `메서드 / 엔드포인트 / 설명 / 요청파라미터(없으면 생략) / 응답타입 / 파일(없으면 생략)`

```
GET
/carenation/community/alram/unread-count
안읽은 알림 개수 조회
{ userType: 'protector' | 'caregiver' }
{ unread_count: number }
src/api/main/main.ts
```

요청 파라미터가 없는 경우 해당 줄 생략:

```
GET
/carenation/community/alarm/unread-count
안읽은 알림 개수 조회
{ unread_count: number }
```

### 공통 규칙
- 응답 타입은 API 응답의 `data` 필드 내부 구조만 입력 (wrapper 제외)
- 예: 실제 응답이 `{ code, data: { unread_count }, message }` → `{ unread_count: number }` 만 입력
- 요청 파라미터가 없으면 `요청 파라미터:` 줄 또는 해당 줄 자체를 생략
- 파일 경로가 없으면 자동으로 위치 선택 안내 (STEP 2)

---

## STEP 1. 입력 분석

입력을 받으면 아래를 파악한다.

1. **함수명 도출**: 엔드포인트 + 메서드로 camelCase 함수명 제안
   - GET + `/alarm` → `getAlarmList`
   - POST + `/alarm/read` → `readAlarm`
   - DELETE + `/alarm/:id` → `deleteAlarm`

2. **타입명 도출**: 함수명 기반으로 PascalCase 타입명 제안
   - `getAlarmList` → `AlarmListRequest` / `AlarmListResponse`

3. **파일 경로 결정**:
   - 입력에 파일 경로가 명시된 경우 → 해당 파일에 추가
   - 명시되지 않은 경우 → STEP 2로

---

## STEP 2. 파일 위치 확인 (파일 경로 미입력 시만)

아래 형식으로 물어본다.

```
──────────────────────────────────
어디에 추가할까요?

  1. 기존 파일에 추가
     → 파일 경로를 입력하세요 (예: src/api/main/main.ts)
  2. 새 파일 생성
     → 서비스명을 입력하세요 (예: alarm)
        → src/api/alarm/AlarmService.ts 생성 예정

번호 또는 경로를 입력하세요:
──────────────────────────────────
```

- `1` 또는 경로 직접 입력 → 해당 파일에 추가
- `2` 또는 서비스명 입력 → 새 파일 생성

---

## STEP 3. 생성 코드 미리보기

실제 파일 수정 전에 생성될 코드를 먼저 출력한다.

```
──────────────────────────────────
생성할 코드 미리보기

📄 [대상 파일 경로]
```ts
// 추가될 API 함수
export const getAlarmList = (page: number, limit: number): Promise<AlarmListResponse> => {
  return API.withAuth.get<AlarmListResponse>(`/carenation/community/alarm?page=${page}&limit=${limit}`);
};
```

📄 src/api/main/response.ts (또는 해당 response 파일)
```ts
// 추가될 응답 타입
export interface AlarmListResponse {
  list: AlarmItem[];
  total: number;
  page: number;
  limit: number;
  total_pages: number;
}
```

📄 src/api/@query/main.ts (또는 해당 query 파일)
```ts
// 추가될 Query 옵션
alarmList: (page: number, limit: number) =>
  queryOptions({
    queryKey: [...mainQuery.all().queryKey, 'alarm-list', page, limit] as const,
    queryFn: () => getAlarmList(page, limit),
  }),
```

이 내용으로 파일에 추가할까요?
  y        → 추가 진행
  n        → 취소
  수정 내용 직접 입력 → 해당 부분 수정 후 진행
──────────────────────────────────
```

---

## STEP 4. 파일 수정 실행

확인 후 순서대로 파일을 수정한다.

### 수정 순서
1. **API 함수** → 지정된 서비스 파일 (main.ts 또는 새 파일)
2. **Request 타입** → 같은 폴더의 `request.ts` (요청 파라미터가 있는 경우만)
3. **Response 타입** → 같은 폴더의 `response.ts`
4. **Query 옵션** → `src/api/@query/[서비스명].ts`

### 기존 파일에 추가할 때 규칙
- 파일 맨 아래에 추가한다
- import가 필요하면 파일 상단 import 블록에 함께 추가한다
- 기존 코드는 절대 수정하지 않는다

### 새 파일 생성할 때 규칙
- `src/api/[서비스명]/[ServiceName]Service.ts` 생성
- `src/api/[서비스명]/request.ts` 생성 (요청 파라미터 있는 경우)
- `src/api/[서비스명]/response.ts` 생성
- `src/api/@query/[서비스명].ts` 생성 (없으면 새로 만들고, 있으면 추가)

---

## STEP 5. 완료 출력

```
✅ 추가 완료

수정된 파일:
- src/api/main/main.ts          (getAlarmList 함수 추가)
- src/api/main/response.ts      (AlarmListResponse 타입 추가)
- src/api/@query/main.ts        (alarmList queryOptions 추가)
```

---

## 규칙

- 미리보기 없이 파일을 수정하지 않는다
- 기존 파일에 추가할 때 기존 코드를 변경하지 않는다
- Query 파일이 없으면 새로 만들고, 있으면 기존 객체 안에 메서드를 추가한다
- 무한 스크롤이 필요한 경우 `queryOptions` 대신 `infiniteQueryOptions`를 사용한다
  - 이 경우 `initialPageParam`, `getNextPageParam`도 함께 작성한다
- 타입이 불명확한 필드는 `unknown` 대신 최대한 구체적으로 유추해서 작성한다

---

## 컨벤션 참고 규칙

파일을 작성하기 전에 반드시 **기존 파일을 먼저 읽어** 아래 항목을 확인하고 동일한 스타일로 작성한다.

### 확인할 항목

| 항목 | 확인 위치 | 예시 |
|------|-----------|------|
| import 경로 스타일 | 대상 파일 상단 | `@api/client`, `@api/main/response` |
| 기존 queryKey 구조 | `@query/[서비스명].ts` | `[...mainQuery.all().queryKey, 'alarm-list']` |
| 응답 타입 네이밍 | `response.ts` | `AlarmListResponse`, `AlarmItemResponse` |
| 요청 타입 네이밍 | `request.ts` | `VoteTodayTopicRequest` |
| 공통 타입 위치 | `src/types/[서비스]/` | `AlarmKeyword`, `UserType` → `src/types/alarm.ts` |
| API 함수 파라미터 방식 | 대상 서비스 파일 | 객체 wrapping 없이 직접 파라미터로 전달 |
| JSDoc 형식 | 대상 서비스 파일 | `@param`, `@returns` 필수 |

### 타입 위치 결정 기준

- **2페이지 이상 사용** 또는 **서비스 전반에서 쓰이는 타입** → `src/types/@shared/`
- **해당 서비스(alarm, feed 등)에서만 사용** → `src/types/[서비스명]/` (예: `src/types/alarm.ts`)
- **API 요청 파라미터 타입** → `src/api/[서비스명]/request.ts`
- **API 응답 타입** → `src/api/[서비스명]/response.ts`

### 자주 쓰이는 기존 타입 (main 서비스 기준)
- `UserType` — `src/types/alarm.ts` (`'protector' | 'caregiver'`)
- `AlarmKeyword` — `src/types/alarm.ts`
- `AlarmTab` — `src/types/alarm.ts`

---

## 사용 예시

### 예시 1 — 기존 파일에 추가 (파일 경로 명시)

```
메서드: GET
엔드포인트: /carenation/community/alarm
설명: 알림 목록 조회 (페이지네이션)
요청 파라미터: { page: number, limit: number, keyword?: string }
응답 타입: { list: AlarmItemResponse[], total: number, totalPages: number }
파일: src/api/main/main.ts
```

→ 결과: `main.ts`에 `getAlarmList` 함수 추가, `response.ts`에 타입 추가, `@query/main.ts`에 `queryOptions` 추가

---

### 예시 2 — 파일 경로 생략 (위치 선택 안내)

```
메서드: POST
엔드포인트: /carenation/community/alarm/read
설명: 알림 읽음 처리
요청 파라미터: { alarmId: number }
응답 타입: { success: boolean }
```

→ 파일 경로 미입력 → "기존 파일에 추가 / 새 파일 생성" 중 선택 안내 출력

---

### 예시 3 — 새 서비스 파일 생성

```
메서드: GET
엔드포인트: /carenation/community/feed
설명: 피드 목록 조회
응답 타입: { list: FeedItem[], total: number }
```

→ `2` 또는 서비스명 `feed` 입력 시
→ `src/api/feed/FeedService.ts`, `src/api/feed/response.ts`, `src/api/@query/feed.ts` 생성

---

### 예시 4 — 무한 스크롤 (infiniteQueryOptions)

```
메서드: GET
엔드포인트: /carenation/community/group/list
설명: 그룹 목록 무한 스크롤 조회
요청 파라미터: { page: number, limit: number }
응답 타입: { list: GroupItem[], totalPages: number }
파일: src/api/main/main.ts
infinite: true
```

→ `queryOptions` 대신 `infiniteQueryOptions` 생성, `initialPageParam: 1`, `getNextPageParam` 자동 포함

---

// 호출 방법:
// /add-api
