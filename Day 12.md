## 타입스크립트 12일차 정리: 타입 조작하기

### 📌 인덱스드 엑세스 타입 (Indexed Access Type)

`객체, 배열, 튜플 타입에서 특정 속성의 타입을 추출하는 문법`

```
interface Post {
  title: string;
  content: string;
  author: {
    id: number;
    name: string;
    age: number;
  };
}

// 특정 속성의 타입을 추출
function printAuthorInfo(author: Post["author"]) {
  console.log(author.name);
}
```

- Post["author"]는 author의 타입을 추출함
- Post["author"]["id"]를 쓰면 id의 타입(number) 추출 가능

```

// 'author'는 변수, 값이 들어갈 수 없고 타입만 명시할 수 있다. ex) const key =  "author" X 안됨

const post: Post = {
    title:"게시글 제목",
    content: "게시글 본문",
    author: {
        id:1,
        name: "이정환"
        age:27
    }
}

id값 뽑아 오는 방법
Post["author"]["id"]


type PostList {
    title:string;
    content:string;
    author: {
        id: number;
        name: string;
        ag: number
    }
}[];

const post:PostList[number] or PostList[0] = {

}
// 배열 타입으로부터 하나의 요소만 가져온다는 것.


type Tup = [number, string, boolean];

type First = Tup[0]; // number
type Second = Tup[1]; // string
type Union = Tup[number]; // number | string | boolean

type Tup3 = Tup[3]  // X
```

### 🔑 keyof 연산자

`객체 타입의 key들을 유니온 타입으로 반환하는 연산자`

```
interface Person {
  name: string;
  age: number;
}

type PersonKeys = keyof Person; // "name" | "age"

function getProperty(person: Person, key: keyof Person) {
  return person[key];
}

const person: Person = {
    name:"이정환",
    age: 27
}
```

- 참고: keyof typeof person 로도 사용 가능

### 🔁 맵드 타입 (Mapped Type)

`기존 타입을 바탕으로 속성 하나하나를 반복하며 변형`

```
interface User {
  id: number;
  name: string;
  age: number;
}

type BooleanUser = {
  [key in keyof User]: boolean;
};

// 예제: 모든 속성을 readonly로 만들기
type ReadonlyUser = {
  readonly [key in keyof User]: User[key];
};

// 예제: 선택적 속성으로 만들기 (Partial)
type PartialUser = {
  [key in keyof User]?: User[key];
};
- 내장 제네릭 Partial<T> 와 동일합니다.

function updateUser(user: Partial<User>) {
  // id, name, age 중 일부만 받아도 OK
}

```

### 🧩 템플릿 리터럴 타입

문자열을 조합해서 새로운 문자열 타입을 생성할 수 있는 기능

```
type Color = "red" | "black" | "green";
type Animal = "dog" | "cat" | "chicken";

type ColoredAnimal = `${Color}-${Animal}`;
// "red-dog" | "red-cat" | "red-chicken" | "black-dog" | ...

문자열로 여러 상황들을 표현할 떄 사용된다.
```

- 실무에서는 이벤트 명("click-user"), 상태 키("is-loading"), API 키 조합 등에 많이 사용됩니다.
- interface에서는 템플릿 리터럴 타입을 사용할 수 없다

| 문법              | 설명                           |
| ----------------- | ------------------------------ |
| `T[K]`            | 특정 속성의 타입 추출          |
| `keyof T`         | 객체 타입의 key만 추출         |
| `[K in keyof T]`  | 속성 반복 → 변형 (Mapped Type) |
| `` `${A}-${B}` `` | 문자열 조합으로 타입 생성      |

### Quiz 1.

```
/*
[ 문제 소개 ]
다음 요구사항을 만족하도록 getSellersFromProducts 함수의 매개변수와 반환값 타입을 정의하세요
- getSellersFromProducts 함수는 매개변수로 받은 Product 배열로부터, seller 객체만 추출해 새로운 배열로 반환하는 함수입니다.
- 반환값을 명시적으로 설정해야 합니다 (인덱스드 엑세스 타입)
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

function getSellersFromProducts(products: Product[]):Product["seller"][] {
  return products.map((product) => product.seller);
}

/* [Test] 여기부터는 정답을 체크하기 위한 용도로 수정하지 않습니다 */
import { Equal, Expect } from "@type-challenges/utils";

type TestCases = [
  Expect<Equal<Parameters<typeof getSellersFromProducts>[0], Product[]>>,
  Expect<Equal<ReturnType<typeof getSellersFromProducts>, Product["seller"][]>>
];
```

### Quiz 2.

```
/*
[ 문제 소개 ]
다음 조건을 만족하는 3개의 타입을 추가로 정의하세요
- PartialProduct 타입은 Product 타입의 모든 프로퍼티를 다 선택적 프로퍼티로 바꾼 타입입니다.
- ReadonlyProduct 타입은 Product 타입의 모든 프로퍼티를 다 읽기 전용 프로퍼티로 바꾼 타입입니다.
- ReadonlyPartialProduct 타입은 Product 타입의 모든 프로퍼티를 다 선택적, 읽기 전용 프로퍼티로 바꾼 타입입니다.
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

type PartialProduct = {
  [key in keyof Product] ?: Product[key]
};

type ReadonlyProduct = {
 readonly [key in keyof Product] : Product[key]
}

type ReadonlyPartialProduct = {
  readonly [key in keyof Product] ?: Product[key]
};

/* [Test] 여기부터는 정답을 체크하기 위한 용도로 수정하지 않습니다 */
import { Equal, Expect, NotEqual } from "@type-challenges/utils";

type TestCases = [
  Expect<Equal<PartialProduct, Partial<Product>>>,
  Expect<Equal<ReadonlyProduct, Readonly<Product>>>,
  Expect<Equal<ReadonlyPartialProduct, Readonly<Partial<Product>>>>
];

```

### Quiz 3.

```
/*
[ 문제 소개 ]
다음 조건을 만족하는 Score 타입을 구현하세요
- Score 타입은 점수를 나타내기 위한 문자열 타입으로 '${0부터 10까지의 정수}-${0부터 10까지의 정수}'형식을 갖습니다'
  - ex) "1-0", "3-2", "0-4" 이런 형태의 문자열 타입입니다.
  - ex) 어느쪽의 점수에도 두 자리수는 올 수 없습니다.
*/

/* [Quiz] 아래의 코드를 수정하세요 */
type N = 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9;
type Score = `${N}-${N}`;

/* [Test] 여기부터는 정답을 체크하기 위한 용도로 수정하지 않습니다 */
import { Equal, Expect, ExpectExtends, ExpectFalse } from "@type-challenges/utils";

const tc1 = "19-0";
const tc2 = "5-11";
const tc3 = "9-1";
const tc4 = "2-8";
const tc5 = "7-2";

type TestCases = [
  ExpectFalse<ExpectExtends<Score, typeof tc1>>,
  ExpectFalse<ExpectExtends<Score, typeof tc2>>,
  Expect<ExpectExtends<Score, typeof tc3>>,
  Expect<ExpectExtends<Score, typeof tc4>>,
  Expect<ExpectExtends<Score, typeof tc5>>
];

```
