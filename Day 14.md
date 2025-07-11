## 타입스크립트 14일차 정리: 유틸리티 타입 총정리

### 🧰 유틸리티 타입이란?

`기존 타입을 변형하거나 조합해 새로운 타입을 만들어내는 도구`

✅ 맵드 타입 기반

- Partial `<T>`
- Required `<T>`
- Readonly `<T>`
- Pick `<T, K>`
- Omit `<T, K>`
- Record `<K, V>`

✅ 조건부 타입 기반

- Exclude `<T, U>`
- Extract `<T, U>`
- ReturnType `<T>`

### 🔹 Partial `<T> `

부분적인, 일부분의 라는 의미를 가지고 있습니다.

`특정 객체 타입의 모든 프로퍼티를 선택적 프롭퍼티 바꿔주는 타입`

```
interface Post {
  title: string;
  content: string;
  tags: string[];
  thumbnailUrl: string;
}

const draft: Partial<Post> = {
  title: "임시 제목",
  content: "초안입니다.",
};

type Partial<T> = {
    // 맵드 타입
    [key in keyof T]?: T[key] // 인덱스드 엑세스타입
}
```

### 🔹 Required `<T>`

"필수의", "필수적인" 이라는 의미를 가지고 있습니다.

`특정 객체 타입의 모든 프로퍼티를 필수 프로퍼티로 바꿔주는 타입`

```
// Required 직접 구현
type Required<T> = {
  [K in keyof T]-?: T[K];
};

const completePost: Required<Post> = {
  title: "완성된 글",
  content: "내용",
  tags: ["ts"],
  thumbnailUrl: "https://...",
};
```

### Readonly `<T>` - 읽기 전용 수정불가

`특정 객체 타입에서 모든 프로퍼티를 읽기 전용 프로퍼티로 만들어주는 타입`

```
// 직접 구현
type Readonly<T> = {
  readonly [K in keyof T]: T[K];
};

const readonlyPost: Readonly<Post> = {
  title: "읽기 전용 글",
  content: "수정 불가",
  tags: [],
  thumbnailUrl: "",
};
```

### Pick `<T, K>` -> 뽑다, 고르다

`객체 타입으로부터 특정 프로퍼티만 딱 골라내는 그런 타입`

```
interface Post {
    title:String;
    tags: string[];
    content: string;
    thumbnailUrl?: string;
}

// 직접 구현
type Pick<T,K extends keyof T> = {
    [key in K] : T[key]
}

const legacyPost: Pick<Post,"title"|"content"> = {
    title:"엣날 글",
    content: "옛날 컨텐츠",
}
```

K에는 제약을 걸어놓아야 한다. 그렇지 안흥면 함수타입 number 타입 등 여러 타입들이 들어올 수 있게 됩니다.

K에는 반드시 T의 key만 올 수 있도록 extends keyof T로 제약을 걸어야 합니다.

K는 T값에서 추출한 유니온 타입인 서브타입만 들어올 수 있게 때문이다.

풀어서 설명해보자.
T에 Post에 들어가게 된다면

- K extends 'title' | 'tags' | 'content' | 'thumbnailURL'
- 'title' | 'content' extends 'title' | 'tags' | 'content' | 'thumbnailURL'

(extends 기준으로) 좌측은 우측의 서브타입이기 때문에 가능하다. 그리고 K에는 T 객체 프로퍼티의 키만 전달할 수 있도록 해야한다.

### Omit `<T,K>` -> 생략하다, 빼다

`객체 타입으로부터 특정 프로퍼티를 제거하는 타입`

```
// 직접 구현하기
type Omit<T, K exnteds typeof T> = Pick<T, Exclude<keyof T, K>>;
// 풀어서 적어보기
Pick<Post, Exclude<keyof Post, 'title'>>
Pick<Post, Exclude<keyof 'title' | 'tags' | 'content' | 'thumbnailURL', 'title'>>
Pick<Post, 'content' | 'tags' | 'thumbnailURL'>>

const noTitlePost: Omit<Post, "title"> = {
  content: "제목 없음",
  tags: [],
  thumbnailUrl: "",
};
```

### Record `<K, V>`

`K를 key로, V를 value로 갖는 객체 타입 생성`

```
type ThumbnailLegacy = {
    large: {
        url: string;
    };
    medium: {
        url: string;
    };
    small: {
        url: string;
    }
    watch: {
        url: string
    }
}

type Thumbnail = Record<"large"| "mediumm" | "small" | "watch", { url:string }>;
```

```
// 직접 구현
type Thumbnail = Record<K extends keyof any, V> = {
    [key in K]: V;
}
```

### Exclude `<T, U>` -> 제외하다, 추방하다

`T에서 U를 제거하는 타입`

```
type A = Exclude<string | boolean, boolean> => string

// 직접 구현
type Exclude<T,U> =  T extends U ? never : T;
```

### Extract `<T, U>`

`T에서 U를 추출합는 타입`

```
// 직접 구현
type Extract<T, U> = T extends U ? T : never
```

### ReturnType

`함수의 반환값 타입을 추출하는 타입`

```
function funcA(){
    return "hello";
}

function funcB(){
    return 10;
}

type ReturnA = ReturnType<typeof funcA> // string
type ReturnB = ReturnType<typeof funcB> // number

// 직접 구현

type ReturnType<T extends (...args: any) => any> = T extends (... args : any) => infer R ? R : never;
```
