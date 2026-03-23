# Suspense 선언적 비동기 패턴

## 적용 버전

| 패키지 | 최소 버전 | 비고 |
|--------|----------|------|
| `react` | `^18` | Suspense for data fetching 지원 |
| `@tanstack/react-query` | `^5` | `useSuspenseQuery` 도입 (v4는 `suspense: true` 옵션) |

## 핵심 원칙

`isLoading` 분기를 컴포넌트 내부에서 제거하고, Suspense 경계로 로딩 상태 처리를 부모에게 위임한다.

## Before

```tsx
function ProductList({ filters }) {
  const { data: products, isLoading } = useQuery({
    queryKey: ['products'],
    queryFn: fetchProducts,
  });

  if (isLoading) return <LoadingSpinner />;

  return <>{products.map(p => <ProductItem key={p.id} product={p} />)}</>;
}
```

## After

```tsx
function ProductList({ filters }) {
  const { data: products = [] } = useSuspenseQuery({
    queryKey: ['products'],
    queryFn: fetchProducts,
  });

  // data는 항상 존재가 보장됨
  return <>{products.map(p => <ProductItem key={p.id} product={p} />)}</>;
}

// 사용처
<Suspense fallback={<LoadingSpinner />}>
  <ProductList filters={filters} />
</Suspense>
```

## 왜 이렇게 하는가

- 컴포넌트가 "데이터가 있는 상태"만 다루면 되므로 로직이 단순해진다
- 로딩 UI 결정권이 소비자(부모)에게 있어 재사용성이 높아진다
- `data`가 `undefined`일 가능성이 없어 타입 안전성이 확보된다
