## webpack plugin

앞전에서 본 loader는

webpack이 번들링 하는 시점에 관여하여 사용자가 지정한 특정 확장자를 필터링하여 원하는 작업을 처리하는데 사용되었다.
loader가 번들링중에 특정 확장자를 가진 모든파일에 대해 관여했다면 plugin인은 webpack이 번들링한 합쳐진 결과물에 작용한다.
번들링된 파일에 해당 빌드 당시 시간과 날짜등을 기록하거나 ,난독화하거나, 특정 텍스트를 추출하는 용도로 사용한다.

custom plugin은 class형식으로 만들어야하고
webpack.config.js의 plugins프로퍼티에 value값으로 []안에 new CustomPlugin 식으로 인스턴스를 넣어주면 된다.

- webpack.config.js

```javascript
const MyPlugin = require("./myplugin");

module.exports = {
  plugins: [new CustomPlugin()],
};
```

실제로 커스텀 플러그인을 만들어 사용할일은 거의 없다고 하며 자주 사용하는 plugin중에
BannerPlugin ,CleanWebpackPlugin, MiniCssExtractPlugin을 간단하게 보자

BannerPlugin은 webpack에서 기본적으로 내장하고있는 plugin이며 번들링 결과물에 빌드정보나 커밋정보를 기록하기 위해서 사용한다고 한다.

- webpack.config.js

```javascript
const { BannerPlugin } = require("webpack");

module.exports = {
  plugins: [
    new BannerPlugin({
      banner: `빌드 날짜: ${new Date().toLocaleString()}`,
    }),
  ],
};
```

이렇게하고 빌드를 하고 기본 output 경로인 dist의 main.js를 보면
![](https://images.velog.io/images/sds11609/post/17c677e1-546a-4dd3-b6aa-2ec74cf0e27b/1.png)

최상단에 빌드 당시 날짜가 주석으로 기록된다.

- banner.js

```javascript
const childProcess = require("child_process");

module.exports = function banner() {
  const date = new Date().toLocaleDateString();
  const user = childProcess.execSync("git config user.name");

  return `빌드날짜: ${date} 작성자 : ${user}`;
};
```

-webpack.config.js

```javascript
const { BannerPlugin } = require("webpack");
const banner = require('./banner.js);

module.exports = {
  plugins: [
    new BannerPlugin(banner),
  ],
};
```

파일이 길어진다면 별도의 js파일로 불러와 import 해도 된다.
