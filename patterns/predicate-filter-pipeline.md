# 술어(Predicate) 필터 파이프라인 패턴

## 적용 버전

| 패키지 | 최소 버전 | 비고 |
|--------|----------|------|
| `@tanstack/react-query` | `^5` | `select` 옵션에서 캐시 공유 필터링 |

## 핵심 원칙

필터 조건을 데이터 조회 로직에 하드코딩하지 않고, 단일 책임 순수 함수로 분리한 뒤 파이프라인으로 체이닝한다.

## 패턴

### 1. 순수 필터 함수 정의

```typescript
// filter.ts - 각 함수는 단 하나의 조건만 검사
type FilterFn<T> = (item: T) => boolean;

const filterByCapacity = (room: Room, attendees: number): boolean => {
  if (attendees <= 0) return true;
  return room.capacity >= attendees;
};

const filterByEquipment = (room: Room, equipment: Equipment[]): boolean => {
  if (equipment.length === 0) return true;
  return equipment.every(e => room.equipment.includes(e));
};

const filterByFloor = (room: Room, floor?: number): boolean => {
  if (floor == null) return true;
  return room.floor === floor;
};
```

### 2. select에서 파이프라인 체이닝

```typescript
// queryOptions
select: ({ rooms, reservations }) =>
  orderByFloorAndName(
    rooms
      .filter(r => filterByCapacity(r, attendees))
      .filter(r => filterByEquipment(r, equipment))
      .filter(r => filterByFloor(r, floor))
  ),
```

### 3. 또는 술어 배열을 외부에서 주입

```tsx
// 사용처에서 필터 조합
<ProductList
  filters={[
    x => filterByMonthlyAmount(x, monthlyAmount),
    x => filterBySavingsTerms(x, savingsTerms),
  ]}
  orderBy={orderByAnnualRate}
/>

// 컴포넌트 내부에서 평가
const filtered = products.filter(x => filters.every(f => f(x)));
```

## 왜 이렇게 하는가

- 각 필터 함수를 독립적으로 테스트 가능
- `select`에서 필터링하면 서버 캐시 원본은 유지되고 파생 결과만 변환됨
- 새로운 필터 조건 추가 시 기존 코드 수정 없이 함수만 추가 (OCP)
- `filters.every(f => f(x))`로 AND 조합이 자연스럽게 표현됨
