# Engineering Patterns

프로젝트에서 실제 적용한 프론트엔드 설계 패턴 모음.
각 패턴에는 적용 버전, Before/After 코드, 그리고 왜 이렇게 하는지에 대한 설명이 포함되어 있습니다.

## Patterns

### Suspense & 비동기
- [Suspense 선언적 비동기](patterns/suspense-declarative-async.md) — `useSuspenseQuery`로 `isLoading` 분기 제거
- [Suspense 경계 세분화](patterns/suspense-boundary-segmentation.md) — 섹션별 독립 Suspense + ErrorBoundary
- [컴포넌트 Loading/Error Co-location](patterns/component-loading-error-colocation.md) — `.Loading`, `.Error` 정적 서브컴포넌트

### 컴포넌트 설계
- [UI-코드 1:1 매핑](patterns/ui-code-one-to-one-mapping.md) — 화면 순서 = JSX 순서, 행위 기반 네이밍

### 데이터 & 상태
- [술어 필터 파이프라인](patterns/predicate-filter-pipeline.md) — 순수 함수 필터를 `select`에서 체이닝
- [URL searchParams 상태 관리](patterns/url-searchparams-state.md) — 필터를 URL로 직렬화
- [queryOptions 팩토리](patterns/query-options-factory.md) — shared 레이어에서 queryKey 공유

### TypeScript
- [탈진적 패턴 매칭](patterns/exhaustive-pattern-matching.md) — `ts-pattern`의 `.exhaustive()`
- [타입 단언 대신 타입 가드](patterns/type-guard-over-assertion.md) — `as` 대신 `value is T`

## Architecture Decisions

_작성 예정_
