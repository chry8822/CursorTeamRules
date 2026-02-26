# Create API Service

아래 규칙에 따라 새로운 API 서비스 파일 구조를 생성해줘.
생성 전에 파일 목록 먼저 보여주고, 생성 여부는 내가 결정한다.

## 입력값
- 서비스명: [서비스명] 하나 또는 공백으로 구분된 여러 개 (예: dailytalk user group)
- 여러 개 입력 시 각 서비스명에 대해 동일한 구조를 동시에 생성한다

## 생성할 파일 구조 (서비스명마다 반복)
```
src/api/[서비스명]/
├── [ServiceName]Service.ts   # 메인 서비스 함수 (PascalCase)
├── request.ts                # 요청 타입
└── response.ts               # 응답 타입

src/api/@query/
└── [서비스명].ts             # TanStack Query queryOptions
```

※ const.ts는 코드성 데이터(createEntries)가 필요할 때만 별도 생성하므로 자동 생성에서 제외

## 주의사항
- 각 파일은 아래 템플릿을 **그대로** 사용한다
- 기존 파일의 패턴을 참고하거나, 템플릿에 없는 내용을 임의로 추가하지 않는다
- 템플릿의 `[서비스명]`, `[ServiceName]`만 치환하고 나머지는 변경하지 않는다

## 각 파일 템플릿

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


### request.ts
```ts
// 응답 타입 정의
export interface [ServiceName]Response {
    [ServiceName] : string  
}
```

### response.ts
```ts
// 응답 타입 정의
export interface [ServiceName]Response {
    [ServiceName] : string  
}
```

### @query/[서비스명].ts
```ts
import { QueryKey, queryOptions } from '@tanstack/react-query';
import { get[ServiceName] } from '@api/[서비스명]/[ServiceName]Service';

export const [서비스명]Query = {
  all: () =>
    queryOptions({
      queryKey: ['[서비스명]'] as const,
    }),
};
```

## 생성 전 체크
1. 입력된 서비스명을 공백 기준으로 분리하여 각각 독립적으로 처리한다
2. 각 서비스명에 대해 src/api/[서비스명]/ 폴더가 이미 존재하는지 확인
3. 존재하는 서비스는 덮어쓰지 말고 반드시 먼저 알려줄 것
4. 전체 생성할 파일 목록을 먼저 출력 후 진행 여부 물어볼 것

## 생성 후 안내
생성 완료 후 아래 내용 안내:
- 각 서비스별로 추가로 작성해야 할 타입 목록
- 공통 타입이 필요하면 src/api/_common/types.ts 활용 안내

