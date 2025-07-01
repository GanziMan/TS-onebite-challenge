## 타입스크립트 6일차 정리

### 🧱 타입 단언(Type Assertion)

타입 단언이란, 개발자가 타입스크립트에게
“이 값은 내가 알고 있는 이 타입이 맞아!”라고 직접 말해주는 문법입니다.

```
const value = someValue as SomeType;

```

A가 B의 슈퍼타입 이거나
A가 B의 서브타입이어야함

```
let num1 = 10 as never;     // ✅ 가능 (number는 never의 슈퍼타입)
let num2 = 10 as unknown;   // ✅ 가능 (unknown은 모든 타입의 슈퍼타입)
let num3 = 10 as string;    // ❌ 에러 (number와 string은 호환되지 않음)
```

### 🔒 const 단언

```
let num = 10 as const;
// num: 10 (number가 아니라 리터럴 타입)

let arr = [1, 2, 3] as const;
// arr: readonly [1, 2, 3]
```

- 값을 수정할 수 없는 상수로 간주됨

- 배열/객체는 자동으로 readonly 속성 부여

### 🚫 Non-Null 단언

옵셔널 체이닝(?.)에서 null 또는 undefined가 절대 아님을 보장하고 싶을 때 ! 사용

```
type Post = {
  title: string;
  author?: string;
};

let post: Post = {
  title: "게시글 1",
  author: "이정환",
};

const len: number = post.author!.length;
// `!` 덕분에 author는 절대 null이 아니라고 단언
```

- 📌 타입 검사기를 속이는 방식이기 때문에, 실제로 null일 가능성이 없을 때만 사용해야 합니다.
-

### 타입 좁히기 (Type Narrowing) - 조건문 등을 활용해 넓은 타입을 더 구체적인 타입으로 좁히는 과정

```
function process(value: number | string | Date) {
  if (typeof value === "number") {
    console.log(value.toFixed()); // number로 좁혀짐
  } else if (typeof value === "string") {
    console.log(value.toUpperCase()); // string으로 좁혀짐
  } else if (value instanceof Date) {
    console.log(value.getFullYear()); // Date로 좁혀짐
  }
}

```

### 🧬 서로소 유니온 타입 (Discriminated Union)

교집합이 없는 유니온 타입을 구성하고,

공통된 식별자(tag)를 기준으로 조건 분기하는 패턴

```
type LoadingTask = {
  state: "LOADING";
};

type FailedTask = {
  state: "FAILED";
  error: {
    message: string;
  };
};

type SuccessTask = {
  state: "SUCCESS";
  data: {
    result: string;
  };
};

type AsyncTask = LoadingTask | FailedTask | SuccessTask;

function processResult(task: AsyncTask) {
  switch (task.state) {
    case "LOADING":
      console.log("로딩 중...");
      break;
    case "FAILED":
      console.log("에러 발생:", task.error.message);
      break;
    case "SUCCESS":
      console.log("성공:", task.data.result);
      break;
  }
}

const loading: AsyncTask = {
  state: "LOADING",
};

const failed: AsyncTask = {
  state: "FAILED",
  error: {
    message: "오류 발생 원인은 ~",
  },
};

const success: AsyncTask = {
  state: "SUCCESS",
  data: {
    result: "정상적으로 완료되었습니다",
  },
};

```

| 개념                | 설명                                             |
| ------------------- | ------------------------------------------------ |
| `as` 단언           | 값의 타입을 강제로 지정                          |
| `as const`          | 값을 리터럴 타입 + readonly로 고정               |
| `!` (non-null 단언) | null 또는 undefined가 아님을 단언                |
| 타입 좁히기         | 조건문 등을 활용해 타입을 구체화                 |
| 서로소 유니온       | `state`처럼 구분 가능한 값을 통해 타입 분기 처리 |

### Quiz 1.

```
/*
[ 문제 소개 ]
타입 단언을 이용해 person 변수에 빈 객체를 할당하세요
(힌트. 초과 프로퍼티 검사 방지를 위한 타입 단언)
*/

type Person = {
  name: string;
  age: number;
};

let person: Person = {} as Person;
```

### Quiz 2.

```
/*
[ 문제 소개 ]
타입 단언을 이용해 함수 호출에서의 오류를 해결하세요
(힌트. const 단언)
*/

let value = 10 as const;
giveMe10(value);

/* [Test] 여기부터는 정답을 체크하기 위한 용도로 수정하지 않습니다 */
function giveMe10(value: 10) {
  return value;
}
```

### Quiz 3.

```
/*
[ 문제 소개 ]
다음 요구사항을 만족하는 코드를 작성하세요
- CompanyMember 타입을 Boss와 Employee의 서로소 유니온 타입으로 정의하세요
*/

/* [Quiz] 아래의 코드를 완성해 오류를 제거하세요 */
type Boss = {
  type: "Boss"
  car: string;
};

type Employee = {
  type:"Employee"
  salary: number;
};

type CompanyMember = Employee | Boss;

/* [Test] 여기부터는 정답을 체크하기 위한 용도로 수정하지 않습니다 */
function test(companyMember: CompanyMember) {
  if (companyMember.type === "Boss") {
    console.log("사장님 또 차 바꿨다 : ", companyMember.car);
  } else if (companyMember.type === "Employee") {
    console.log("아직도 월급은 : ", companyMember.salary);
  }
}

```
