# 타입 단언 대신 타입 가드 패턴

## 적용 버전

| 패키지 | 최소 버전 |
|--------|----------|
| `typescript` | `^4.5` |

## 핵심 원칙

`as` 타입 단언은 런타임 검증 없이 타입을 강제 변환한다. 타입 가드(`value is T`)를 사용하면 런타임 안전성이 확보된다.

## Before

```typescript
// as 단언 - 잘못된 값이 들어와도 타입 에러 없음
const equipment = params.getAll('equipment') as Equipment[];
```

## After

```typescript
const VALID_EQUIPMENT = ['projector', 'whiteboard', 'monitor'] as const;
type Equipment = (typeof VALID_EQUIPMENT)[number];

function isEquipment(value: string): value is Equipment {
  return VALID_EQUIPMENT.includes(value as Equipment);
}

// 런타임에 유효하지 않은 값을 필터링
const equipment = params.getAll('equipment').filter(isEquipment);
```

## 왜 이렇게 하는가

- URL에서 오는 값은 외부 입력이므로 신뢰할 수 없다
- `as`는 컴파일러를 속이기만 하고 런타임에는 아무 검증도 하지 않는다
- 타입 가드를 통과한 값은 타입과 런타임 값이 모두 보장된다
