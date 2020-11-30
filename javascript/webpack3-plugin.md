## webpack plugin

앞전에서 본 loader는

webpack이 번들링 하는 시점에 관여하여 사용자가 지정한 특정 확장자를 필터링하여 원하는 작업을 처리하는데 사용되었다.
loader가 번들링중에 특정 확장자를 가진 모든파일에 대해 관여했다면 plugin인은 webpack이 번들링한 합쳐진 결과물에 작용한다.
번들링된 파일에 해당 빌드 당시 시간과 날짜등을 기록하거나 ,난독화하거나, 특정 텍스트를 추출하는 용도로 사용한다.

자주 사용하는 플러그인을 보면
custom plugin은 class형식으로 만들어야하고
webpack.config.js의 plugins프로퍼티에 value값으로 []안에 new CustomPlugin 식으로 인스턴스를 넣어주면 된다.

```javascript
const MyPlugin = require("./myplugin");

module.exports = {
  plugins: [new CustomPlugin()],
};
```
