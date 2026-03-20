---
name: cursor-add-api
description: 단일 API 엔드포인트 추가. 메서드·엔드포인트·응답타입을 입력받아 API 함수, 타입, Query 옵션을 기존 또는 새 파일에 추가한다. 코드 미리보기 후 확인 시에만 파일을 수정한다. 사용자가 API 추가, /add-api, 엔드포인트 추가를 요청할 때 사용한다.
---

# Add API

프로젝트에 `.cursor/commands/add-api.md` 가 있으면 반드시 그 파일을 읽고 절차대로 실행한다.

없을 경우 아래 절차를 따른다.

## 입력 형식

```
메서드: GET
엔드포인트: /carenation/community/alarm/unread-count
설명: 안읽은 알림 개수 조회
요청 파라미터: { userType: string }   ← 없으면 생략
응답 타입: { unread_count: number }
파일: src/api/main/main.ts            ← 없으면 생략
```

정보 없이 `/add-api` 만 호출되면 위 형식으로 입력 안내를 먼저 출력하고 대기한다.

## STEP 1. 입력 분석

- 함수명: 메서드+엔드포인트 기반 camelCase 도출 (예: `GET /alarm` → `getAlarmList`)
- 타입명: 함수명 기반 PascalCase 도출 (예: `AlarmListResponse`)
- 파일 경로 미입력 시 STEP 2로

## STEP 2. 파일 위치 확인 (경로 미입력 시)

```
1. 기존 파일에 추가 → 파일 경로 입력
2. 새 파일 생성     → 서비스명 입력
```

## STEP 3. 코드 미리보기

수정 전 반드시 생성될 코드를 출력한다.

- API 함수 (서비스 파일)
- Response 타입 (response.ts)
- Request 타입 (request.ts) — 요청 파라미터 있을 때만
- Query 옵션 (@query/서비스명.ts)

확인(y) 후에만 파일을 수정한다.

## STEP 4. 파일 수정

순서: API 함수 → Request 타입 → Response 타입 → Query 옵션

- 기존 파일: 맨 아래에 추가, 기존 코드 수정 금지
- 새 파일: 서비스 폴더 전체 생성

## 규칙

- 미리보기 없이 파일 수정 금지
- 기존 파일 수정 시 기존 코드 변경 금지
- 무한 스크롤이면 `infiniteQueryOptions` 사용 (`infinite: true` 입력 시)
- 수정 전 기존 파일을 반드시 읽어 import 경로·queryKey 구조·타입 네이밍 스타일 확인
