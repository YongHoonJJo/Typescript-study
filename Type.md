### 타입스크립트의 여러가지 타입



#### 1. 다양한 타입

```typescript
const arr1: number[] = [1, 2, 3]
const arr2: Array<number> = [1, 2, 3]
arr1.push('a') // 타입 에러
```

> 배열 타입은 두 가지 방법으로 정의할 수 있다.

<br>

```typescript
const msg: string = 'Big'
const size: number = 123

const data: [string, number] = [msg, size]
```

> 문자열과 숫자로 구성된 튜플 타입을 정의.

<br>

##### null 과 undefined 

```typescript
let v1: undefined = undefind
let v2: null = null
v1 = 123 // 타입 에러

let v3: number | undefined = undefined
v3 = 123
```

> undefined 와 null 타입은 다른 타입고 ㅏ함께 유니온 타입으로 정의할 때 많이 사용된다.

<br>

##### 문자열 리터럴과 숫자 리터럴 

```typescript
let v1: 10 | 20 | 30;
v1 = 10;
v1 = 15; // 타입 에러

let v2: 'good' | 'bad';
v2 = 'soso'; // 타입 에러
```

> v1 은 오직 숫자 10, 20, 30만 가질 수 있는 타입으로 정의되었다.

<br>

##### any

```typescript
let a: any;
a = 123;
a = '456';
a = () => {};
```

> 숫자, 문자, 함수 등 모든 종류의 값을 허용하는 타입.
>
> 기존 JS 프로젝트를 TS로 포팅하는 경우 유용하게 사용될 수 있다.

<br>

##### void 와 never

```typescript
function f1(): void {
  console.log('hello')
}

function f2(): never {
  throw new Error('some error')
}

function f3(): never {
  while(true) {
    // ...
  }
}
```

> 리턴값이 없는 경우 void 타입.
>
> 항상 예외가 발생해서 비정상적으로 종료가 되건, 무한 루프때문에 종료되지 않는 함수의 리턴타입은 never 타입으로 정의.

<br>

##### object

```typescript
let obj: object;
obj = { name: 'abc' };
console.log(v.prop1) // 타입 에러
```

> 객체의 속성에 대한 정보가 없기 때문에 특정 속성값에 접근하면 타입 에러가 발생.
>
> 속성 정보를 포함해서 타입을 정의하기 위해서는 인터페이스를 사용.

<br>

##### 교차 타입(&)과 유니온 타입(|)

```typescript
let a: (1 | 3 | 5) & (3 | 5| 7);
a = 3;
a = 1; // 타입 에러
```

> 교집합과 합집합을 표현하는 타입으로 a 의 타입은 3 | 5 와 같다.

<br>

##### type 키워드로 타입에 별칭 주기

```typescript
type Width = number | string;
let width: Width;
width = 100;
width = '100px';
```

> Width 는 일반적인 타입처럼 사용할 수 있게 된다.

<br>

#### 2. 열거형 타입

열형 타입은 enum 키워드를 사용해서 정의하며, 열거형 타입의 각 원소는 값이나 타입으로 사용될 수 있다.

```ts
enum Fruit {
  Apple,
  Banana = 5,
  Orange,
}

const v1: Fruit = Fruit.Apple;
const v2: Fruit.Apple | Fruit.Banana = Fruit.Banana;

console.log(Fruit.Apple, Fruit.Banana, Fruit.Orange); // 0, 5, 6
```

> 열거형 타입을 이용해서 과일을 정의하고, 원소중 Apple 을 값으로 사용하였으며, 또한 타입으로도 사용하였다.
>
> 첫 번째 원소에 값을 할당하지 많으면 디폴트로 0이 할당되며, 명시적으로 값을 입력하지 않으면 이전 원소에서 1만큼 증가한 값이 할당된다.

<br>

```ts
var Fruit;
(function(Fruit) {
  Fruit[(Fruit['Apple'] = 0)] = 'Apple';
  Fruit[(Fruit['Banana'] = 5)] = 'Banana';
  Fruit[(Fruit['Orange'] = 6)] = 'Orange';
})(Fruit || Fruit = {})
console.log(Fruit.Apple, Fruit.Banana, Fruit.Orange) // 0, 5, 6
```

> 열거형 타입은 객체로 존재하며 컴파일 후에도 관련된 코드가 남는다. 또한, 각 원소는 이름과 값이 양방향으로 매핑된다.

<br>

```ts
enum Fruit {
  Apple,
  Banana = 5,
  Orange,
}

console.log(Fruit.Banana) // 5
console.log(Fruit.]'Banana']) // 5
console.log(Fruit[5]) // Banana
```

> 열거형 타입은 객체로 존재하기 때문에 해당 객체를 런타임에 사용할 수 있다.

<br>

##### 열거형 타입의 값으로 문자열 할당하기

```ts
enum Language {
  Koran = 'ko',
  English = 'en',
  Japanese = 'jp',
}
```

```ts
var Language;
(function(Language) {
  Language['Koran'] = 'ko';
  Language['English'] = 'en';
  Language['Japanese'] = 'jp';
})(Language || (Language = {}))
```

> 위의 코드는 아래와 같이 컴파일되며, 열거헝 타입의 원소에 문자열을 할당하는 경우에는 단방향으로 매핑된다.

<br>

##### 열거형 타입을 위한 유틸리티 함수

```ts
function getEnumLength(enumObject: any) {
  const keys = Obejct.keys(enumObject);
  return keys.reduce((acc, key) => (typeof enumObject[key] === 'string' ? acc + 1 : acc), 0)
}
```

> 열형 타입의 원소 개수를 알려주는 함수.

<br>

```ts
function isValidEnumValue(enumObject: any, value: number | string) {
  if(typeof value === 'number') {
    return !!enumObject[value]
  } else {
    return (
    	Object.keys(enumObject)
        .filter(key => isNaN(Number(key)))
        .find(key => enumObject[key] === value) != null
    )
  }
}
```

> 열거형 타입에 존재하는 값인지 검사하는 함수.
>
> 값이 숫자면 양방향으로 매핑되었는지 검사하고, 값이 문자열이면 양방향 매핑에 의해 생성된 키를 제거하고 해당 값이 존재하는지 검사하는 로직.

<br>

##### 상수 열거형 타입

열거형 타입은 컴파일 후에도 남아 있기 때문에 번들 파일의 크기가 불필요하게 커질 수 있다. 열거형 타입의 객체에 접근하지 않는다면 굳이 컴파일 후에 객체로 남겨놓을 필요는 없다. 상수 열거형 타입을 사용하면 컴파일 결과에 열거형 타입의 객체를 남겨 놓지 않을 수 있다.

```ts
const enum Fruit {
  Apple,
  Banana,
  Orange,
}
const fruit: Fruit = Fruit.Apple;
```

```ts
var fruit = 0;
```

> 컴파일 결과는 위와 같다.

```ts
console.log(getEnumLength(Fruit))
```

> 하지만, 열거형 타입의 객체를 사용할 수 없어, 위의 코드는 컴파일 타임에 에러가 발생한다.

<br>

#### 3. 함수타입

함수 타입을 정의하기 위해서는 매개변수 타입과 리턴 타입이 필요하다.

```ts
function getInfoText(name: string, age: number): string {
  //...
  return `name: ${name}, age: ${age}`
}
```

<br>

함수를 저장할 변수의 타입은 화살표 기호를 이용한다.

```ts
const getinfoText: (name: string, age: number) => string = function(name, age) {
  //...
}
```

> 이 경우, 함수에서는 매개변수 타입과 리턴 타입을 작성하지 않아도 된다.

<br>

##### 선택 매개변수

선개 매개변수는 반드시 입력하지 않아도 되는 매개변수.

```ts
function getInfoText(name: string, age: number, language?: string): string {
  //...
}
```

> 매개변수 오른쪽에 ? 를 입력하면 된다. 선택 매개변수는 맨 오른쪽에만 올 수 있다.

<br>

##### 매변수의 기본값 정의하기

```ts
function getInfoText(
	name: string,
  age: number = 15,
  language = 'korean'
): string {
  //...
}

const f1: (name: string, age?: number, language?: string) => string = getInfoText;
```

> 기본값이 있는 매개변수는 선택 매개변수이다.

<br>

##### 나머지 매개변수

```ts
function getInfoText(name: string, ...rest: string[]): string {
  //...
}
```

> 나머지 매개변수는 배열로 정의할 수 있다.

<br>

##### this 타입

```ts
function getParam(this: string, index: number): string {
  const params = this.split(',')
}
```

> this 타입은 첫 번째 매개변수 위치에서 정의할 수 있으며, 정의하지 않으면 any 타입이 된다. 또한, this 타입은 매개변수가 아니기 때문에 index 가 첫 번째 매개변수가 된다.

<br>

##### 원시 타입에 메서드 추가하기

primitive 타입에 메서드를 등록할 때는 인터페이스를 이용한다.

```ts
interface String {
  getParam(this: string, index: number): string;
}
String.prototype.getParam = getParam;
```

> 문자열 타입에 메서드를 추가하는 코드.

<br>

##### 함수 오버로드: 여러 개의 타입 정의하기

```ts
function add(x: number, y: number): number;
function add(x: string, y: string): string;

function add(x: number | string, y: number | string): number | string {
  //...
}
console.log(add(1, '2')) // 타입 에러
```

> 매변수와 리턴 타입의 모든 가능한 조합을 정의 한 상태이다. 실제 구현하는 쪽에서 정의한 타입은 함수 오버로드의 타입 목록에서 제외된다.

<br>

##### 명명된 매개변수

인터페이스를 활용한다.

```ts
interface Param {
  name: string;
  age?: number;
  language?: string
}

function getInfoText({ name, age = 15, language}: Param): string {
  //...
}
```

<br>

#### Reference

<https://book.naver.com/bookdb/book_detail.nhn?bid=15008532>