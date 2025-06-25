## 타입스크립트 2일차 정리

### 타입스크립트의 파일 스코프

타입스크립트는 기본적으로 각 파일을 전역 스코프(global scope) 로 인식합니다.

```
// index.ts
const a = 1;

// hello.ts
const a = 1;

```

위와 같이 작성하면, 두 파일 모두 전역 스코프에서 a라는 동일한 이름의 변수를 선언한 것으로 간주됩니다.

이로 인해 변수 중복 오류가 발생합니다.

### 전역 → 개별 모듈로 만들기 (모듈 스코프)

타입스크립트에서 파일을 **모듈(Module)**로 인식시키려면 아래 방법을 사용합니다:

1️⃣ export/import 사용하기

- 파일 내에 `export` 또는 `import` 구문이 존재하면, 해당 파일은 자동으로 모듈 스코프로 전환됩니다.
- 각 파일의 스코프가 독립적으로 격리되므로 변수 중복 문제가 발생하지 않습니다.

2️⃣ moduleDetection 옵션 사용하기

- `tsconfig.json` 파일에 `moduleDetection` 옵션을 설정하면, 타입스크립트가 보다 적극적으로 모듈을 감지합니다.
- 예를들어

```
{
  "compilerOptions": {
    "moduleDetection": "force"
  }
}
```

이 설정을 사용하면 명시적인 `export` 없어도 모듈 스코프로 인식시킬 수 있습니다. (단, 프로젝트 설정에 따라 권장여부는 달라지겠죠?)

### 원시 타입 (Primitive Types)

| 타입        | 설명                      | 예시                                   |
| ----------- | ------------------------- | -------------------------------------- |
| `number`    | 숫자                      | `let age: number = 30;`                |
| `string`    | 문자열                    | `let name: string = "John";`           |
| `boolean`   | 참/거짓                   | `let isAdmin: boolean = true;`         |
| `null`      | 값 없음                   | `let data: null = null;`               |
| `undefined` | 정의되지 않음             | `let value: undefined = undefined;`    |
| `symbol`    | 고유하고 변경 불가능한 값 | `let sym: symbol = Symbol("key");`     |
| `bigint`    | 매우 큰 정수              | `let big: bigint = 9007199254740991n;` |

### 리터럴(Literal)

리터럴(Literal) 이란? "값 그 자체" 를 의미합니다.

```
let a: 1; // a 변수는 오직 1이라는 값만 가질 수 있음
let b: "hello"; // b 변수는 오직 "hello"라는 문자열만 가질 수 있음
```

- 리터럴 타입은 특정 값만을 허용하는 타입입니다.
- 주로 유니언 타입, 상수 값 제한, 태그드 유니언 등에 활용됩니다.

## Day 2 Quiz

### Quiz 1.

정적 타입시스템과 동적 타입 시스템의 단점들은 보완하고 장점들은 그대로 가져온 TypeScript의 타입 시스템을 뭐라고 부를까요? (with 영문버전)

### Answer: 점진적 타입 시스템 - (Gradual Type System)

### Quiiz 2.

TypeScript의 타임 시스템이 갖는 특징에는 어떤 점이 있을까요?

### Answer: 컴파일 전 타입 검사, 타입이 명시되어 있지 않아도 타입을 자동으로 추론합니다.

### Quiz 3.

TypeScript를 컴파일 하면 그 결과로 무엇이 생성되나요?
(힌트! TypeScript는 다른 언어들과는 달리 컴파일 결과 ByteCode가 생성되지 않아요)

### Answer: Javascript 코드

### Quiz 4.

타입스크립트는 사용자가 직접 컴파일로 옵션을 설정할 수 있는 자유로움을 제공해요.
따라서 우리가 tsconfig.json 파일의 내용을 수정하면서 이럱 저런 옵션을 설정할 수 있는데요.
이 파일에서 타입 검사를 매우 엄격하게 하려면 어떤 옵션을 어떤 값으로 설정해야 할까요?

### Answer: "strict": true

### Quiz 5.

아래 코드처럼 number 타입의 변수에 null 값을 할당하려고 해요.

```
let numA: number = null; // ❌ 오류 발생!
```

이 코드는 엄격한 타입 검사가 활성화 되어 있을 때에는 오류로 판단되는데요
만약 위 코드를 오류가 아닌 것으로 판단하게 하려면 어떤 옵션을 어떤 값으로 설정해야 할까요?

### Answer: "strictNullChecks": false
