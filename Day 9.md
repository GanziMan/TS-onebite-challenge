## 타입스크립트 9일차 정리

### 🏗️ 자바스크립트의 클래스

클래스란 객체를 만들어내는 틀
📦 붕어빵이 객체라면, 붕어빵 기계가 클래스

```
let studentA = {
  name: "이정환",
  grade: "A+",
  age: 27,
  study() {
    console.log("열심히 공부");
  },
  introduce() {
    console.log("헬로");
  },
};



class Student {
  name;
  grade;
  age;

  constructor(name, grade, age) {
    this.name = name;
    this.grade = grade;
    this.age = age;
  }

  study() {
    console.log(`${this.name} 공부 중...`);
  }

  introduce() {
    console.log("안녕~");
  }
}

const studentB = new Student("김범수", "B", 27);
studentB.study();      // 김범수 공부 중...
studentB.introduce();  // 안녕~


class StudentDeveloper extends Student {
    faboriteSkill;

    // 생성자 - 실제로 객체를 생성을 하는 메서드이다.
    constructor(name, garde, age, favoriteSkill){
        // 부모클래스의 생성자를 호출합니다.
        super(name, grade, age)
        this.favoriteSkill = favoriteSkill
    }

    const studentDeveloper = new StudentDeveloper("김범수", "A", 28, "Javascript")
}

```

### 타입스크립트 클래스

`타입스크립트 클래스는 자바스크립트의 클래스 + 타입 시스템을 결합한 형태입니다.`

```
const employee = {
    name:"이정환",
    age:27,
    position:"developer",
    work(){
        console.log("일함")
    }
}

class Employee {
  name: string;
  age: number;
  position: string;

  constructor(name: string, age: number, position: string) {
    this.name = name;
    this.age = age;
    this.position = position;
  }

  work() {
    console.log("열심히 일함");
  }
}


class ExecutiveOfficer extends Employee {
    // 필드
    officeNumber: number;


    // 생성자
    // 파생 클래스의 생성자는 super 호출을 포함해야 한다.
    constructor(name:string, age:number, position:string; officeNumber:number){
        super(name,age,position);
        this.offceNumber = officeNumber;
    }
}


const employeeB = new Employee("이정환",27,"개발자");


// 타입스크립트의 클래스는 타입으로도 활용할 수 있다.
const employeeC :Employee = {
    name:"",
    age:0,
    position:"",
    work(){

    }
}
```

### 접근 제어자 - aceess modifier

`특정 필드나 메서드의 접근할 수 있는 범위를 설정하는 문법`
=> public private protected

private를 사용하면

외부에서, 파생클래스에서도 this.name으로 접근 안된다.
만약 파생클래스에서라도 접근 허용하려면 protected(Public private 중간인 느낌) 접근제어자를 사용하자.

```
class User {
  public name: string;
  private password: string;
  protected email: string;

  constructor(name: string, password: string, email: string) {
    this.name = name;
    this.password = password;
    this.email = email;
  }
}

class PremiumUser extends User {
  showEmail() {
    console.log(this.email); // ✅ 가능
    // console.log(this.password); // ❌ private이므로 접근 불가
  }
}

```

| 키워드      | 설명                                      |
| ----------- | ----------------------------------------- |
| `public`    | 어디서든 접근 가능 (기본값)               |
| `private`   | 클래스 내부에서만 접근 가능               |
| `protected` | 클래스 내부 + 자식 클래스에서만 접근 가능 |

### 인터페이스와 클래스

`클래스가 인터페이스를 구현(implements) 하면,
해당 인터페이스가 요구하는 구조를 따라야 합니다.`

```
interface CharacterInterface {
    name:string;
    moveSpeed:number;
    move(): void;
}

// implements 구현한다 라는 의미
// CharacterInterface는 설계도라고 하여 캐릭터 class는 해당 설계도를 구현한다는 의미

class Character implements CharacterInterface{
    name:string;
    moveSpeed:number

    constructor(name:string, moveSpeed:number){
        this.name=name
        this.moveSpeed = moveSpeed
    }

    move(): void{
        consolel.log(`${this.moveSpeed} 속도로 이동`)
    }

    // interface로 정의하는 필드들은 무조건 public만 정의할 수 있기때문.
}
```

### Quiz 1.

```
/*
[ 문제 소개 ]
다음 요구사항을 만족하는 Pokemon 클래스를 완성하세요
- 다음 3개의 필드를 갖습니다.
  1. name 필드는 String 타입이며 Public입니다.
  2. skill 필드는 String 타입이며 Public 입니다.
  3. type 필드는 String 타입이며 ReadOnly(읽기 전용)필드 입니다.
- 다음 2개의 메서드를 갖습니다.
  1. getName 메서드는 name 필드의 값을 반환합니다.
  2. setSkill 메서드는 String 타입의 매개변수를 받아 skill 필드의 값을 업데이트 합니다.
*/

/* [Quiz] 구현 시그니쳐를 완성하세요 */
class Pokemon {
  constructor(public name: string, public skill: string, readonly type: string) {}

  getName(): string {
    return this.name;
  }

  setSkill(skill: string): void {
    this.skill = skill;
  }
}

/* [Test] 여기부터는 정답을 체크하기 위한 용도로 수정하지 않습니다 */
const pikachu = new Pokemon("피카츄", "백만볼트", "전기");
pikachu.getName();
pikachu.setSkill("천만볼트!");

```
