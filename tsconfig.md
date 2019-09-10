### tsconfig.json

#### include, exclude

컴파일에 포함시킬 / 제외시킬 파일 명시

```json
{
  "include": [
    "src/**/*.ts"
  ],
  "exclude": [
    "node_modules"
  ]
}
```

<br>

#### compilerOptions

컴파일에 대한 옵션들

```json
{
	"include": [],
  "exclude": [],
  "compilerOptions": {
    "module": "commonjs",
    "rootDir": "src",
    "outDir": "dist",
    "target": "es5"
  }
}
```

> "rootDir" : 모듈의 루트가 되는 폴더<br>"outDir": 컴파일된 파일들이 만들어지는 최상위 폴더

```
--outDir : Redirect output structure to the directory.
--rootDir : Specifies the root directory of input files. Only use to control the output directory structure with --outDir. // (Default : common root directory is computed from the list of input files)
```



<br>

프로젝트 구조

```
├── src
│   ├── hello.ts
│   └── util.ts
├── tsconfig.json
└── package.json
```

<br>

$ tsc

컴파일 후 프로젝트 구조

```
├── dist
│   ├── hello.js
│   └── util.js
├── src
│   ├── hello.ts
│   └── util.ts
├── tsconfig.json
└── package.json
```

> es5 형태의 코드로 컴파일된 결과를 확인할 수 있다. 

<br>

웹 애플리케이션 같은 경우 dist 폴더에 만들어진 파일들을 하나의 파일로 합쳐서 대체로 배포를 하는데, 웹팩과 같은 모듈 번들러를 통해 적용 가능하다. 

<br>

#### sourceMap

컴파일된 내용과 원본 ts 파일을 연결해서 보고 싶은 경우 설정.

```json
{
	"include": [],
  "exclude": [],
  "compilerOptions": {
    "module": "es6",
    "rootDir": "src",
    "outDir": "dist",
    "target": "es5",
    "sourceMap": true
  }
}
```

<br>

$ tsc

컴파일 후 프로젝트 구조

```
├── dist
│   ├── hello.js
│   ├── hello.js.map
│   ├── util.js
│   └── util.js.map
├── src
│   ├── hello.ts
│   └── util.ts
├── tsconfig.json
└── package.json
```

> 브라우저에서 소스코드를 확인할 경우, sourceMap 이 있으면, ts 원본 파일(hello.ts)에 대한 내용이 보여지고, 아니면 컴파일된 결과에 대한 내용(hello.js)을 볼 수 있게 된다.

 <br>

#### removeComments

주석 제거

```json
{
	"include": [],
  "exclude": [],
  "compilerOptions": {
    "module": "es6",
    "rootDir": "src",
    "outDir": "dist",
    "target": "es5",
    "sourceMap": true,
    "removeComments": true
  }
}
```

<br>

#### noImplicitAny

함수의 파라미터 경우 타입을 정의하지 않으면, 암묵적으로 any 처리가 된다.

`noImplicitAny` 를 설정하면 any 형태로 선언되는 것을 방지할 수 있다.

```json
{
	"include": [],
  "exclude": [],
  "compilerOptions": {
    "module": "es6",
    "rootDir": "src",
    "outDir": "dist",
    "target": "es5",
    "sourceMap": true,
    "removeComments": true,
    "noImplicitAny": true
  }
}
```

##### Author by Harry

<br>

<hr>

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



