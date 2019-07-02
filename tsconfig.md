#### tsconfig.json



상황)

```typescript
import axios from 'axios'

//...
```

> `axios` 모듈을 설치했지만, `axios` 모듈 아래 빨간줄이 가며 해당 모듈을 찾을 수 없다는 경고문구가 계속해서 떳다.

<br>

`tsconfig.json` 에는 최상의 프로퍼티 8가지가 존재한다.  `compilerOptions` 는 그중 하나이다. 

```json
// tsconfig.json
{
  "compilerOptions": {
    "outDir": "./dist",
    "sourceMap": true,
    "noImplicitAny": true,
    "module": "es6",
    "target": "es5",
    "jsx": "react",
    "allowJs": true,
    "strict": true,
  }
}
```

> `tsconfig.json` 을 설정 상태는 위와 같은 상태. ~~(세상에서 환경설정이 제일 어렵다..)~~
>
> 결과적으로 `"module": "commonjs"` 로 변경 후 해결되었다.

<br>

```
The module system is independent of the language implementation. ES6 (ES2015) modules use the import/export syntax, and it is up to the module loader to interpret that.

Here you have specified using the ES2015 module system, so that enables the ES6 module syntax.

The Javascript itself may target ES5, and use only ES5 features, but it is theoretically possible to use a module loader with that code that operates with ES2015 module syntax. Although it is possible, it is not necessarily something you would want to do. It is more common to use CommonJS or AMD modules with ES5 Javascript.
```

> Ref : [Understanding “target” and “module” in tsconfig](https://stackoverflow.com/questions/41993811/understanding-target-and-module-in-tsconfig)

<br>

target 에 대한 설명

```
- 빌드의 결과물을 어떤 버전으로 할 것이냐
- 지정을 안하면 es3 이다.
```

<br>

module 에 대한 설명.

```
- 컴파일 된 모듈의 결과물을 어떤 모듈 시스템으로 할지를 결정
- target 이 'es6' 이면 es6 가 디폴트이고,
- target 이 'es6' 가 아니면 commonjs 가 디폴트이다.
- AMD 나 System 와 사용하려면, outFile 이 지정되어야 한다.
- ES6 나 ES2015 를 사용하려면, target 이 es5 이하여야 한다.
```

<br>

Document

```
--target
Specify ECMAScript target version: 'es3' (default), 'es5', or 'es6'.

--module
Specify module code generation: 'none', 'commonjs', 'amd', 'system', 'umd', 'es6', or 'es2015'.

Only 'amd' and 'system' can be used in conjunction with --outFile.
'es6' and 'es2015' values may be used when targeting ES5 or lower.
```

<br>





