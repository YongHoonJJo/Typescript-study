### Decorator

데코레이터란?

```
- 함수다.
- 컴파일 타임에는 그 함수의 타입만 체크한다.
- 런타임에 사용 및 처리가 된다.
- 클래스, 메서드, 프로퍼티, 메서드의 파라미터에 사용할 수 있다.
- 클래스가 인스턴스로 만들어질 때가 아니라, 최초 클래스가 참조될 때, 한번만 적용된다.
```

<br>

Decorator 를 사용하려면 `tsconfig.json` 에서 `experimentalDecorators: true` 설정을 해야한다. 

<br>

#### Method Decorator

기본구조

```typescript
function methodDecorator(
  target: any,
  propertyKey: string,
  descriptor: PropertyDescriptor
) {
  console.log('target', target);            // 클래스
  console.log('propertyKey', propertyKey);  // 메서드명
  console.log('descriptor', descriptor);    // PropertyDescriptor (lib.es5.d.ts)
}

class Test3 {
  @methodDecorator
  public print() {}
}
```

실행결과

```
target Test3 {}
propertyKey print
descriptor { value: [Function: print],
  writable: true,
  enumerable: false,
  configurable: true }
```

> `descriptor.value` 는 여기서  `print` 메서드를 가리킨다. 
>
> 객체를 만들지 않아도 된다. 

<br>

##### Ex1)

```typescript
function log(target: Object, propertyKey: string, descriptor: TypedPropertyDescriptor<any>) {
  const originalMethod = descriptor.value; // save a reference to the original method
  console.log('call @log')
  // NOTE: Do not use arrow syntax here. Use a function expression in 
  // order to use the correct value of `this` in this method (see notes below)
  descriptor.value = function(...args: any[]) {
      // pre
      console.log("The method args are: " + JSON.stringify(args));
      // run and store result
      const result = originalMethod.apply(this, args);
      // post
      console.log("The return value is: " + result);
      // return the result of the original method (or modify it before returning)
      return result;
  };

  return descriptor;
}

class MyClass {
  @log
  myMethod(arg: string) { 
      console.log('myMethod()')
      return "Message -- " + arg;
  }
}

new MyClass().myMethod("testing");
```

실행결과

```
call @log
The method args are: ["testing"]
myMethod()
The return value is: Message -- testing
```

<br>

##### Ex2) 

```typescript
function log(target: Object, propertyKey: string, descriptor: TypedPropertyDescriptor<any>) {
  const originalMethod = descriptor.value; // save a reference to the original method
  console.log('call @log')

  descriptor.value = function(...args: any[]) {
      console.log("The method args are: " + JSON.stringify(args));
      const result = originalMethod.apply(this, args);
      console.log("The return value is: " + result);
      return result;
  };

  return descriptor;
}

function log2(target: Object, propertyKey: string, descriptor: TypedPropertyDescriptor<any>) {
  const originalMethod = descriptor.value; // save a reference to the original method
  console.log('call @log2')
  
  descriptor.value = function(...args: any[]) {
      console.log("222The method args are: " + JSON.stringify(args));
      const result = originalMethod.apply(this, args);
      console.log("222The return value is: " + result);
      return result;
  };

  return descriptor;
}

class MyClass {
  @log
  @log2
  myMethod(arg: string) { 
      console.log('myMethod()')
      return "Message -- " + arg;
  }
}

console.log('before new MyClass().myMethod("testing")')
new MyClass().myMethod("testing");
console.log('after new MyClass().myMethod("testing")')
```

실행결과

```
call @log2
call @log
before new MyClass().myMethod("testing")
The method args are: ["testing"]
222The method args are: ["testing"]
myMethod()
222The return value is: Message -- testing
The return value is: Message -- testing
after new MyClass().myMethod("testing")
```

> myMethod 가 처음 가리켰던 함수가 A 라고 하자. 
>
> `@log2` 가 먼저 호출되어 myMethod 는 B 라는 새로운 함수를 가리키게 되고, B 라는 함수 내부에는 A를 실행시키는 코드가 포함되어 있다. 
>
> 다음으로 `@log` 가 호출되면, myMethod 는 C 라는 새로운 함수를 가리키게 되고, C라는 함수 내부에서는 B 를 실행시키는 코드가 포함되어 있다. 
>
> 그래서 실행결과가 위와 같아진다. 

<br>

##### Ex 3)



이처럼 데코레이터를 통해 전처리를 한 다음 생성자를 통해 후처리를 할 수 있다. 





<br>

##### Reference

<https://www.notion.so/Decorators-ee9928ba4a0b4c5584ddaf478e3910ee>

<https://stackoverflow.com/questions/29775830/how-to-implement-a-typescript-decorator>