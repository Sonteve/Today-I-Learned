새로운 프로젝트를 시작하기전 사용할만한 리소스를 찾고있던 중에

라우팅시 화면 전체가 부드럽게 나타나게 할수는 없을까 하다가

`react-transition-group`이라는 라이브러리를 발견했고 사용법에 대해 기록하려고한다.

nextjs에서 기본적으로 pages directory안의 page component들은 \_app.tsx에 공통적으로 wrapping된다.
라우팅시 페이지컴포넌트가 바뀌기 때문에 \_app.tsx컴포넌트안에 들어오는 page컴포넌트인 `<Component/>` 컴포넌트를 우리가 만든 AppLayout이라는 컴포넌트로 감싸주고 이 컴포넌트안에서 페이지 전환 애니메이션 처리를 하려고 한다.

> \_app.tsx

```javascript
import AppLayout from "../components/AppLayout";

const App = ({ Component }) => {
  return (
    <AppLayout>
      <Component />
    </AppLayout>
  );
};
export default App;
```

`_app.tsx` 컴포넌트의`<Component/>` 를 공통 레이아웃인 `AppLayout`으로 감싸준다.

> AppLayout.tsx

```javascript
import React, { ReactNode } from "react";
import Link from "next/link";
import { Transition, TransitionGroup } from "react-transition-group";
import { useRouter } from "next/router";
interface Props {
  children: ReactNode;
}
const TIMEOUT = 100;
const getTransitionStyles = {
  entering: {
    position: `absolute`,
    opacity: 0,
  },
  entered: {
    transition: `opacity ${TIMEOUT}ms ease-in-out, transform ${TIMEOUT}ms ease-in-out`,
    opacity: 1,
  },
  exiting: {
    transition: `opacity ${TIMEOUT}ms ease-in-out, transform ${TIMEOUT}ms ease-in-out`,
    opacity: 0,
  },
};
const AppLayout = ({ children }: Props) => {
  const router = useRouter();
  return (
    <>
      <div>
        <Link href="/">
          <a>HOME</a>
        </Link>
        <Link href="/mypage">
          <a>MY_PAGE</a>
        </Link>
        <Link href="/contact">
          <a>CONTACT</a>
        </Link>
      </div>
      <TransitionGroup style={{ position: "relative" }}>
        <Transition
          key={router.pathname}
          timeout={{
            enter: TIMEOUT,
            exit: TIMEOUT,
          }}
        >
          {(status) => (
            <div
              style={{
                ...getTransitionStyles[status],
              }}
            >
              {children}
            </div>
          )}
        </Transition>
      </TransitionGroup>
    </>
  );
};

export default AppLayout;
```

TransitionGroup과 Transition으로 감싸준다. `<Transition/>`의
key값으로 현재 url의 pathname을 넘겨주면
페이지가 전환될 때 이전 페이지가 TransitionGroup에서 빠지고
새 페이지가 들어온 것으로 간주하고 애니메이션을 실행한다.
timeout에 들어가는 객체값은 enter,exit가 있는데 각각
렌더되는데 걸리는 시간, 화면에서 사라지는데 걸리는 시간이다.
그리고 컴포넌트 내부에서 string datatype의 status란 상태값을 얻는데
`unmounted`, `exited`, `entering`, `entered`, `exiting` 가 있고

나는 fadein fadeout을 하고싶기때문에
entering: 렌더 되기전 상태
enterted: 렌더 후 상태
exiting: 화면에서 사리질 때 이다.

```javascript
const getTransitionStyles = {
  entering: {
    position: `absolute`,
    opacity: 0,
    /* transform: `translateX(50px)`, */
  },
  entered: {
    transition: `opacity ${TIMEOUT}ms ease-in-out, transform ${TIMEOUT}ms ease-in-out`,
    opacity: 1,
    /* transform: `translateX(0px)`, */
  },
  exiting: {
    transition: `opacity ${TIMEOUT}ms ease-in-out, transform ${TIMEOUT}ms ease-in-out`,
    opacity: 0,
    /* transform: `translateX(-50px)`, */
  },
};
```

transition 및 opacity등 애니메이션효과로 들어갈 css 속성값을 가지는 객체를 만든다.

`<Transition/>`컴포넌트 안에서 status값을 받아서 그 값에 따라 getTransitionStyles에서 다른 css객체를 얻어오고 전개연산자로 css 속성을 넣어준다.

```javascript
{
  (status) => (
    <div
      style={{
        ...getTransitionStyles[status],
      }}
    >
      {children}
    </div>
  );
}
```
