## 타입스크립트 13일차 정리: 조건부 타입 & 추론

### 🎯 조건부 타입 (Conditional Type)

```
type A = number extends string ? string : number; // A: number

type ObjA = { a: number };
type ObjB = { a: number; b: number };

type B = ObjB extends ObjA ? number : string; // B: number
```

- A extends B ? C : D 구조로 작성됨

- extends는 서브타입 관계를 검사

- 조건이 true면 C, false면 D를 결과 타입으로 반환

### 💡 제네릭과 조건부 타입

```
type StringNumberSwitch<T> = T extends number ? string : number;

let varA: StringNumberSwitch<number>; // string
let varB: StringNumberSwitch<string>; // number
```

- 호출 시 전달된 제네릭 타입에 따라 결과가 바뀜
- 매우 범용적인 타입 설계가 가능

### 예시: 문자열만 처리하는 함수 타입 정의

```
function removeSpaces<T>(text: T): T extends string ? string : undefined;

function removeSpacesImpl(text: any) {
  if (typeof text === "string") {
    return text.replaceAll(" ", "") as any;
  }
  return undefined as any;
}

const result1 = removeSpacesImpl("hi im winterlood"); // string
const result2 = removeSpacesImpl(undefined); // undefined


```

### 분산적인 조건부

```
type StringNumberSwitch<T> = T exjtends number ? string : number;

let varA : StringNumberSwitch<number> // string
let varB : StringNumberSwitch<string> // number

let c : StringNumberSwitch<number|string>;
// 이렇게 사용하면 한번은 StringNumberSwitch<number> 이렇게, 나머지 한번은 StringNumberSwitch<string> 이렇게 들어간다 그 결과를 유니온으로 묶는다.

그래서 결과가 타입은 string | number로 된다
```

- T가 유니온 타입이면 조건부 타입이 각 요소에 개별적으로 적용됨

- 이걸 분산(distributed) 된다고 표현함

```
let d: StringNumberSwitch<boolean | number | string>;

// 1단계
StringNumberSwitch<boolean>  |
StringNumberSwitch<number> |
StringNumberSwitch<string>

// 2단계
number |
string |
number

// 결과: number | string

```

### 분산을 막는 방법

ts
복사
편집

```
type NonDistributive<T> = [T] extends [number] ? string : number;

type A = NonDistributive<number | string>; // number

```

### 실용적인 예제 : union에서 특정 타입을 제거하는 기능

### 🧹 Exclude<T, U>

- T에서 U를 제거

```
type Exclude<T, U> = T extends U ? never : T;

type A = Exclude<number | string | boolean, string>;

// 단계적으로 보면
// Exclude<number, string> → number
// Exclude<string, string> → never
// Exclude<boolean, string> → boolean


// 결과: number | boolean
```

- never는 왜 없나? never라는 아무것도 없는 공집합을 합집합 하면 number | boolean 이 나온다.

### Extract<T,U>

- T에서 U에 해당하는 타입만 추출

```
type Extract <T, U> = T extends U ? T : never ;
type B = Extract<number | string | boolean, string>;

// 1 단계
// Extract<number, string> |
// Extract<string, string> |
// Extract<boolean, string>


// 2 단계
// never |
// string |
// never

// 최종 결과
// string

분산적인 조건부 타입을 막고 싶다면
[T] extends [number] ? string : number
// 이렇게 하면 분산적으로 가지 않는다.
```

### 타입 추론 with infer

`조건부 타입 내에서 특정 타입만 추론해낼 수 있는 기능`

```
type Func = () => string;
type FuncB = () => number;

type ReturnType<T> = T extends () => string ? string : never

type A = ReturnType<Func>; // string
type B = ReturnType<FuncB>; // never


// infer 사용
type ReturnType<T> = T extends () => infer R ? R : never  -> R을 추론해라

() => string 이 infer R 타입에 서브 타입인지 확인하게 된다. 이때 infer R은 funcB의 () => number를 참으로 만들게 끔 동작합니다. 이때 R은 그러면 number로 추론이 된다.


type ReturnType<T> = T extends () => infer R ? R : never;

type A = ReturnType<() => string>; // string
type B = ReturnType<() => number>; // number
type C = ReturnType<number>;       // never (함수가 아님)


```

### 예제

```
type PromiseUnpack<T> = T extends Promise<infer R> ? R : never;

type P1 = PromiseUnpack<Promise<number>>; // number
type P2 = PromiseUnpack<Promise<string>>; // string
```

- Promise<Something> 형태의 타입에서 Something만 추출하고 싶을 때 사용

| 개념                  | 설명                                         |
| --------------------- | -------------------------------------------- |
| `T extends U ? A : B` | 조건에 따라 타입 분기                        |
| 분산 조건부 타입      | 유니온 타입에 대해 분기 조건 개별 적용됨     |
| `Exclude<T, U>`       | `T`에서 `U`를 제거                           |
| `Extract<T, U>`       | `T`에서 `U`만 추출                           |
| `infer`               | 조건부 타입 내에서 특정 타입을 추론해서 사용 |
| `[T] extends [U]`     | 분산 조건부 타입 방지 패턴                   |

### Quiz 1.

```
/*
[ 문제 소개 ]
다음 요구사항을 만족하는 IsProductKey<T> 타입을 완성하세요
- IsProductKey 타입은 조건부 타입으로 다음 조건에 따라 각각 다른 타입으로 결정됩니다.
  - T가 Product의 key(프로퍼티 이름)중 하나일 경우 결과는 true 타입이 됩니다.
  - T가 Product의 key(프로퍼티 이름)중 하나가 아닐 경우 결과는 false 타입이 됩니다.
  - 예를 들면 다음과 같습니다.
    - ex) IsProductKey<"id"> 타입은 true 타입이 됩니다.
    - ex) IsProductKey<"author"> 타입은 false 타입이 됩니다.
*/

/* [Quiz] 아래의 코드를 수정하세요 */
interface Product {
  id: number;
  name: string;
  price: number;
  seller: {
    id: number;
    name: string;
    company: string;
  };
}

type IsProductKey<T> = T extends keyof Product ? true : false;

/* [Test] 여기부터는 정답을 체크하기 위한 용도로 수정하지 않습니다 */
import { Equal, Expect } from "@type-challenges/utils";

type TestCases = [
  Expect<Equal<IsProductKey<"id">, true>>,
  Expect<Equal<IsProductKey<"name">, true>>,
  Expect<Equal<IsProductKey<"author">, false>>,
  Expect<Equal<IsProductKey<"winterlood">, false>>
];
```

### Quiz 2.

```
/*
[ 문제 소개 ]
다음 조건을 만족하는 Extract<T, U> 타입을 구현하세요
- Extract<T, U> 타입은 T로부터 U만 추출하는 타입입니다.
  - ex) Extract<string | boolean, boolean>은 boolean 타입이 됩니다.
  - ex) Extract<number | string, string>은 string 타입이 됩니다.

(힌트💡 강의 중 직접 구현하는 Exclude 타입의 반대라고 생각해보세요!)
*/

/* [Quiz] 아래의 코드를 수정하세요 */
type Extract<T, U> = T extends U ? T : never;

/* [Test] 여기부터는 정답을 체크하기 위한 용도로 수정하지 않습니다 */
import { Equal, Expect } from "@type-challenges/utils";

type TestCases = [
  Expect<Equal<Extract<string | number, string>, string>>,
  Expect<Equal<Extract<string | number, number>, number>>
];
```

### Quiz 3.

```
/*
[ 문제 소개 ]
배열 타입의 요소를 추출하는 InferArrayType<T> 타입을 구현하세요
*/

/* [Quiz] 아래의 코드를 수정하세요 */
type InferArrayType<T> = T extends (infer R)[] ? R : never;

/* [Test] 여기부터는 정답을 체크하기 위한 용도로 수정하지 않습니다 */
import { Expect, Equal } from "@type-challenges/utils";

const arr1 = [1, 2, 3];
const arr2 = ["hello", "myname", "winterlood"];
const arr3 = [1, true, "hi"];

type TestCases = [
  Expect<Equal<InferArrayType<typeof arr1>, number>>,
  Expect<Equal<InferArrayType<typeof arr2>, string>>,
  Expect<Equal<InferArrayType<typeof arr3>, number | string | boolean>>
];s
```
