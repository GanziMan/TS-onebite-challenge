### 제네릭 인터페이스

```
interface KeyPair<K, V> {
    key: K;
    value: V;
}

let keyPair : KeyPair<string,number> {
    key: "key",
    value:0,
}

// 제네릭 인터페이스를 사용할 때, 타입 변수에 할당할 타입을 꺽새와 함께 반드시 사용해야합니다.

타입변수
= 타입 파라미터
= 제네릭 타입 변수
= 제네릭 타입 파라미터

let keyPair: KeyPair<boolean, string[]> = {
    key:ture,
    value: ["1"],
}

```

### 인덱스 시그니처

```
interface NumberMap {
    [key:string]: number;
}

interface Map<V>{
    [key:string]: V
}

let stringMap: Map<string> = {
    key:"value",
}

let booleanMap: Map<boolean> = {
    key: true,
}
```

### 제네릭 타입 별칭

```
type Map2<V>{
    [key:string]: V
}


let stringMap2 : Map2<string> = {
    key:"",
}
```

### 제네릭 인터페이스의 활용 예시

```
interface Student {
    type: "student";
    school:string;
}

interface Developer {
    type: "developer",
    skill: string;
}

interface User<T>{
    name:string;
    profile: T;
}

const developerUser: User<Developer> ={
    name:"이정환",
    profile: {
        type:"develper",
        skill:"TypeScriptio"
    }
}

const studentUser: User<Student>={
    name:"이정환",
    profile: {
        type:"sutdnet",
        school:"한림대"
    }
}

function goToSchool(user: User<Student>){
    if(user.profile.type !== "student"){
        console.log("잘 못 오셨습니다.")
        return;
    }
    const school = user.profile.school;
    console.log(`${school}로 등교 완료`)
}

타입 좁히기 코드를 쓸 필요가 없음
```

### 제네릭 클래스

```
class List<T> {
    consturctor(private list: T[]){}

    push(data: T){
        this.list.pushdata()
    }
    pop(){
        this.list.pop()
    }

    print(){
        console.log(this.ist)
    }
}


const numberList = new List([1,2,3]);

제네릭 클래스는 제네릭 인터페이스, 타입과 다르게 생성장에 인수로 전달하는 값을 기준으로 타입의 값을 추론한다.

```

### 프로미스

```
const promise = new Promise<number>((resolve,reject) => {
    setTimeout(() => {
        resolve(20);
    },3000)

    promise.then((response) => {
        console.log(response ); // 20
    })
})

### 프로미스를 반환하는 함수의 타입을 정의

interface Post {
    id: number;
    title: string;
    content: string;
}

function fetchPost(): Promise<Post>{
    return new Promise((resolve,reject) =>{
        setTimeout(() => {
            resolve({
                id: 1,
                title: "게시글 제목",
                content: "게시글 컨텐츠",
            })
        },3000)
    })
}

const postRequest = fetchPost();

postRequest.then((post) => {
    post.id
})
```

### Quiz 1.

```
/*
[ 문제 소개 ]
다음 요구사항을 만족하는 3개의 인터페이스를 정의하세요
1. Content Interface
  - name(String 타입), info(T 타입) 프로퍼티를 갖습니다.
2. Video Interface
  - playTime(Number 타입) 프로퍼티를 갖습니다.
3. Book Interface
  - pageNumber(Number 타입) 프로퍼티를 갖습니다.
*/

/* [Quiz] 아래의 코드를 수정하세요 */

interface Video {
  playTime:number;
}

interface Book {
  pageNumber:number;
}

interface Content<T> {
    name:string;
    info:T;
}

/* [Test] 여기부터는 정답을 체크하기 위한 용도로 수정하지 않습니다 */
function watchVideo(content: Content<Video>) {
  console.log(`${content.info.playTime}시간의 강의를 시청함`);
}

function readBook(content: Content<Book>) {
  console.log(`${content.info.pageNumber}권의 책을 읽음`);
}

watchVideo({
  name: "한 입 크기로 잘라먹는 타입스크립트",
  info: {
    playTime: 10.5,
  },
});

readBook({
  name: "한 입 크기로 잘라먹는 리액트",
  info: {
    pageNumber: 700,
  },
});
```

### Quiz 2.

```
/*
[ 문제 소개 ]
getComments 함수의 적절한 반환값 타입을 정의 하세요
- getComments 함수는 여러개의 Comment 타입 객체를 담는 배열을 비동기적으로 반환하는 함수입니다.
- 반환값 타입이 Promise<unknown>이 아닌 좀 더 정확한 타입으로 추론되게 하여도 정답으로 인정합니다.
*/

/* [Quiz] 아래의 코드를 수정하세요 */
interface Comment {
  id: number;
  author: string;
  content: string;
}

function getComments():Promise<Comment[]> {
  return new Promise((resolve) => {
    setTimeout(() => {
      resolve([
        {
          id: 1,
          author: "이정환 & 김효빈",
          content: "한입 FE 챌린지! 완강까지 화이팅!",
        },
      ]);
    }, 2000);
  });
}

/* [Test] 여기부터는 정답을 체크하기 위한 용도로 수정하지 않습니다 */
import { Equal, Expect, NotEqual } from "@type-challenges/utils";

type TestCases = [
  Expect<NotEqual<ReturnType<typeof getComments>, Promise<any>>>,
  Expect<NotEqual<ReturnType<typeof getComments>, Promise<unknown>>>,
  Expect<NotEqual<ReturnType<typeof getComments>, Promise<never>>>,
  Expect<Equal<ReturnType<typeof getComments>, Promise<Comment[]>>>
];

```
