---
name: cursor-create-api
description: API 서비스 파일 전체 구조 생성. 서비스명을 입력받아 ServiceName.ts, request.ts, response.ts, @query/서비스명.ts 파일을 컨벤션에 맞게 생성한다. 사용자가 API 파일 생성, /create-api, 새 서비스 API 만들기를 요청할 때 사용한다.
---

# Create API Service

프로젝트에 `.cursor/commands/create-api.md` 가 있으면 반드시 그 파일을 읽고 절차대로 실행한다.

없을 경우 아래 절차를 따른다.

## 입력

서비스명 하나 또는 공백으로 구분된 여러 개 (예: `dailytalk user group`)

## 생성 파일 구조

```
src/api/[서비스명]/
├── [ServiceName]Service.ts   # 메인 서비스 함수
├── request.ts                # 요청 타입
└── response.ts               # 응답 타입

src/api/@query/
└── [서비스명].ts             # TanStack Query queryOptions
```

## 절차

1. 입력된 서비스명으로 생성할 파일 목록을 먼저 출력한다
2. 기존 파일 존재 여부를 확인하고 덮어쓰지 않는다
3. 사용자 확인 후 생성한다
4. 생성 후 추가 작성 필요 항목을 안내한다

## 파일 템플릿

### [ServiceName]Service.ts
```ts
import API from '@api/client';
import type { [ServiceName]Request } from '@api/[서비스명]/request';
import type { [ServiceName]Response } from '@api/[서비스명]/response';

/**
 * [서비스명] 상세 조회
 * @param id - [서비스명] ID
 * @returns Promise<[ServiceName]Response>
 */
export const get[ServiceName] = (id: number): Promise<[ServiceName]Response> => {
  return API.withAuth.get<[ServiceName]Response>(`/carenation/community/[서비스명]/${id}`);
};
```

### @query/[서비스명].ts
```ts
import { queryOptions } from '@tanstack/react-query';
import { get[ServiceName] } from '@api/[서비스명]/[ServiceName]Service';

export const [서비스명]Query = {
  all: () =>
    queryOptions({
      queryKey: ['[서비스명]'] as const,
    }),
};
```
