# 탈진적 패턴 매칭 (Exhaustive Pattern Matching)

## 적용 버전

| 패키지 | 최소 버전 |
|--------|----------|
| `ts-pattern` | `^5` |
| `typescript` | `^5` |

## 핵심 원칙

뷰 타입 분기를 `if/else`나 `switch`가 아닌 `ts-pattern`의 `.exhaustive()`로 처리하여, 모든 케이스가 컴파일 타임에 처리됐음을 보장한다.

## Before

```tsx
// switch - default가 런타임 버그를 숨김
function TabContent({ view }: { view: TabType }) {
  switch (view) {
    case 'products':
      return <ProductList />;
    case 'results':
      return <CalculationResult />;
    default:
      return null; // 새 탭 추가 시 여기로 빠져도 에러 없음
  }
}
```

## After

```tsx
import { match } from 'ts-pattern';

const TABS = {
  products: 'products',
  results: 'results',
} as const;

type TabType = (typeof TABS)[keyof typeof TABS];

// .exhaustive() - 새 탭 추가 시 컴파일 에러 발생
{match(view)
  .with(TABS.products, () => (
    <Suspense fallback={<ProductList.Loading />}>
      <ProductList />
    </Suspense>
  ))
  .with(TABS.results, () => <CalculationResult />)
  .exhaustive()}
```

## 왜 이렇게 하는가

- `TABS`에 새 값을 추가하면 `.exhaustive()`에서 타입 에러가 즉시 발생
- `default` 케이스가 런타임 버그를 숨기는 문제를 차단
- `as const` + 파생 타입으로 런타임 객체와 타입을 단일 소스에서 관리
