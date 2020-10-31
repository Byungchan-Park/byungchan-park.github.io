---
title: "Webpack과 Babel로 프론트엔드 개발 환경 구성하기"
excerpt: "create-react-app 사용하지 않고 리액트 시작하기"

categories:
  - React
  - Webpack
  - Babel
tags:
  - React
  - Webpack
  - Babel
last_modified_at: 2020-10-31T08:06:00-05:00
---

## 프로젝트 생성 & npm init 으로 package.json 파일을 생성

```bash
npm init -y
```

- package.json에 정보를 기록하고 프로젝트를 초기화한다.
- -y : default 설정으로 즉시 init한다.

## 리액트 패키지 설치

```bash
npm install --save react react-dom
```

- –save : package.json의 dependencies에 해당 패키지를 추가한다.
- react : 리액트 라이브러리
- react-dom : 리액트 컴포넌트가 DOM에 접근할 수 있게 해주는 패키지. 항상 react와 동일한 버전을 유지하자.
- react-hot-loader : hot loader 설치. 프로덕션 모드일때는 자동으로 인식하여 실행되지 않기때문에 –save 옵션을 사용해도 된다.

## 웹팩 패키지 설치

```bash
npm install --save-dev webpack webpack-cli webpack-dev-server
```

- –save-dev : package.json의 devDependencies에 해당 패키지를 추가한다. 프로덕션 배포시 install 되지 않는다.
- webpack : 여러 코드를 브라우저가 이해할 수 있도록 변환해주고, 번들링해준다.
- webpack-cli : webpack 터미널 도구이며, webpack 명령어를 사용할 수 있게 해준다. v4 부터 필수임.
- webpack-dev-server : 개발단계에서 hot-reloading이 가능한 웹서버를 실행시켜준다.

## 필요한 로더 설치

```bash
npm install --save-dev css-loader style-loader url-loader file-loader
```

- css-loader : css 파일을 자바스크립트 파일에서 모듈처럼 불러와 사용할 수 있게끔 해준다.
- style-loader : 자바스크립트로 변경된 스타일을 동적으로 돔에 추가하는 로더이다.
- url-loader : 크기가 작은 이미지나 글꼴 파일 등을 base64로 변환하여 번들 파일에 포함시킨다.
- file-loader : 파일을 다룬다.
- html-webpack-plugin : 번들링할때 html파일을 생성해준다. 컴파일 할 때마다 파일 이름에 해시가 포함되는경우 유용하다.

## 필요한 플러그인 설치

```bash
npm install -D clean-webpack-plugin mini-css-extract-plugin html-webpack-plugin
```

- clean-webpack-plugin : 빌드 이전 결과물을 제거하는 플러그인이다.
- mini-css-extract-plugin : css를 별도 파일로 뽑아내는 플러그인이다.
- html-webpack-plugin : html 파일을 후처리하는 데 사용하는 플러그인이다. 공백을 제거하고 주석을 제거하여 파일의 용량을 줄일 수 있게 해준다.

## 바벨 패키지 설치

바벨은 ECMAScript2015 이상의 코드를 적당한 하위 버전으로 바꾸는 것이 주된 역할이다. 이렇게 바뀐 코드는 인터넷 익스플로러나 구버전 브라우저처럼 최신 자바스크립트 코드를 이해하지 못하는 환경에서도 잘 동작한다.

```bash
npm install --save-dev babel-loader @babel/core @babel/preset-env @babel/preset-react
```

- babel-loader : Webpack이 모듈을 번들링할 때 Babel을 사용하여 ES6+ 코드를 ES5 코드로 트랜스파일링하는 데 사용된다.
- @babel/preset-env : ES6 이상의 코드를 ES5로 트랜스파일하는 데 사용된다.
- @babel/preset-react : .jsx 코드를 ES5로 트랜스파일하는 데 사용된다.

```jsx
// .babelrc
// 설치한 @babel/preset-env, @babel/preset-react를 사용하겠다는 의미이다.
{
  "presets": ["@babel/preset-env", "@babel/preset-react"]
}
```

프로젝트의 폴더 구조는 다음과 같다.

```bash
- node_modules
- public
  - index.html
- src
  - App.js
  - index.js
- .babelrc
- package.json
- package-lock.json
- webpack.config.js
```

webpack.config.js:

```jsx
const path = require("path");
const { CleanWebpackPlugin } = require("clean-webpack-plugin");
const MiniCssExtractPlugin = require("mini-css-extract-plugin");

module.exports = {
  // 현재 모드를 개발 환경으로 설정한다.
  mode: "development",
  // entry : 웹팩에서 웹 자원을 변환하기 위해 필요한 최조 진입점이자 자바스크립트 파일 경로
  // 웹팩은 entry를 통해서 모듈을 로딩하고 하나의 파일로 묶는다.
  entry: {
    main: "./src/index.js",
  },
  // 웹팩에서 entry로 찾은 모듈을 하나로 묶은 결과물을 반환할 위치이다.
  output: {
    path: path.resolve(__dirname, "dist"),
    publicPath: "/",
    // '[name]'은 entry에 추가한 main이 문자열로 들어오는 방식이다.
    filename: "[name].js",
  },
  // loader : 자바스크립트가 아닌 다른 자원(HTML, CSS, Image)를 빌드할 수 있도록 도와주는 속성이다.
  module: {
    rules: [
      {
        test: /\.(js|jsx)$/, // .js 혹은 .jsx 확장자로 마치는 모든 파일
        exclude: /node_modules/, //  node_modules 폴더 내에 있는 파일은 제외한다.
        use: {
          loader: "babel-loader",
        },
      },
      {
        test: /\.css$/, // .css 확장자로 마치는 모든 파일
        use: [
          process.env.NODE_ENV === "production"
            ? MiniCssExtractPlugin.loader // 프로덕션 환경
            : "style-loader",
          "css-loader",
        ], //개발 환경, css-loader가 먼저 적용되고 style-loader가 그 다음으로 적용된다.
      },
      {
        test: /\.png$/, // .png 확장자로 마치는 모든 파일
        loader: "file-loader",
        options: {
          publicPath: "./dist/", // prefix를 아웃풋 경로로 지정
          name: "[name].[ext]?[hash]", // 파일명 형식
        },
      },
      {
        test: /\.html$/, // .html 확장자로 마치는 모든 파일
        use: [
          {
            loader: "html-loader", // 웹팩이 html을 읽을 수 있게 해준다.
            options: {
              minimize: true,
            },
          },
        ],
      },
    ],
  },
  // plugin : 웹팩의 기본적인 동작 외 추가적인 기능을 제공하는 속성이다.
  // 결과물의 형태를 바꾸는 역할을 한다.
  plugins: [
    // HTML 파일을 후처리하는 데 사용한다.
    new HtmlWebPackPlugin({
      template: "./public/index.html", // 템플릿 경로를 지정
      filename: "./index.html",
      // 배포 모드일 때는 파일을 압축하고 불필요한 주석을 제거한다.
      minify:
        process.env.NODE_ENV === "production"
          ? {
              collapseWhitespace: true, // 빈칸 제거
              removeComments: true, // 주석 제거
            }
          : false,
    }),
    // 빌드 이전 결과물을 제거하는 플러그인이다.
    new CleanWebpackPlugin(),
    ...(process.env.NODE_ENV === "production"
      ? [new MiniCssExtractPlugin({ filename: `[name].css` })]
      : []),
  ],
  // 개발 서버 설정
  devServer: {
    // 콘텐츠를 제공할 경로 지정
    // contentBase: __dirname + "/dist/",
    // inline 모드 활성화
    inline: true,
    // webpack의 HMR 기능 활성화
    hot: true,
    // 사용될 호스트 지정
    host: "localhost",
    // 접속 포트 설정
    port: 5500,
    // open page when start
    open: true,
  },
};
```

loader는 module과 rules 라는 키워드를 사용한다. 룰을 정의할 때는 아래와 같은 형태를 사용한다.

```jsx
module: {
  rules: [
    {
      test: '빌드할 파일 확장자 정규식', ex) /\.(js|jsx)$/
      exclude: '제외할 파일 정규식',
      use: {
        loader: '사용할 로더 이름',
        option: '로더 옵션'
      }
    }
  ]
}
```

/src/App.js :

```jsx
import React from "react";

const App = () => {
  return (
    <div>
      <h1>Hello, React!!!</h1>
    </div>
  );
};

export default App;
```

/src/index.js :

```jsx
import React from "react";
import { render } from "react-dom";

import App from "./App";

render(<App />, document.getElementById("root"));
```

/public/index.html

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Minimal React Boilerplate</title>
  </head>
  <body>
    <div id="root"></div>
  </body>
</html>
```

package.json :

```json
"scripts": {
  "dev": "webpack serve",
  "build": "webpack --config webpack.config.js"
}
```

npm run dev 명령어를 터미널에서 입력 후 실행하면 실시간으로 리로딩되는 webpack-dev-server를 실행시킬 수 있다.

npm run build 명령어를 터미널에서 입력 후 실행하면 모듈이 번들링된 후 dist폴더가 생성되고, 결과물이 dist폴더 안에 생성된다.
