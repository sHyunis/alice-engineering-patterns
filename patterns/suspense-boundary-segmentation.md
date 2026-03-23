# Suspense 경계 세분화 패턴

## 적용 버전

| 패키지 | 최소 버전 |
|--------|----------|
| `react` | `^18` |
| `@tanstack/react-query` | `^5` |

## 핵심 원칙

화면의 독립 섹션마다 Suspense + ErrorBoundary를 분리하여, 한 섹션의 실패가 다른 섹션에 전파되지 않게 한다.

## Before

```tsx
function DashboardPage() {
  const { data: timeline, isLoading: isTimelineLoading } = useQuery(...);
  const { data: myList, isLoading: isMyListLoading } = useQuery(...);

  if (isTimelineLoading || isMyListLoading) return <FullPageSpinner />;

  return (
    <>
      <Timeline data={timeline} />
      <MyList data={myList} />
    </>
  );
}
```

## After

```tsx
function DashboardPage() {
  return (
    <>
      <ErrorBoundary fallback={<TimelineError />}>
        <Suspense fallback={<Timeline.Loading />}>
          <Timeline />
        </Suspense>
      </ErrorBoundary>

      <ErrorBoundary fallback={<MyList.Error />}>
        <Suspense fallback={<MyList.Loading />}>
          <MyList />
        </Suspense>
      </ErrorBoundary>
    </>
  );
}
```

## 왜 이렇게 하는가

- Timeline API가 실패해도 MyList는 정상 렌더링된다
- 각 섹션이 독립적으로 로딩되어 사용자가 빠르게 콘텐츠를 볼 수 있다
- 에러 복구(retry) 범위가 섹션 단위로 좁아진다
