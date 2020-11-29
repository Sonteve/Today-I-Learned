자바스크립트의 모든 객체는 부모 역할을 하는 객체와 연결되어 있다.

객체지향 언어에서 주로 사용하는 class와 instance 같이 말이다.

이런 부모역할을 하는 객체를 프로토타입객체라고 부른다.

브라우저에 간단한 테스트 코드를 작성해보자.

```javascript
const sonteve = {
  name: "sonteve",
};
console.log(sonteve.toString()); // [object Object]
console.dir(sonteve);
```

![](https://images.velog.io/images/sds11609/post/0478a422-523d-489c-91ea-45070d4fb82e/1.png)

우리는 어떻게 선언하지않은 toString()이란 메서드를 사용 할 수 있었을까?

우리가 생성한 name과 따로 생성하지 않은 \_**\_proto\_\_** 라는 속성이있다.
\_**\_proto\_\_**를 보면 어떤 객체의 프로퍼티가 나와있고 내가 만든 프로퍼티처럼 직접 사용도 가능하다.
**proto**가 가리키는 대상은 모든객체의 부모격인 Object.prototype 객체이다.
sonteve객체가 Object.prototype객체의 프로퍼티들을 상속받기때문에
객체 내부에 없는 메서드를 sonteve.toString() 이런식으로 호출이 가능했던 것이다.

모든 객체의 프로토타입은 객체를 생성할때 결정된다.
sonteve라는 객체는 객체 리터럴 방식({})으로 생성된것이며
위에서 생성한 sonteve라는 객체의 \_**\_proto\_\_** 속성은 Object.prototype이라는 프로토타입객체를 가리킨다.
