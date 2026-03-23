# URL searchParams 기반 상태 관리 패턴

## 적용 버전

| 패키지 | 최소 버전 | 비고 |
|--------|----------|------|
| `react-router-dom` | `^6` | `useSearchParams` 훅 지원 |

## 핵심 원칙

필터/폼 상태를 `useState`가 아닌 URL searchParams로 직렬화하여, 공유/새로고침/뒤로가기가 자연스럽게 동작하게 한다.

## 패턴

### 1. 파싱/직렬화 순수 함수

```typescript
// searchParams.ts
function parseSearchParams(params: URLSearchParams): BookingParams {
  return {
    date: params.get('date') ?? '',
    startTime: params.get('startTime') ?? '',
    endTime: params.get('endTime') ?? '',
    attendees: Number(params.get('attendees')) || 1,
    equipments: params.getAll('equipments').filter(isEquipment),
    floor: params.has('floor') ? Number(params.get('floor')) : undefined,
  };
}

function buildSearchParams(params: BookingParams): Record<string, string> {
  const result: Record<string, string> = {};
  // 기본값은 URL에서 생략하여 URL 간결화
  if (params.date) result.date = params.date;
  if (params.attendees !== 1) result.attendees = String(params.attendees);
  return result;
}
```

### 2. 읽기/쓰기 훅 분리

```typescript
// useBookingParams.ts - 읽기
function useBookingParams(): BookingParams {
  const [searchParams] = useSearchParams();
  return parseSearchParams(searchParams);
}

// useUpdateBookingParam.ts - 쓰기 (머지 방식)
function useUpdateBookingParam() {
  const [searchParams, setSearchParams] = useSearchParams();
  return useCallback((partial: Partial<BookingParams>) => {
    const current = parseSearchParams(searchParams);
    setSearchParams(buildSearchParams({ ...current, ...partial }), { replace: true });
  }, [searchParams, setSearchParams]);
}
```

## 왜 이렇게 하는가

- URL 공유만으로 동일한 필터 상태를 재현 가능
- 브라우저 뒤로가기/앞으로가기가 필터 변경 이력과 일치
- 새로고침해도 필터 상태가 유지됨
- 상태 관리 라이브러리 의존성 제거 가능
