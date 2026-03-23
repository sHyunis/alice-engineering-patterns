# queryOptions 팩토리 패턴

## 적용 버전

| 패키지 | 최소 버전 | 비고 |
|--------|----------|------|
| `@tanstack/react-query` | `^5` | `queryOptions` 헬퍼 도입 |

## 핵심 원칙

쿼리 설정을 `queryOptions` 팩토리 함수로 추출하여 shared 레이어에 배치한다. 여러 컴포넌트가 동일한 queryKey로 캐시를 공유하고, invalidation이 일관되게 동작한다.

## 패턴

```typescript
// shared/queries/getRoomsQueryOptions.ts
import { queryOptions } from '@tanstack/react-query';

function getRoomsQueryOptions() {
  return queryOptions({
    queryKey: ['rooms'] as const,
    queryFn: getRooms,
    staleTime: 5 * 60 * 1000,
  });
}

// shared/queries/getReservationsQueryOptions.ts
function getReservationsQueryOptions(date: string) {
  return queryOptions({
    queryKey: ['reservations', date] as const,
    queryFn: () => getReservations(date),
    enabled: Boolean(date),
  });
}
```

```tsx
// 여러 컴포넌트에서 동일한 queryOptions 사용
function FloorSelectField() {
  const { data } = useSuspenseQuery(getRoomsQueryOptions());
  // ...
}

function AvailableRoomList({ filters }) {
  const { data } = useSuspenseQuery({
    ...getRoomsQueryOptions(),
    select: (rooms) => rooms.filter(r => filters.every(f => f(r))),
  });
  // 동일한 queryKey → 캐시 공유, select로 파생 결과만 다름
}
```

## 왜 이렇게 하는가

- queryKey가 한 곳에서 관리되어 오타나 불일치 방지
- `invalidateQueries` 시 동일한 queryKey를 사용하는 모든 컴포넌트가 일관되게 갱신
- `select`로 같은 캐시 데이터에서 다른 파생 결과를 만들어 중복 네트워크 요청 방지
- 타입 추론이 `queryOptions` 반환값에서 자동으로 전파
