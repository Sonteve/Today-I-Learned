loader
웹팩에는 loader라는 것이 있는데 js뿐만 아니라 css, 이미지, 폰트 모든것을 모듈화하여 import 구문으로 불러올수 있게 해준다.
이제 js파일 안에서 css나 문자열로 변환된 이미지를 import 해올수있는것이다.

기본적으로 js파일은 별도의 플러그인 설치나 설정을 해주지않아도 번들링이 된다.
하지만 번들링 될때 추가적인 작업을 하고싶다면 `webpack.config.js`에
module을 추가하고 rules안에 loader 속성을 추가해준다.

기본적으로 js파일을 처리하는 custom loader를 만들어보자.

- webpack.config.js

```javascript
const path = require("path");

module.exports = {
  mode: "production",
  entry: {
    main: "./src/app.js",
  },
  output: {
    path: path.resolve("./dist"),
    filename: "[name].js",
  },
  module: {
    rules: [
      {
        test: /\.js$/,
        use: [path.resolve("./custom-webpack-loader.js")],
      },
    ],
  },
};
```

이렇게하면 번들링될때 webpack이 entry파일부터 타고가면서 test 프로퍼티 value값의 정규식을 보고 .js파일로 끝나는 파일을 만나면 use안의 경로의 js파일을 통과하고 그 파일 내부에서 정규식에 걸린 파일에 대한 추가적인 작업을 처리할수 있다.
아래는 console.log를 alert으로 치환해주는 간단한 custom loader이다.

- custom-webpack.loader.js

```javascript
module.exports = function customWebpackLoader(content) {
  console.log(content);
  return content.replace("console.log(", "alert(");
};
```

webpack이 번들링시 webpack.config.js에 module의 rules가 있는것을 보고
확장자가 js파일로 끝나는파일을 찾아서 그 내용을 content라는 변수로 전달해준다.

현재 app.js, math.js 총 2개의 js파일이 있는데 모두 customWebpackLoader란 함수를 거쳐갔고 파일안의 내용중 console.log(부분이 alert(로 치환되었다.

- 빌드 및 서버실행

```javascript
npm run build
npx lite-server index.html
```

- 결과

![](https://images.velog.io/images/sds11609/post/d76539b0-e9a6-44c1-879a-688be4c03c08/1.png)

customWebpackLoader 함수를 통과한 모든파일의 console.log가 alert로 치환되었다.
console.log(content)를 적어주었기 때문에 console을 보면 custom loader를 거쳐간 파일들이 출력된다.

```javascript
import * as math from "./math.js";

console.log(math.sum(1, 2));

export function sum(a, b) {
  return a + b;
}
```

## css-loader

기존의 es6 module system을 보면

```javascript
import { sum } from "./math.js"; // O
import "./main.css"; // X
```

이런방식으로 다른 js 모듈을 사용하였고 css를 불러올수가없었다.

하지만 css-loader를 사용하면 css파일을 js파일안에서 모듈처럼 불러와서 사용할수가있다.

```javascript
npm i -D css-loader
```

- webpack.config.js

```javascript
const path = require("path");

module.exports = {
  mode: "development",
  entry: {
    main: "./src/app.js",
  },
  output: {
    filename: "[name].js",
    path: path.resolve("./dist"),
  },
  module: {
    rules: [
      {
        test: /\.css$/,
        use: ["css-loader"],
      },
    ],
  },
};
```

module의 rules안에 로더 속성을 적어준다.

```javascript
{
    test: /\.css$/,
    use: ["css-loader"]
}
```

뜻을 풀어보면
entry파일부터 타고가면서 번들링을 시작하는데 파일명이 .css로 끝나는 파일을 만나면 css-loader라는 로더를 사용해서 처리를 한다. 라는 뜻이다.

loader 설정을 끝냈으면 js파일에서 css를 import 해보자.

- main.css

```css
body {
  background: yellow;
  font-size: 50px;
}
```

- app.js

```javascript
import * as math from "./math";
import "./main.css";

document.write(math.sum(1, 2));
```

`npm run build` 스크립트를 실행해주면
성공적으로 번들링이 되었다면 dist폴더에 main.js가 생겼을것이다.
그리고 dist/main.js를 보면

![](https://images.velog.io/images/sds11609/post/82606c3d-065f-4855-8bd4-047c57aef8c0/1.png)

js파일안에 아까 import한 css가 잘 반영된 것을 볼수있다.

## style-loader

html을 실행시켜보면 아무것도 없는 빈창이 뜬다.

사실 css-loader자체가 css파일을 모듈화 하여 js파일안에서 불러올수있게 해주는것이지 그 css를 inline속성이나 style태그로 적용시켜주는 역할이 아니기 때문이다.

불러온 style의 적용을 위해서는 style-loader란 loader가 필요하다.

`npm i -D style-loader`

그리고 `webpack.config.js` loader 설정에 추가해준다.

- webpack.config.js

```javascript
const path = require("path");

module.exports = {
 ...
  module: {
    rules: [
      {
        test: /\.css$/,
        use: ["style-loader","css-loader"],
      }
    ],
  },
};
```

use 배열안에 여러 loader가 함께 나열될때는 먼저 사용되는 로더가 뒤에 적힌다.
여기서는 css-loader로 먼저 .css파일을 js파일안으로 불러오고 그다음 style-loader로 그 style을 적용시키는것이 된다.

설정후 다시 html파일을 실행하면
![](https://images.velog.io/images/sds11609/post/7c5cc0af-a464-49e8-8f03-dcd80868a9c0/2.png)

성공적으로 잘되는것을 볼 수 있다.
