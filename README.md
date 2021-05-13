## CRA 없이 리액트 환경 만들기 연습

### 폴더 생성 및 초기화

프로젝트 폴더를 만들고 yarn을 초기화하여 package.json 파일을 생성해준다.

```js
yarn init -y
```

### public 폴더 생성

정적 파일들을 담을 public 폴더를 생성한다.

public 폴더 안에 index.html 파일을 만들고 html 코드를 넣어준다.

```html
<!DOCTYPE html>
<html lang="ko">
  <head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1" />
    <title>Title</title>
  </head>
  <body>
    <div id="root"></div>
  </body>
</html>
```

### react 설치

```js
yarn add react react-dom
```

### typescript 설치

```js
yarn add --dev typescript

yarn run tsc --init
```

tsconfig.json 설정

```js
{
  "compilerOptions": {
    "sourceMap": true,  // 소스맵(*.map) 파일 생성 여부
    "jsx": "react", // Resolve: Cannot use JSX unless '--jsx' flag is provided
    "esModuleInterop": true // import * as => import
  }
}
```

[1 : 타입스크립트란? & 기본 설치](https://jellymando.github.io/typescript/2020-12-24-typscript-notion/)

### babel 설치

```js
yarn add @babel/core --dev
```

### webpack 설치

```js
yarn add -D webpack webpack-cli webpack-dev-server
```

### loader 설치

웹팩의 번들링에 필요한 로더 설치

```js
yarn add -D babel-loader css-loader style-loader file-loader ts-loader
```

### 플러그인 설치

웹팩으로 번들링을 한 후에 적용할 플러그인들을 설치

```js
yarn add -D html-webpack-plugin clean-webpack-plugin
```

yarn run dev

### 웹팩 설정

루트 경로에 webpack.config.js 파일을 만들고 설정

- **entry** : 모듈의 의존성이 시작되는 부분으로 웹팩은 이 Entry 속성에 명시된 파일을 기준으로 의존성 트리를 만들어 하나의 번들 파일을 만든다.

```js
module.exports = {
  entry: {
    main: "./src/index.tsx",
  },
};
```

- **resolve** : 웹팩이 모듈을 처리하는 방식 정의하는 것으로 확장자를 생략하고도 인식하게 만든다.

```js
resolve: {
  extensions: [".ts", ".tsx", ".js", ".jsx"];
}
```

- **output** : 웹팩의 번들링 결과물에 대한 옵션. 번들링 결과를 path 경로에 filename으로 묶어낸다. entry 설정은 항상 프로젝트 디렉터리 내부이기 때문에 상대 경로로 하는 반면에, output 설정은 항상 프로젝트 디렉터리 내부라는 보장이 없으므로 절대 경로로 한다. path 모듈을 사용하기 위해서 설정 파일의 module.exports 위에 선언해주자.

```js
const path = require('path');

output: {
  filename: 'build.js',
  path: path.resolve(__dirname, 'build/js'),
}
```

> 참고로 \_\_dirname은 NodeJS에서 현재 프로젝트 디렉터리를 의미합니다. npx webpack을 실행하면 프로젝트 최상위 디렉터리에 build.js 파일이 생성되었음을 확인할 수 있습니다.

- **module.rules** : Loader 추가

```js
module: {
  rules: [
    {
      test: /\.(js|jsx)$/,
      exclude: '/node_modules/',
      loader: 'babel-loader'
    },
    {
      test: /\.css$/,
      use: [
        { loader: 'style-loader' },
        { loader: 'css-loader' }
      ]
    },
    {
      test: /\.jfif$/,
      loader: 'file-loader',
      options: {
        name: '[name].[ext]'
      }
    },
    { test: /\.tsx?$/, loader: "ts-loader" }
  ]
},
```

- **plugins** : 웹팩을 실행할 때 마다 기존에 있던 번들 파일을 먼저 깔끔히 지우고 싶은 경우에는 clean-webpack-plugin 플러그인을 사용

```js
plugins: [
    new CleanWebpackPlugin(),
    new HtmlWebpackPlugin({
      template: "./public/index.html",
    }),
  ],
```

- **mode** : production - 빌드 / development - dev <u>(생략..)</u>

### package.json에 스크립트 입력

```js
{
  "scripts": {
    "dev": "webpack serve --progress --mode development",
    "build": "webpack --progress --mode production"
  }
}
```

webpack-cli가 3.x 버전이라면 webpack-dev-server, 4.x 버전이라면 webpack serve 이다!!

webpack-dev-server로 하면 not found module 에러가 계속 나서 찾다가 해결..