###  Type Guard



```typescript
function main() {
  
  const token: string|null = getToken();

  if (token === null) {
    location = '/login';
    return;
  }
  
	getUserByToken(token)
    .then(user => {
      if (user === null) {
        location = '/login';
      }
      return getBooks(token);
    })
    .then(books => { ... }
    // ...
}
```

> 위에서 `token` 은 string 일 수도, null 일 수도 있다. 그렇기 때문에 if 문 위에서 `token.length` 와 같은 프로퍼티를 사용하려고 하면, 해당 변수에 빨간줄이 생기면서 null 일 수도 있다는 메세지를 띄워준다 .

<br>

이처럼 `null` 일 수 있는 변수에 대해 처리할 수 있는 방법이 세가지 있다. 

- Type Assertion
- non-null assertion
- Type Guard

<br>

1) Type Assertion

Type Assertion 은 타입을 강제하는 것이다. 

```typescript
function main() {
  
  const token: string|null = getToken();

  <string>token;
  // or
  const a = token as string;
}
```

> 위와 같은 방법으로 사용할 수 있다. (후자를 선호)

<br>

```typescript
function main() {
  
  const token: string|null = getToken();

  const a = token as string;
  
  a.split('/') // runtime error
}
```

> 여기서 런타임과 타입 시스템 사이에 괴리가 발생한다. 

<br>

Assertion 을 사용하게 되면, 실제로 `token` 에 null 이 들어갔을 때, `a` 에는 빈문자열이 들어가게 된다. 하지만, 런타임에서는 null 이 들어가기 때문에 위와 같은 상황에서 런타임 에러가 발생하게 된다. 이처럼 어설션을 사용하게 되면, 실제값과 타입이 달라지기 때문에 앞으로 어떤 것도 확정할 수 없는 상태가 되어 버린다. 

<br>

Type Assertion 은 개발자가 타입에 대해 100% 확정할 수 있을 때 사용하면 된다. 

```typescript
const listElement = document.querySelector('#list');
listElement.append(...)
```

> `listElement` 의 경우 `: HTMLListElement|null` 으로 추론이 되어 있어, `append()` 메서드의 사용을 확정할 수 없다는 메세지가 뜨게 된다. 

<br>

하지만 아래의 경우, 누가봐도 `#list` 에 대한 값을 가져올 수 있다는 것을 확인할 수 있는 상태이다. 이럴 땐 Type Assertion 을 사용하면 된다. 

```typescript
document.addEventListener('DOMContentLoaded', main);
function main() {
	const listElement = document.querySelector('#list') as HTMLListElement;
	listElement.append(...)
}
```

> 이와 같이 DOMContentLoaded 가 된 다음 실행되었기 때문에 DOM 이 분명존재한다는 것을 개발자는 확신할 수 있는 상태지만 타입스크립트의 입장에서는 그것을 알 수 없다. 이럴 때 as 를 통해 assertion 을 사용한다.

<br>



 2) non-null assertion

```typescript
function main() {
  
  const token: string|null = getToken();

  const a = token!;
  
  a.split('/') // runtime error
}
```

> 위 코드의 경우 `a` 가 string 으로 들어온다.  이는, Union 타입인 경우 null 만 없애주는 역할을 한다. 

<br>

3) 타입 가드

타입 가드에는 여러 종류가 있다. 그 중 아래와 같은 방식은 리터럴 타입가드라고 한다. 

```typescript
function main() {
  
  const token: string|null = getToken();

  if (token === null) {
    location = '/login';
    return;
  }
  
	//...
}
```

> null 은 리터럴 값이기 때문이며, 런타임시 체크가 진행된다. 

<br>

 그 외 typeof type gurad, instanceof type guard, in type guard, custom type guard 가 있다. 