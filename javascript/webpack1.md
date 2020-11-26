## webpack의 등장 배경

webpack 등장 이전의 javascript는 각각의 파일을 module처럼 분리시켜서 html파일에서 로드해오는 방식을 취했었다.

![](https://images.velog.io/images/sds11609/post/806da039-07f5-48eb-af8c-17d48e22f3e9/app.png)

결과
![](https://images.velog.io/images/sds11609/post/d88002fb-1ff6-4694-bfad-b75289acb7ae/2.png)

잘 작동하지만 이 방식의 경우 sum이란 함수가 브라우저의 전역객체인 window의 메서드로 들어가고

동적타입언어인 자바스크립트에서는 그 값을 쉽게 바꿀 수 있다. 아래처럼 말이다
![](https://images.velog.io/images/sds11609/post/baf45902-ceac-4652-8937-80dcf4a52231/3.png)

변수 이름의 중복으로 전역 scope가 오염되어 원하지않는 에러가 발생 할 수 있다.

## IIFE

즉시실행함수이며 함수를 다른 함수로 감싸고 즉시실행하는 방식이다.
외부에서 접근할수없는 그 함수만의 스코프를 가지기 때문에 우리가 원하는 모듈화를 이룰수있고 실제 많은 라이브러리들이 이런 방법으로 만들어졌다고한다.

```javascript
var math = math || {}; // math 네임스페이스

(function () {
  function sum(a, b) {
    return a + b;
  }
  math.sum = sum; // 네이스페이스에 추가
})();
```

이렇게 모듈화가되면 math 객체를 통해서만 sum에 접근할수있다.

## module system

공식적으로 나온 방법이 module system이다.
![](https://images.velog.io/images/sds11609/post/ee731e44-65e5-4f15-8209-bd8ba77399ea/4.png)

```typescript
<script type="module" src="./src/app.js></script>
```

html파일에서 최초로 진입하는 js파일(entry)인 app.js만 적고 type="text/javascript"대신 "module" 이란 속성을 추가한다.

결과
![](https://images.velog.io/images/sds11609/post/7e153196-f515-421e-92b5-432265e3c222/5.png)

잘작동한다. 그리고 브라우저에서 우리가 module화한 js파일 내부로의 접근이 불가능하다.
변수 중복으로 전역scope가 오염 되던것을 잘 해결하였다.
하지만 우리가 url로 해당 페이지에 접속하면 host측으로 html파일과 관련된 js나 css 파일을 받아오게된다. js파일이 여러개일경우 여러번 요청과 응답이 오고간다. 그게 많아질 경우 서버에 부하가 갈수있고 불러오는 파일이 많아짐으로 인해 client측의 사용자경험이 떨어 질수도 있다.

이방식은 최초 진입 파일로 들어가서 그 파일에 관련된 여러 파일까지 불러오는 방식이다. 그리고 모든 브라우저에서 지원하지 않는다..

**이후에 등장한 것이 **webpack**이다.**

## webpack의 등장

![](https://images.velog.io/images/sds11609/post/6de8ff41-ba50-4fcd-803e-439f30bd4ed8/webpack.jpeg)

webpack은 **번들러**라고 하는데 우리의 web application안에서 사용되는 여러개의 자바스크립트 파일을 합쳐서 하나의 js파일로 만들어준다고 생각하면된다.(결과를 꼭 1개가 아니라 설정값에 따라 여러개로 만들수도 있고 js뿐만아니라 css 등의 다른 파일도 합칠수있다.)
우리의 application이 수십개의 js파일로 이루어져 있더라도 서버로부터 많은 js파일을 받아오지 않고 합쳐진 하나의 js파일만 받아오는게 가능하다.

위에서 만들었던 파일들을 webpack을 이용해 번들링 해보자.

```javascript
npm init -y
npm i -D webpack webpack-cli
```

그리고 root경로에 webpack.config.js파일을 생성하고 그안에 웹팩 관련 설정들을 export 해주어야한다.

- webpack.config.js

```javascript
const path = require("path");

module.exports = {
  mode: "development",
  entry: {
    main: "./src/app.js",
  },
  output: {
    path: path.resolve("./dist"),
    filename: "[name].js",
  },
};
```

- mode:
  development, production ,none이 있는데
  지금은 개발용이므로 development를 선택했다.

- entry:
  시작점이 되는 최초의 js파일 경로를 entry라고 하고 entry: "경로"를 적어줄수도 있고 위처럼 번들링의 결과물로 저장될 파일의 이름을 미리 적어줄수있다.

- output:
  번들링의 결과물 파일이 저장도리 경로와 파일이름을 지정하는 옵션이다. 절대경로를 사용할것이기 때문에 nodeJs에서 기본적으로 제공하는 path module을 불러와서 resolve란 메서드로 현재 webpack.config.js파일 바로 하위경로인 dist를 번들링 결과물이 저장 되는 위치로 정한다. filenamedml `[name]` 부분은 entry에서 적었던 entry 속성의 key값이 들어가게된다. 위의 예제의 번들링의 결과물은 main.js이다.

간소화하면 아래처럼 쓸 수 있다.

```javascript
module.exports = {
  entry: "./src/app.js",
  output: {
    path: path.resolve("./dist"),
    filename: "main.js",
  },
};
```

그리고 package.json의 scripts부분을 수정한다.

- package.json

```javascript
"scripts": {
    "build": "webpack"
  },
```

이렇게 수정해준후에 `npm run build`스크립트를 실행하면 dist란 폴더가 생성되고 그곳에 번들링의 결과물인 main.js가 생긴다.

- 성공적으로 번들링 되었다.
  ![](https://images.velog.io/images/sds11609/post/016e3485-905c-41f3-95c1-b26597e6d7b8/6.png)

결과를 확인해보기위해서 index.html을 수정해준다.

- index.html

```javascript
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
  </head>
  <body>
    <script src="dist/main.js"></script>
  </body>
</html>
```

번들링 결과물인 main.js로 수정을 해주고 html을 실행시키면 성공적으로 실행되고 network탭을 보면 아까는 app.js, math.js두개의 파일을 받아왔지만 이번에는 main.js하나만 받아온것을 알 수 있다.

참고 글
[김정환님의 블로그 - 프론트엔드 개발환경의 이해: 웹팩(기본)](https://jeonghwan-kim.github.io/series/2019/12/10/frontend-dev-env-webpack-basic.html)
