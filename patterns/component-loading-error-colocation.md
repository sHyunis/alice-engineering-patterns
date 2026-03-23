# 컴포넌트 Loading/Error Co-location 패턴

## 적용 버전

| 패키지 | 최소 버전 |
|--------|----------|
| `react` | `^18` |

## 핵심 원칙

컴포넌트가 자신의 로딩/에러 상태 UI를 정적 프로퍼티로 소유한다. 사용처에서 별도 import 없이 `Component.Loading`으로 fallback을 선언할 수 있어, 어떤 UI가 어떤 상태에 대응하는지 코드만으로 파악된다.

## 패턴

```tsx
function FloorSelectField({ value, onChange }) {
  const { data: floors } = useSuspenseQuery(getFloorsQueryOptions());

  return (
    <Select value={value} onChange={onChange}>
      {floors.map(f => <Select.Option key={f.id} value={f.id}>{f.name}</Select.Option>)}
    </Select>
  );
}

FloorSelectField.Loading = function () {
  return <Select.Skeleton />;
};

FloorSelectField.Error = function () {
  return <div>{'층 정보를 불러올 수 없습니다'}</div>;
};
```

```tsx
// 사용처 - 자기 설명적 코드
<ErrorBoundary fallback={<FloorSelectField.Error />}>
  <Suspense fallback={<FloorSelectField.Loading />}>
    <FloorSelectField value={floor} onChange={setFloor} />
  </Suspense>
</ErrorBoundary>
```

## 왜 이렇게 하는가

- fallback UI가 컴포넌트와 같은 곳에 있어 유지보수가 쉽다
- 사용처 코드가 자기 설명적이다 (어떤 컴포넌트의 로딩/에러 상태인지 명확)
- 별도 파일이나 import 없이 `.Loading`, `.Error`로 접근 가능하다
