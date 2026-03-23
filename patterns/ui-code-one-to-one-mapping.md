# UI-코드 1:1 매핑 패턴

## 적용 버전

프레임워크 무관. React/Vue/Svelte 등 컴포넌트 기반 프레임워크에서 공통 적용.

## 핵심 원칙

화면에 보이는 순서와 JSX 코드 순서가 일치해야 한다. 코드를 읽으면 화면을 예측할 수 있고, 화면을 보면 코드를 찾을 수 있어야 한다.

## 세 가지 규칙

### 1. JSX 순서 = 화면 순서

```tsx
// Good: 화면 위에서 아래 순서와 일치
function SavingsCalculatorPage() {
  return (
    <>
      <AmountInput label={'목표 금액'} />
      <AmountInput label={'월 납입액'} />
      <SavingsTermsSelect label={'저축 기간'} />
      <TabSection />
      <ProductList />
    </>
  );
}
```

### 2. 컴포넌트명은 UI 구현체가 아닌 행위/목적 기반

```tsx
// Bad: UI 구현체에 종속
const FloorBottomSheet = () => { ... };
const DatePickerModal = () => { ... };

// Good: 행위/목적 중심
const FloorSelectField = () => { ... };
const DatePicker = () => { ... };
```

UI가 BottomSheet에서 Modal로, 혹은 인라인 Select로 바뀌어도 이름 변경이 필요 없다.

### 3. 컴포넌트 내부도 선언 → 가공 → 렌더링 순서 유지

```tsx
function ProductList({ filters }) {
  // 1. 선언 (데이터 페칭)
  const { data: products } = useSuspenseQuery(queryOptions);

  // 2. 가공
  const filteredProducts = products.filter(p => filters.every(f => f(p)));

  // 3. 렌더링
  return <>{filteredProducts.map(p => <ProductItem key={p.id} product={p} />)}</>;
}
```

## 왜 이렇게 하는가

- 새로운 팀원이 코드를 읽을 때 화면과 대조하며 빠르게 파악 가능
- 디자인 변경 시 코드에서 수정할 위치를 즉시 특정 가능
- 코드 리뷰 시 화면 흐름과 불일치하는 부분을 쉽게 발견
