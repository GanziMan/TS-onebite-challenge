## 타입스크립트 5일차 정리

### 🧩 객체 타입 간의 호환성

어떤 객체 타입을 다른 객체 타입으로 취급해도 괜찮은가?

타입스크립트에서는 객체의 구조(프로퍼티) 를 기준으로 타입 간의 호환성을 판단합니다.

이를 구조적 타입 시스템(Structural Typing) 이라고 합니다.

```
// 슈퍼타입
type Animal = {
  name: string;
  color: string;
};

// 서브타입
type Dog = {
  name: string;
  color: string;
  breed: string;
};

let animal: Animal = {
  name: "기린",
  color: "yellow",
};

let dog: Dog = {
  name: "돌돌이",
  color: "brown",
  breed: "진도",
};

animal = dog; // ✅ OK: Dog는 Animal의 모든 속성을 포함하므로 업캐스팅
dog = animal; // ❌ Error: breed 속성이 없기 때문에 다운캐스팅은 불가
```

### ⚖️ 유니언 타입 (Union)

여러 타입 중 하나만 만족하면 되는 타입

```
type Developer = {
  name: string;
  language: string;
};

type Designer = {
  name: string;
  tool: string;
};

type Union1 = Developer | Designer;


let union1: Union1 = {
  name: "이정환",
  language: "TypeScript",
}; // ✅ Developer 만족

let union2: Union1 = {
  name: "이정환",
  tool: "Figma",
}; // ✅ Designer 만족

let union3: Union1 = {
  name: "이정환",
  language: "TypeScript",
  tool: "Figma",
}; // ✅ 둘 다 만족해도 괜찮음

let union4: Union1 = {
  name: "이정환",
}; // ❌ Error: Developer도, Designer도 모두 만족하지 못함

```

### 🔗 교집합 타입 (Intersection)

```
let variable: number & string;

type Dog = {
    name: string;
    color: string;
}

type Person = {
    name: string;
    language: string;
}

type Intersection = Dog & Person;

let intersection1: Intersection = {
    name: "",
    color: "",
    language: ""
}

let intersection1: Intersection = {
  name: "돌돌이",
  color: "brown",
  language: "Korean",
}; // ✅ 모든 속성 충족


```

### 🤖 타입 추론 (Type Inference)

타입스크립트는 대부분의 경우 타입을 자동으로 추론합니다.

변수 선언 시

```
let a = 10; // 🔍 a: number 로 추론됨
const b = 10; // 🔍 b: 10 (리터럴 타입) 으로 추론됨
```

let은 변수이므로 일반 타입(number 등) 으로 추론

const는 재할당이 불가능하므로 리터럴 타입(고정값) 으로 추론

대부분의 경우 명시적 타입 선언 없이도 정확하게 추론되므로
불필요하게 타입을 반복하지 않아도 됨

| 개념                | 설명                                   | 특징                                             |         |
| ------------------- | -------------------------------------- | ------------------------------------------------ | ------- |
| 객체 타입 호환성    | 구조 기준으로 타입 호환 판단           | 프로퍼티가 더 많은 쪽이 더 적은 쪽으로 할당 가능 |         |
| 유니언 타입 (\`     | \`)                                    | 여러 타입 중 하나만 만족하면 됨                  | OR 조건 |
| 인터섹션 타입 (`&`) | 여러 타입 모두 만족해야 함             | AND 조건                                         |         |
| 타입 추론           | 타입을 명시하지 않아도 자동으로 추론됨 | `const`는 리터럴, `let`은 일반 타입              |         |
