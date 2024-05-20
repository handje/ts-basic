# 코딩앙마-타입강좌

## 타입스크립트

- 브라우저는 타입스크립트를 이해하지 못함 => js로 변환 필요
- 자바스크립트는 동적언어기 때문에 정적언어인 타입스크립트로 안정적으로 개발

## 기본타입

- 대부분 타입 추론(types by inference)을 통해 타입을 생성
- 변수 선언 뒤에 :TypeName

```ts
//number, boolean, array
let age: number = 30;
let isAdult: boolean = true;

let a: number[] = [1, 2, 3];
let b: Array<number> = [1, 2, 3];

let c: string[] = ["mon", "tue"];
let d: Array<string> = ["mon", "tue"];

//tuple : 원소의 수와 각 원소의 타입이 정확히 지정된 배열의 타입을 정의 ?
let tp: [string, number];
tp = ["x", 1];
//tp=[1,"x"]; error
tp[0].toLowerCase();
//tp[1]].toLowerCase(); error

//void:함수에서 아무것도 반환하지 않을 때 주로 사용
function f(): void {
  console.log("hi");
}

//never: 항상 에러를 반환하거나, 무한루프일때 ..?
function f2(): never {
  throw new Error();
}
function f3(): never {
  while (true) {}
}

//enum : 양방향 매핑 (숫자와 문자 서로 접근 가능)
enum Os {
  Windows, //0
  Ios, //1
  Android, //2
}
let myOs: Os;

enum Os2 {
  Windows = 3, //3
  Ios = 10, //10
  Android, //11
}

//null, undefined
```

## 인터페이스

- 프로퍼티를 정의해서 객체를 표현할 때 사용

```ts
//1.object
type Score = "A" | "B" | "C" | "D"; //주어진 값 외에는 할당x

interface User {
  name: string;
  age: number;
  address?: string; //옵셔널
  readonly year: number;
  [grade: number]: Score; //여러개 프로퍼티 가능
}

let user: User = {
  name: "jieun",
  age: 20,
  year: 1999,
  1: "A",
  2: "B",
  // 3:'c' error
};

//user.gender="male" error
//user.year=1999; error

//2.function : 재사용할때 유용,간단한 것은 바로 타입 지정이 간편
interface Add {
  (num1: number, num2: number): number;
  //(parameter) : return
}

const add: Add = function (x, y) {
  return x + y;
};
//error
// add(1,2,3)
// add(1,"2")

//3.class : implements 사용
interface Car {
  color: string;
  wheels: number;
  start(): void;
}

class Bmw implements Car {
  color;
  wheels = 4;
  constructor(c: string) {
    this.color = c;
  }
  start() {
    console.log("go");
  }
}

const cl = new Bmw("black");

//4. extends : interface 확장
interface Car {
  color: string;
  wheels: number;
  start(): void;
}

interface Toy {
  name: string;
}

interface ToyCar extends Car, Toy {
  price: number;
}

const exInterface: ToyCar = {
  //확장한 속성까지 모두 선언해야함
  color: "yellow",
  wheels: 4,
  name: "toy",
  price: 100,
  start() {},
};
```

## 함수

- 매개변수와 리턴타입 지정가능(없을경우 자동으로 타입 추론)
- 옵셔널 가능 : 필수 매개변수 뒤에서만 가능

```ts
function hello(name?: string) {
  return `hello ${name || "user"}`;
}

hello();
hello("jieun");
//hello(123) error, undefined or string만 가능

//레스트 파라미터(...)는 모든 인자를 하나의 배열로 전달
function add(...nums: number[]) {
  return nums.reduce((res, num) => res + num, 0);
}
add(1, 2, 3);

//this 타입지정
interface User {
  name: string;
}

const Sam: User = { name: "Sam" };

function showName(this: User, gender: "m" | "f") {
  console.log(this.name, gender);
}

const a = showName.bind(Sam);
a("m");

//조건에 따라 return타입이 달라질 때 -> 함수 오버로드
interface User {
  name: string;
  age: number;
}

function join(name: string, age: string): string;
function join(name: string, age: number): User;
function join(name: string, age: number | string): User | string {
  if (typeof age === "number") {
    return { name, age };
  } else {
    return "숫자입력";
  }
}
```

## 리터럴, 유니온, 교차

```ts
//literal types (정해진 값만 가질 수 있는 것)

const str1 = "bob"; //문자열 리터럴(정해진 string값)
let str2 = "Tom"; //변할수있기때문에 string타입

//유니온 타입 (Union Types) : | (or)

interface Car {
  name: "car"; //구분하기 위한 것
  color: string;
  start(): void;
}
interface Mobile {
  name: "mobile";
  color: string;
  call(): void;
}

function getGift(gift: Car | Mobile) {
  console.log(gift.color); //둘다 가지고 있기 때문에 가능
  if (gift.name === "car") {
    //interface 구분 => 식별가능한 union타입
    gift.start();
  } else {
    gift.call();
  }
}

//교차 타입(intersection types) : &
interface Car2 {
  name: string;
  start(): void;
}
interface Toy {
  name: string;
  color: string;
  price: number;
}
const toyCar: Toy & Car2 = {
  //모든속성 할당
  name: "toycar",
  start() {},
  color: "green",
  price: 100,
};
```

## 클래스

- 멤버 변수 미리 선언 or 매개변수앞에 public or readonly
  - readonly는 변경 불가능(constructor에서 처리하면 가능)
- static 정적변수는 클래스.변수로 바로 접근

```ts
//1.멤버 변수 선언
class Car{
    color:string;
    constructor(color:string){
        this.color=color;
    }
    start(){}
}
//2. public
class Car{
    constructor(public(readonly) color:string){
        this.color=color;
    }
    start(){}
}
const bmw=new Car("red");
```

- 타입스크립트는 접근제한자(Access modifier) o
  - public (default) : 자식, 인스턴스 모두 o
  - private (#을 붙이면 같은 의미) : 클래스 내부에서만
  - protected : 자식o, 클래스 인스턴스로는 접근 x

```ts
class Car {
  private name: string = "car";
  protected name2: string = "bmw";
  color: string;
  constructor(color: string) {
    this.color = color;
  }
  start() {}
}

class Bmw extends Car {
  constructor(color: string) {
    super(color); //super를 호출하지 않으면 error
  }
  showName() {
    //console.log(this.name); private접근 불가능
    console.log(this.name2); //protected 접근 가능
  }
}
const bmw = new Bmw("red");

//bmw.name2 protected 접근 불가능
```

- 추상 클래스
  - new로 인스턴스 생성 불가능 (상속만 가능)
  - 하위 클래스에서 구현 필수

```ts
abstract class Car {
  color: string;
  constructor(color: string) {
    this.color = color;
  }
  start() {}
  abstract doSomething(): void;
}
// const car=new Car("red"); //error
class Bmw extends Car {
  constructor(color: string) {
    super(color);
  }
  doSomething() {} //필수
}
const bmw = new Bmw("red");
```

## Generic(제네릭)

- 클래스,함수 등을 다양한 타입으로 재사용 가능
- 생성할때 타입을 지정
- 동일한 내용에 타입이 다를 경우 기존에는 함수오버로딩 or 유니온타입 사용

```ts
function getSize<T>(arr: T[]): number {
  return arr.length;
}
//사용하는 곳에서 타입 지정
getSize<number>([1, 2, 3]);
getSize<string>(["1", "2"]);

interface Mobile<T> {
  name: string;
  option: T;
}

const m1: Mobile<{ color: string; coupon: false }> = {
  //<object>도 가능
  name: "m1",
  option: {
    color: "y",
    coupon: false,
  },
};

const m2: Mobile<string> = {
  name: "m2",
  option: "good",
};

//제네릭 확장
interface User {
  name: string;
}
interface Car {
  name: number;
}
interface Book {
  price: number;
}

const user: User = { name: "a" };
const car: Car = { name: 123 };
const book: Book = { price: 100 };

function showName<T extends { name: string }>(data: T): string {
  //모든 interface가 name을 가진것을 확인할 수 없을때 타입 지정
  return data.name;
}

showName(user);
//showName(car) type error
//showName(book); 필드가 없기때문에 error
```

## 유틸리티 타입 (Utility types)

```ts
interface User {
  id: number;
  name: string;
  gender: "m" | "f";
}

//1. keyof
type UserKey = keyof User; // = 'id'|'name'|'gender'

const uk: UserKey = "name";
//const uk:UserKey="age" error

//2. Partial<T> : 일부 프로퍼티만 사용할때
let admin: Partial<User> = {
  id: 1,
  name: "bob",
  //gender가 없어도 에러 x, 없는 프로퍼티를 사용하면 에러
};

//3. Required<T> : 모든 프로퍼티를 필수로 바꿈 (?도 필수로)

//4. Readonly<T> : 할당만 가능하고 수정x
let readAdmin: Readonly<User> = {
  id: 1,
  name: "read",
  gender: "m",
};
//readAdmin.name="change"; error

//5. Record<K,T> : <key,type> , 객체의 key, value 타입을 지정
type Grade = "1" | "2" | "3";
type Score = "A" | "B" | "F";

const score: Record<Grade, Score> = {
  //key(Grade)의 값을 모두 사용해야함
  1: "A",
  2: "B",
  3: "A",
};

interface User2 {
  id: number;
  name: string;
}
function isValid(user: User) {
  const result: Record<keyof User2, boolean> = {
    id: user.id > 0,
    name: user.name !== "",
  };
  return result;
}

//6.Pick<T,K> : T타입에서 K프로퍼티만 골라서 사용
const pickUser: Pick<User, "id"> = {
  id: 0,
  //name:"pick" error
};

//7.Omit<T,K> : K 프로퍼티 생략
const omitUser: Omit<User, "id" | "gender"> = {
  name: "omit",
  //id:9, error
};

//8.Exclude<T1,T2> : T1에서 T2를 제외하고 사용
type T1 = string | number;
type T2 = Exclude<T1, number>; // = string

//9.NonNullable<Type> : null,undefined를 제외한 타입
type Tp1 = string | null | undefined | void;
type Tp2 = NonNullable<Tp1>;
```
