### 타입추론 및 제네릭

#### 1) 타입 추론

Ex1)

```js
/*** index.js ***/
function getBooks(token) {
  return new Promise(resolve => {
    axios
      .get('url/book', {
        headers: {
          Authorization: `Bearer ${token}`,
        },
      })
      .then(res => {
        return resolve(res.data);
      })
      .catch(error => {
        return resolve(null);
      });
  });
}
```



위의 파일을 `index.ts` 로 변경해보자.

그러면, 매개변수인 `token` 등에 에러표시인 빨간줄이 생기게 된다. (VSCode 기준)

 마우스를 올려보면 `(parameter) token: any` , `Parameter 'token' implicitley has an 'any' type.` 이라는 메세지가 뜬다. 이는 매개변수로 오는 `token` 의 타입을 알려주란 뜻으로 이해할 수 있다.

함수를 표현할 때, 왜 이것을 여기에 쓰는 것이 좋은지에 대해 아는 것이 중요하다. 함수는 밖에서 어떻게 사용해야하는지를 알려주는 것은 중요하다. 

<br>

개발자는 `token` 의 타입을 추론해야한다. 해당 함수를 호출하는 곳으로 가보자. 

```js
function getToken() {
  return localStorage.getItem('token');
}

function main() {
  
  const token = getToken();

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

> `token` 에 대해 `null` 임을 체크하고 넘겨준다. 

<br>

또한, `getToken()` 의 경우 ts 파일에서도 에러가 발생하지 않는다. 또한, 이 함수에 마우스를 올려놓으면 `string|null` 로 리턴을 한다는 내용을 알려준다. 이는, **인자와 함수에 리턴타입을 꼭 명시해야 하는 것이 타입스크립트의 기본 조건이 아니라는 뜻**이 된다. 그렇다면 이 함수의 경우 리턴타입을 명시하지 않았음에도 `string|null` 을 리턴한다는 것을 vscode 는 어떻게 우리에게 알려줄 수 있는 것일까?

현재상황에서 아래와 같이 리턴타입을 명시해주게 되면 에러가 발생한다.

```typescript
function getToken(): string {
  return localStorage.getItem('token');
}
```

> 에러발생..!!

<br>

우선 `localStorage` 는 브라우저에서 사용할 수 있는 API 이다. 이런 API 는 어딘가에 타입이 명시가 되어 있다. 여기서 `localStorge` 에 마우스를 올리면 `var localStorage: Storage` 라는 메세지와 함께 타입을 알 수 있다. command + 클릭을 하면, 해당 내용이 `lib.dom.d.ts` 라는 파일에 명시되어 있다는 것을 확인할 수 있다. 그렇다면 이러한 명세는 어디에서 온걸까? 타입스크립트의 설정을 도와주는 `tsconfig.json` 을 확인해보자.

<br>

현재 `tsconfig.json` 의 내용은 아래와 같다. (`tsc —init` 으로 생성한 기본옵션으로 설정된 파일이다. )

```json
{
  "compilerOptions": {
    "target": "es5",
    "module": "commonjs",
    "strict": true,
    "esModuleInterop": true
  }
}
```

> 하지만, 이 옵션에는 저 API 에 대한 명세를 사용하겠다는 내용은 보이지 않는 것 같다.
>
> 디폴트 옵션이란 것이 존재.

 <br>

다시한번 `lib.dom.d.ts` 파일을 보자. 이 파일은 구현체가 아니다. (구현체는 브라우저에 있다.) 하지만, 그 API 에 대한 명세를 이 파일을 통해 확인할 수 있게 된 것이다. 또한 이 명세를 사용하겠다는 것은 `tsconfig.json` 에서 정할 수 있으며, 이는 `lib` 프로퍼티를 통해 설정할 수 있다. 

Typescript Documentation 을 보면, `lib` 에는 `string[]` 이 올 수 있다는 것을 확인할 수 있다 .

```json
{
  "compilerOptions": {
    "target": "es5",
    "module": "commonjs",
    "lib": [],
    "strict": true,
    "esModuleInterop": true
  }
}
```

> 위와 같이 `lib` 를 추가할 수 있다는 뜻이 된다. 또한, 위와 같이 빈 배열을 사용하면 `lib` 를 아무것도 사용하지 않겠다는 뜻이 되어, `localStorage` 에 빨간줄이 생기게 된다. 마우스를 올리면 `any` , `cannot find name 'localStorage'` 라는 메세지가 뜨게 되며, 타입스크립트는 이 객체를 모르게 된다. 하지만, 이는 브라우저에서 정상적으로 작동하는 코드이다. (잘못된 코드는 아니란 뜻이다.) 이는 구현체와는 별개일 수 있다는 뜻이 된다.

<br>

타입스크립트는 컴파일타임에 체크를 해줄 뿐이다. 타입스크립트인데 점검을 안해주면, 그건 바벨이다.  

타입 시스템을 잘 활용하는 것이 타입스크립트를 사용하는 이유중 하나이다. 

<br>

```json
{
  "compilerOptions": {
    //...
    "lib": ["dom"],
    //...
  }
}
```

> 위와 같이  `"dom"` 을 추가하면 에러가 잡히는 것을 확인할 수 있다. 이는, 타입스크립트의 내장된 타입인  `dom` 에서 찾아오게 된 것이다. 

<br>

Ex2)

이번엔 `axios` 를 확인해보자. 이는 우선 내장된 타이핑이 아니다. 구현체 자체도 사용중이지 않다. 즉, 타입도 모르고 있는지도 모르는 상태.

```
> npm i axios -D
```

> `npm` 을 통해 설치를 하자. 참고로 -D 옵션은 개발용, 붙이지 않으면 런타임에 사용되는 모듈이란 것을 의미하게 된다. 
>
> 개발을 할 때 `axios` 모듈은 웹팩을 통해 말아져 들어가게 된다. 즉, production 레벨에서는 해당 모듈을 가져다 쓸 일이 없게 된다. 그래서 여기서는 -D 옵션으로 설치를 해도 되는 상태..!!

<br>

```js
import axios from 'axios'
```

> 모듈을 불러오면 `axios` 모듈에 빨간줄이 사라지게 된다. 

<br>

그런데, `axios` 만 설치했을 뿐인데 타입스크립트를 axios 에 대해 알고 있는 상태까지 되었다. `axios` 모듈에는 당연히 해당 구현체가 들어 있는 상태이며, 이 모듈을 타입스크립트에서 사용할 수 있게 타입이 명세된 파일이 포함되어 있기 때문이다. 해당 모듈에 대한 내용을 확인해보면 `index.d.ts` 파일이 포함되어 있다. 또한 해당 모듈의 `package.json` 을 보면 이러한 명세가 어디에 포함되어 있는지  `typings` 라는 프로퍼티를 통해 표기가 되어 있다 .

```json
{
  //...
  "typings": "./index.d.ts",
  "version": "0.18.0"
}

```

> `axios` 모듈의 `package.json` 내용의 일부.

<br>

또한 타입 명세파일이 같이 포함되어 있는지에 대한 확인은, 우선 포함이 안되어 있으면 빨간줄이 생긴다. 그리고 마우스를 올려놓으면, `Could not find a declaration file for module 'moduleName'` 과 같은 메세지를 확인할 수 있다. 

<br>

Ex3)

```typescript
function getToken(): string {
  return localStorage.getItem('token');
}
```



이번엔 `getItem()` 에서드에 마우스를 올려보자. 이는 `lib.dom.d.ts` 에 명세가 되어있으며, `string|null` 값을 리턴한다는 내용을 확인할 수 있다. 이를 통해 `getToken()` 역시 `string|null` 을 알 수 있기 때문에 `function getToken(): string|null { … }`  와 같이 써줄 수도 있지만, 이는 verbose 한 타이밍이 된다. 사실 `getToken()` 함수에 리턴타입을 명시하지 않아도 마우스를 올려 놓으면 `string|null` 을 리턴한다는 것을 알려준다..!! 이렇게 이미 있는 타입을 통해 새로운 함수의 타입이 정해지는 것을 **타입추론** 이라고 한다. 즉, 타입이 추론되어 잇는 경우 굳이 명시를 하지 않아도 된다.  

<br>

이 함수를 호출한 곳으로 가보자. 

```typescript
function main() {
  
  const token = getToken();

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

> `token` 의 경우도 마우스를 올리면 `string|null` 로 추론이 되는 것을 확인할 수 있다. 

<br>

#### 2) 제네릭을 이용한 타입 명세

```typescript
public getUserByToken = (token: string): Promise<User> => {
  return new Promise(async (resolve, reject) => {
    try {
      const res = await axios.get('url/me', {
        headers: {
          Authorization: `Bearer ${token}`,
        },
      })
      resolve(res.data)
    } catch (err) {
      reject(null)
    }
  })
} 
```

<br>

`axios.get()` 에 마우스를 올리면 `(method) AxiosInstance.get<any>(url: string, config?: AxiosRequestConfig | undefined): AxiosPromise<any>`  라는 메세지를 확인할 수 있다. 이는 이 함수에 제네릭을 넣을 수 있고, `Promise<any>` 를 리턴하는 형태이다. 

<br>

```typescript
public getUserByToken = (token: string): Promise<User> => {
  return new Promise(async (resolve, reject) => {
    try {
      const res = await axios.get<User>('url/me', {
        headers: {
          Authorization: `Bearer ${token}`,
        },
      })
      resolve(res.data)
    } catch (err) {
      reject(null)
    }
  })
} 
```

> 위와 같이 변경 후 마우스를 올리면 `(method) AxiosInstance.get<User>(url: string, config?: AxiosRequestConfig | undefined): AxiosPromise<User>` 와 같이 변경되어 있는 것을 확인할 수 있다.
>
> (여기서 `User` 는 인터페이스이다.)

<br>

```typescript
public getUserByToken = (token: string): Promise<User> => {
  return new Promise(async (resolve, reject) => {
    try {
      const {data}: {data: User} = await axios.get('url/me', {
        headers: {
          Authorization: `Bearer ${token}`,
        },
      })
      resolve(res.data)
    } catch (err) {
      reject(null)
    }
  })
} 
```

> 이렇게 해도 마우스를 올리면 `(method) AxiosInstance.get<User>(url: string, config?: AxiosRequestConfig | undefined): AxiosPromise<User>` 와 같은 내용을 확인할 수 있다. 
>
> 타입추론 관련, R-Value 에 무엇이 올 지 명확하면 typing 을 하지 않아도 된다.
>
> 또한, 위의 코드의 경우, new Promise() 로 한번 더 감싼 경우로 굳이 이렇게 하지 않아도 ㄷ

<br>

제네릭은 이런 상황에서 많이 쓰인다. 어떤 라이브러리에 요청을 하고, 데이터를 받아오는 형태에서 우리가 받아야 할 데이터의 형태를 제네릭으로 넣어줄 수 있다. 