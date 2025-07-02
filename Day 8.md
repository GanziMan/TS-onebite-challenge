### 🔁 함수 오버로딩 (Function Overloading)

`하나의 함수를 매개변수의 개수나 타입에 따라 여러 버전으로 정의하는 방식`
`하나의 함수 func 모든 매개변수의 타입 number`

- ver1. 매개변수가 1개 -> 이 매개변수에 20을 곱한 값 출력
- ver2. 매개변수가 3개 -> 이 매개변수들을 다 더한 값을 출력

// 오버로드 시그니처
function func(a: number): void;
function func(a: number, b: number, c: number): void;

```
// 실제 구현부 -> 구현 시그니처

function func(...args: number[]): void {
  if (args.length === 1) {
    console.log(args[0] * 20);
  } else if (args.length === 3) {
    console.log(args[0] + args[1] + args[2]);
  }
}

func(1);         // ✅ 20 출력
func(1, 2, 3);   // ✅ 6 출력
func();          // ❌ 매개변수 없음
func(1, 2);      // ❌ 시그니처 없음

⚠ 오버로드를 사용할 땐 정의한 오버로드 시그니처 중 하나와 반드시 일치해야 합니다. 실제 구현 시그니처는 가장 마지막에 하나만 존재합니다.
오버로드 시그니처 func를 정의해줌으로써 앞으로 func는 이렇게 정의하겠다 라는 식으로 정의한 것

만일, function func(a: number, b:number, c:number){} 이렇게 하면
function func(a: number): void; 가 의미가 없어진다.

```

### 🔐 사용자 정의 타입 가드 (Custom Type Guard)

```
type Dog = {
  name: string;
  isBark: boolean;
};

type Cat = {
  name: string;
  isScratch: boolean;
};

type Animal = Dog | Cat;

// 사용자 정의 타입 가드
function isDog(animal: Animal): animal is Dog {
  return (animal as Dog).isBark !== undefined;
}

// 반환값이 true이면 매개변수로 받은 또는 전달한 animal이 Dog 타입이구나 라고 명시한다.

function warning(animal: Animal) {
  if ("isBark" in animal) {
    // 🐶 강아지
  } else if ("isScratch" in animal) {
    // 🐱 고양이
  }
}

```

`animal is Dog는 타입스크립트에게 조건이 true일 경우 animal이 Dog 타입임을 단언하는 역할을 합니다.`

### 🧾 인터페이스 (Interface)

`타입에 이름을 지어주는 또 다른 문법`

- 객체의 구조를 정의하는데 특화된 문법

```
interface Person {
  name: string;
  age: number;
}

const person: Person = {
  name: "이정환",
  age: 27,
};


❓ 인터페이스에서 함수 오버로딩은 어떻게?

함수 타입 표현식 ❌ (X)
// 이렇게 쓰면 오버로드 인식 안됨
interface Person {
  sayHi: () => void;
  sayHi: (a: number, b: number) => void;
}

✔ 호출 시그니처 방식 (O)
interface Person {
  sayHi(): void;
  sayHi(a: number, b: number): void;
}


interface Person {
    name: string;
    age: number;
    sayHi:() => void;
    sayHi:(a: number, b: number) => void;
}

person.sayHi();
person.sayHi(1,2);

sayHi:() => void;
sayHi:(a: number, b: number) => void;
이렇게 함수타입 표현식을 쓰면 오버로드 시그니처를 알지못함


메서드의 오버로딩을 사용하고 싶다면 다음과 같이 호출 시그니처를 써야함
sayHi:(): void;
sayHi:(a: number, b: number): void;
```

| 항목                 | `type` | `interface` |
| -------------------- | ------ | ----------- |
| 유니온/인터섹션 가능 | ✅     | ❌          |
| 선언 병합            | ❌     | ✅          |
| 확장 가능성          | 제한적 | 유연함      |
| 객체 구조 정의       | 가능   | 특화됨      |

### 🔗 인터페이스 확장 (Extends)

```
interface Animal {
  name: string;
  age: number;
}

interface Dog extends Animal {
  isBark: boolean;
}

interface Cat extends Animal {
  isScratch: boolean;
}

interface Chicken extends Animal {
  isFly: boolean;
}

```

### 🌐 다중 확장

```
interface DogCat extends Dog, Cat {}

const hybrid: DogCat = {
  name: "하이브리드",
  age: 3,
  isBark: true,
  isScratch: false,
};

```

### 📚 인터페이스 선언 병합 (Declaration Merging)

`동일한 이름의 인터페이스를 여러 번 선언하면 자동으로 병합`

```
interface Person {
  name: string;
}

interface Person {
  age: number;
}

const person: Person = {
  name: "이정환",
  age: 27,
};

```

### 🔧 모듈 보강 (Module Augmentation)

이미 존재하는 인터페이스를 추가적으로 확장할 때 사용

```
interface Lib {
  a: number;
  b: number;
}

interface Lib {
  c: string;
}

const lib: Lib = {
  a: 1,
  b: 2,
  c: "추가됨",
};

```

### 🔤 인터페이스 작명 관습

```
interface IPerson {
  name: string;
}

```

type Type1 = number | string;
타입과 다르게 interface는 유니온이나 intersection을 만들 수 없다.
그래서, 사용할 거면 타입에다가 별칭을 사용하거나 그런식으로 사용을 해야한다.

interface의 이름을 정의할 때 앞에다가 'I'Person 과 같은 I를 쓰는 관습들이 있다. 하지만 논란이 있습니다. 헝가리안 표기법이라고 하는데 js 프로그래밍기법을 잘 안쓴다. '\_' 이거나 파스칼, 카멜 스네이크를 쓰는데 헝가리안까지 써야하냐??
라는 논란이 있다.

### Quiz 1.

```
/*
[ 문제 소개 ]
다음 요구사항을 만족하는 구현 시그니쳐를 완성하세요(매개변수 타입만 정의하면 됩니다)
- 이미 작성되어 있는 3개의 오버로드 시그니쳐를 모두 만족해야 합니다.
- a, b, c의 값을 모두 더한 값을 반환합니다.
- 만약 매개변수의 값이 undefined일 경우 모두 0으로 취급합니다.
  - a가 없을 경우 : 0
  - b가 없을 경우 : 0
  - c가 없을 경우 : 0
*/

function add(): number;
function add(a: number): number;
function add(a: number, b: number, c: number): number;

/* [Quiz] 구현 시그니쳐를 완성하세요 */
function add(a?:number, b?:number, c?:number) {
  return (a ?? 0) + (b ?? 0) + (c ?? 0);
}

```

### Quiz 2.

```
/*
[ 문제 소개 ]
다음 요구사항을 만족하도록 사용자 정의 타입 가드를 완성하세요
- isGuest 함수가 true를 반환하면 매개변수로 전달된 user는 Guest 타입임이 보장됩니다.
- isMember 함수가 true를 반환하면 매개변수로 전달된 user는 Member 타입임이 보장됩니다.
*/

/* [Quiz] 사용자 정의 타입가드를 완성하세요(타입 수정 X) */
type Guest = {
  visitCount: number;
};

type Member = {
  id: string;
};

type User = Guest | Member;

function isGuest(user: User): user is Guest {
  return (user as Guest).visitCount !== undefined
}

function isMember(user: User): user is Member {
  return (user as Member).id !== undefined
}

/* [Test] 여기부터는 정답을 체크하기 위한 용도로 수정하지 않습니다 */

function inviteUser(user: User) {
  if (isGuest(user)) {
    console.log(`${user.visitCount}번째 방문입니다`);
  } else if (isMember(user)) {
    console.log(`${user.id}님 안녕하세요!`);
  }
}

```

### Quiz 3.

```
/*
[ 문제 소개 ]
다음 조건을 만족하는 3가지 인터페이스를 추가로 구현하세요
- 모든 인터페이스는 Person 인터페이스를 확장합니다(extends).
  1. Student 인터페이스는 grade 프로퍼티를 갖습니다.
    - grade 프로퍼티에는 "A", "B", "C"만 저장할 수 있습니다.
  2. Developer 인터페이스는 skills 프로퍼티를 갖습니다.
    - skills 프로퍼티에는 문자열 배열을 저장할 수 있습니다.
  3. Boss 인터페이스는 company 프로퍼티를 갖습니다.
    - company 프로퍼티에는 문자열을 저장할 수 있습니다.
*/

/* [Quiz] 사용자 정의 타입가드를 완성하세요(타입 수정 X) */
interface Person {
  name: string;
}

interface Student extends Person {
  grade:"A"|"B"|"C"
}

interface Developer extends Person {
  skills:string[]
}
interface Boss extends Person {
  company:string
}

/* [Test] 여기부터는 정답을 체크하기 위한 용도로 수정하지 않습니다 */
import { Equal, Expect, NotAny } from "@type-challenges/utils";

const student: Student = {
  name: "이정환",
  grade: "A",
};
const developer: Developer = {
  name: "이정환",
  skills: ["React", "TypeScript"],
};
const boss: Boss = {
  name: "이정환",
  company: "한입 스튜디오",
};

type TestCases = [
  Expect<Equal<Student["grade"], "A" | "B" | "C">>,
  Expect<Equal<Developer["skills"], string[]>>,
  Expect<Equal<Boss["company"], string>>
];

```
