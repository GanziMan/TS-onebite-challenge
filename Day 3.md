## 객체 리터럴 타입과 다양한 객체 타입 패턴들

### 객체 리터럴타입

```
let user: {
    id: number;
    name: string;
} = {
    id: 1,
    name: "이정환",
}
```

- 타입스크립트는 구조를 기준으로 타입을 판단합니다.
- 이처럼 구조를 기반으로 하는 타입 시스템을 구조적 타입 시스템(Structural Type System) 이라고 부릅니다.
- 속성(Property)을 중심으로 타입을 정의하기 때문에 Property-Based Type System 이라고도 불립니다.

참고:
C언어나 Java는 타입의 이름(name)을 기준으로 판단하는
명목적 타입 시스템(Nominal Type System)을 사용합니다.

### 🔒 readonly 키워드

절대 수정되어서는 안 되는 값에는 readonly를 붙여서 불변성을 보장할 수 있습니다.

```
let config: {
  readonly apiKey: string;
} = {
  apiKey: "MY_API_KEY",
};

config.apiKey = "HACKED"; // ❌ 오류 발생: 읽기 전용 속성입니다.

```

### 🏷️ 타입 별칭 (Type Alias)

```
type User = {
  id: number;
  name: string;
};

let user: User = {
  id: 1,
  name: "이정환",
};

```

- 타입 별칭은 컴파일 이후 JavaScript 코드에는 존재하지 않음
- 전적으로 개발 단계에서만 사용되는 문법

### 🔑 인덱스 시그니처 (Index Signature)

- key와 value의 패턴을 정의하여 객체의 타입을 설정할 수 있는 문법

```
type CountryCodes = {
  Korea: string;
  UnitedStates: string;
};

let countryCodes = {
  Korea: "ko",
  UnitedStates: "us",
};

```

하지만 나라가 수십 개라면? 일일이 작성하기 어렵습니다.

```
type CountryCodes = {
  [key: string]: string;
};

let codes: CountryCodes = {
  Korea: "ko",
  UnitedStates: "us",
  France: "fr",
};

```

### 유의사항

- 인덱스 시그니처를 사용하면, 명시적으로 지정한 속성들도 인덱스 시그니처의 value 타입과 일치하거나 호환해야 합니다.

```
type InvalidCodes = {
  [key: string]: number;
  Korea: string; // ❌ 오류! string은 number와 호환되지 않음
};
```

### 📚 enum 타입

- 여러 값을 열거해 이름을 붙여주는 방식

```
enum Direction {
  Up,
  Down,
  Left,
  Right,
}

let d: Direction = Direction.Up;

```

- 기본적으로 숫자형 enum이며, 0부터 자동으로 값이 부여됩니다:

```
console.log(Direction.Up);    // 0
console.log(Direction.Down);  // 1
```

- 명시적으로 시작 숫자를 지정할 수도 있습니다:

```
enum Status {
  Success = 200,
  NotFound = 404,
  ServerError = 500,
}
```

### 마무리

- 객체 타입은 타입스크립트의 강력한 기능 중 하나이며, 구조 기반으로 동작합니다.
- readonly, 타입 별칭, 인덱스 시그니처 등은 실무에서 자주 사용됩니다.
- enum은 열거형 상수를 정의할 때 깔끔하고 직관적입니다.

## Day 3 Quiz

### Quiz 1.

다음 요구사항을 만족하는 4개의 타입을 정의하세요

Any 타입은 사용할 수 없습니다.

Nums 타입은 숫자만 담을 수 있는 배열 타입입니다.

Colors 타입은 문자열만 담을 수 있는 배열 타입입니다.

Coords 타입은 [숫자, 숫자] 형태의 배열만 허용하는 타입입니다.

Info 타입은 [숫자, 문자열] 형태의 배열만 허용하는 타입입니다.

```
type Nums = never;
type Colors = never;
type Coords = never;
type Info = never;
// 위 4개의 타입을 각각 어떻게 구현해야 할까요?
```

### Answer

```
type Nums = number[];
type Colors = string[];
type Coords = [number,number];
type Info = [string,number];
```

### Quiz 2.

다음 요구사항을 만족하는 Course 타입을 정의하세요
(이 타입은 마치 인프런에서 활용할 것 같군요?)

- Any 타입은 사용할 수 없습니다.
- Course 타입은 온라인 강의 정보를 포함하는 객체 타입을 정의합니다.
- 문자열을 저장하는 name 프로퍼티를 가져야 합니다.
- 숫자를 저장하는 price 프로퍼티를 가져야 합니다.
- 숫자를 저장하는 student_cnt 프로퍼티를 가져야 합니다.
- 문자열을 저장하는 author 프로퍼티를 가져야 합니다.
- 문자열 배열을 저장하는 related_courses 프로퍼티를 가져야 합니다.

```
type Course = never;
// Course 타입을 어떻게 구현해야 할까요?
```

### Answer

```
type Course = {
  name:string;
  price:number;
  student_cnt:number;
  author: string;
  related_courses:string[]
};
```

### Quiz 3.

다음 요구사항을 만족하는 User 타입을 구현하세요

- Any 타입은 사용할 수 없습니다.
- 객체 타입이어야 합니다.
- String 타입의 name 프로퍼티가 있어야 합니다.
- String 타입의 email 프로퍼티가 있어야 합니다.
- 그 외의 String 타입의 동적 프로퍼티들도 추가할 수 있어야 합니다.

```
type User = never;
// User 타입을 어떻게 구현해야 할까요?
```

### Answer

```
type User = {
  name: string;
  email: string;
  [key: string]: string;
};
```
