## 타입스크립트 10일차 정리

### 제네릭

`일반적인 포괄적인 함수 - 모든 타입에 두루두루 사용할 수 있는 범용적인 함수나 타입을 만드는 방법`

```
function func<T>(value: T): T {
    return value;
}

```

- `<T>는 타입 변수(Type Variable) 로, 실제 타입은 함수를 호출할 때 결정됨`

- `타입 변수와 함께 타입의 값을 인수로 받아서 범용적으로`

```
let a = func<number>(10);    // T = number
let b = func<string>("hi");  // T = string
```

### ⚙️ 제네릭 응용: 타입 변수 여러 개

```
function swap(a: any, b: any){
    return [b, a];
}

const [a, b] = swap(1, 2);
=> swap("1", 2); 하면 타입 오류 발생.

a와 b의 타입이 같을 수 있지만 다를 수 있는 상황에서는 다른 타입은 타입 변수를 하나만 쓰는게 아닌 두개를 쓰자.

<T, U> => 바꾸면서 (a: T, B:U)


function swap<T, U>(a: T, b: U): [U, T] {
  return [b, a];
}

const [x, y] = swap("hello", 123); // [123, "hello"]

```

```
function returnFirstValue<T>(data: T[]): T {
  return data[0];
}

const val = returnFirstValue([1, 2, 3]); // val: number

// 첫 번째 요소만 특정 타입으로 제한하고 싶을 때
function returnFirstValue<T>(data: [T, ...unknown[]]): T {
  return data[0];
}

const val = returnFirstValue([1, "hello", true]); // val: number

// 첫 번째 요소만 특정 타입으로 제한하고 싶을 때

function returnFirstValue<T>(data: [T, ...unknown[]]): T {
  return data[0];
}

const val = returnFirstValue([1, "hello", true]); // val: number

[T, ...unknown[]]: 첫 번째 요소는 T, 나머지는 어떤 타입이든 가능

첫 번째 요소만 타입을 고정하고 나머지는 무시 가능

let str = returnFirstValue([1,"hello","mynameis"]);
=> str의 타입 추론 결과는 string|number가 되며 T는 [number,string][] 튜플타입이 된다.

```

- `만약 첫번째 요소가 number로 타입을 추론하고 싶으면
<T>(data: [T, ...unknown[]])을 하게 되고 T첫번째 요소가 number이고 나머지는 몰라도 되는 것`
- 첫 번째 요소만 타입을 고정하고 나머지는 무시 가능

```
// length 속성을 가진 값만 허용
function getLength<T extends { length: number }>(data: T): number {
  return data.length;
}

getLength([1, 2, 3]);            // ✅ Array
getLength("hello");             // ✅ String
getLength({ length: 10 });      // ✅ 객체
getLength(123);                 // ❌ number는 length 속성이 없음



<T extends { length: number}>(data:T)
: T를 length 타입이 number 타입으로 가지고 있는 객체를 확장하는 타입으로 제한한다.
```

- extends 를 사용하여 타입의 범위를 제한(bounded generic) 할 수 있음

### 🧩 제네릭으로 고차 함수 구현하기

### map 메서드 타입 정의하기

```
function map<T, U>(arr: T[], callback: (item: T) => U): U[] {
  let result: U[] = [];
  for (let i = 0; i < arr.length; i++) {
    result.push(callback(arr[i]));
  }
  return result;
}

// 사용 예시
const doubled = map([1, 2, 3], (item) => item * 2); // number[]
const uppercased = map(["hi", "hello"], (item) => item.toUpperCase()); // string[]
```

- T: 입력값 타입
- U: 반환값 타입

### forEach 함수 타입 정의

```
function forEach<T>(arr: T[], callback: (item: T) => void): void {
  for (let i = 0; i < arr.length; i++) {
    callback(arr[i]);
  }
}

// 사용 예시
forEach([1, 2, 3], (item) => {
  console.log(item.toFixed()); // item은 number로 추론됨
});

```

| 항목                | 설명                                     |
| ------------------- | ---------------------------------------- |
| `T`, `U`            | 타입 변수 (Generic Type Parameters)      |
| `<T>`               | 타입스크립트에서 제네릭 사용의 기본 문법 |
| `T extends ...`     | 타입 변수의 범위 제한                    |
| `[T, ...unknown[]]` | 첫 요소만 고정하고 나머지 무시           |
| `map`, `forEach`    | 고차 함수 구현에도 제네릭 활용 가능      |

### Quiz 1.

```
/*
[ 문제 소개 ]
다음 요구사항을 만족하는 func 함수의 타입을 정의하세요
- func 함수는 매개변수를 그대로 반환하는 함수입니다.
- 매개변수 value에는 모든 타입의 값이 들어올 수 있습니다.
- 반환값의 타입은 매개변수로 전달된 값의 타입과 같습니다.
  - ex) value의 값이 1일 경우 반환값은 number 타입입니다.
  - ex) value의 값이 'hi'일 경우 반환값은 string 타입입니다.
  - ex) value의 값이 [1,2]일 경우 반환값은 number[] 타입입니다.
*/

/* [Quiz] 아래의 코드를 수정하세요 */
function func<T>(value: T) {
  return value;
}

/* [Test] 여기부터는 정답을 체크하기 위한 용도로 수정하지 않습니다 */

import { Equal, Expect, NotAny } from "@type-challenges/utils";

const tc1: number = func(1);
const tc2: string = func("hello");
const tc3: boolean = func(true as boolean);
const tc4: number[] = func([1, 2, 3]);

type TestCases = [
  Expect<NotAny<Parameters<typeof func>[0]>>, // 첫번째 파라미터가 any 타입이 아닐 것
  Expect<Equal<typeof tc1, number>>,
  Expect<Equal<typeof tc2, string>>,
  Expect<Equal<typeof tc3, boolean>>,
  Expect<Equal<typeof tc4, number[]>>
];

```

### Quiz 2.

```
/*
[ 문제 소개 ]
다음 요구사항을 만족하는 getLastValue 함수의 타입을 정의하세요
- getLastValue 함수는 매개변수로 배열을 받아 배열의 마지막 값을 반환하는 함수입니다.
- 매개변수 data에는 빈 배열을 제외한 모든 배열이 들어올 수 있습니다.
- 반환값의 타입은 매개변수 data 배열의 마지막 요소의 타입이어야 합니다.
  - ex) data 배열의 값이 [1, "hello"] 일 경우 반환값은 string 타입입니다.
  - ex) data 배열의 값이 ["hello", 1] 일 경우 반환값은 number 타입입니다.
*/

/* [Quiz] 아래의 코드를 수정하세요 */
function getLastValue<T>(data: [any,T]):T {
  return data[data.length - 1];
}



/* [Test] 여기부터는 정답을 체크하기 위한 용도로 수정하지 않습니다 */
const a: string = getLastValue([1, 2, "3"]);
const b: boolean = getLastValue([1, 2, true]);

import { Equal, Expect, NotAny } from "@type-challenges/utils";

const tc1 = getLastValue([1, 1]);
const tc2 = getLastValue([1, "2"]);
const tc3 = getLastValue([1, true]);
const tc4 = getLastValue([1, undefined]);

type TestCases = [
  Expect<NotAny<Parameters<typeof getLastValue>[0]>>, // 첫번째 파라미터가 any 타입이 아닐 것
  Expect<Equal<typeof tc1, number>>,
  Expect<Equal<typeof tc2, string>>,
  Expect<Equal<typeof tc3, boolean>>,
  Expect<Equal<typeof tc4, undefined>>
];

```

### Quiz 3.

```
/*
[ 문제 소개 ]
다음 요구사항을 만족하는 map 함수의 타입을 구현하세요
- map 함수는 자바스크립트 배열 메서드 map을 본따 만든 함수입니다.
- 2개의 매개변수 arr과 callback을 받습니다.
- arr 배열의 모든 요소에 callback 함수를 수행한 결과를 배열로 모아 반환합니다.
*/

/* [Quiz] 아래의 코드를 수정하세요 */
function map<T,U>(arr:T[], callback:(item:T) => U):U[] {
  let result = [];
  for (let i = 0; i < arr.length; i++) {
    result.push(callback(arr[i]));
  }
  return result;
}

/* [Test] 여기부터는 정답을 체크하기 위한 용도로 수정하지 않습니다 */
import { Equal, Expect, NotAny } from "@type-challenges/utils";
const tc1: number[] = map([1, 2, 3], (item) => item);
const tc2: string[] = map([1, 2, 3], (item) => item.toString());

type TestCases = [
  Expect<NotAny<Parameters<typeof map>[0]>>,
  Expect<NotAny<Parameters<typeof map>[1]>>,
  Expect<Equal<typeof tc1, number[]>>,
  Expect<Equal<typeof tc2, string[]>>
];

```
