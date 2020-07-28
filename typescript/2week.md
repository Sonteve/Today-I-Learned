# TypeScript Redux

## 간단한 Redux 설명.

- 리덕스를 쓰는 이유.

```
 프로젝트가 커지고 컴포넌트가 깊어지게 되면 state관리에 어려움이 생길 수 있다.
 하지만 리덕스를 사용하면 컴포넌트들의 상태 관련 로직들을 다른 파일들로 분리시켜서 더욱 효율적으로 관리 할 수 있다.
 오히려 작은 사이즈의 프로젝트라면 contextAPI나 MobX를 사용하는것이 좋다고 한다.
```

- 리덕스에서 사용되는 키워드

## 1.액션

상태에 어떠한 변화가 필요하게 될 땐, 우리는 액션이란 것을 발생시킨다. 이것은, 하나의 객체로 표현되는데, 액션 객체는 다음과 같은 형식으로 이뤄져있다.

```javascript
{
  type: 'INCREASE';
}
```

액션 객체는 type을 필수로 가지고 있어야하고 그 외의 값은 마음대로 넣어줄 수 있다.

```javascript
{
    type: 'INCREASE',
    payload: 3
}
```

payload는 리덕스 관련 여러 라이브러리에서 쓰는convention이다. (다른것으로 해도 상관없다.)

### ex)

```javascript
{
    type: 'INCREASE',
    data: 3
}
```

## 2. 액션 생성함수

위에 설명된 코드(액션)를 만드는 함수이다.
단순 파라미터를 받아와서 객체 형태로 만든다.

```javascript
const increase = (diff) => ({
  type: 'increase',
  payload: diff,
});
// 결과로 {type:'increase', payload: 3}이라는 액션객체를 return한다.
```

## 3. 리듀서

리듀서는 발생한 액션객체를 전달 받아서 새로운 state를 반환한다.

```javascript
// 초기 state값이다. 객체가 아니어도 상관없다.
const initialState = {
  count: 0,
};

const counter = (state = initialState, action) => {
  // 어떤 액션타입인가에 따라 state를 다르게 return한다.
  switch (action.type) {
    case 'increase':
      return {
        count: state.count + action.payload,
      };
    default:
      return state;
  }
};
```

액션을 발생시킨다는것을 dispatch한다고 하는데.

```javascript
dispatch(increase(3));
```

뜻 : store의 count란 변수를 3만큼 증가시킬거야.

설명 : increase라는 액션생성함수에 3이란 값을 넣어서 호출했기 때문에
{type:'increase', payload: 3}이라는 액션객체를 만들고
그 객체를 dispatch한다.(액션을 발생시킨다. ≒ 액션을 dispatch한다.)
액션이 dispatch되면 reducer에게 액션객체가 전달되고
reducer의 switch구문안에서 어떤 타입이냐에 따라 다른 state가 return되고 결국 store의 count라는 변수의 값이 바뀌게 된다.
보통 어떠한 기능을 하냐에 따라 다른 파일로 모듈화한다.

## 구독(subscribe)

action이 dispatch되어 store의 값이 바뀌었을때
그 값을 참조하기위해서 subscribe함수란 redux내장 메서드가 있다.
react에서는 useRecuder나 connet함수가 그 기능을 한다.

```javascript
const count = useSelector((state) => state.count);
```

# TypeScript Redux

간단한 todoList에는 TypeScript redux 는 과하고 많은 라이브러리가 더해졌지만이지만 이번 예제를 하면서 많은 공부가 되었다.
이번 예제에서는 역할별 다른 모듈로 나누고 또 그 모듈 action, type, reducer에 관련된것에 따라 나눌것이다.

- 구조
  ![](https://blogfiles.pstatic.net/20200728_221/home1609_15958698002276vB8g_PNG/ts-redux.png?type=w2)

- 폴더구조

![](https://blogfiles.pstatic.net/20200728_44/home1609_1595870147067d3PMB_PNG/directory_structure.png?type=w2)

# 리덕스 모듈 코드

### modules/todo/actions.ts

```typescript
import { createAction } from 'typesafe-actions';
import { Todo } from './types';

/* 액션에 관련된것이 모여있는 파일 */

// todoList에 필요한 액션 이름 선언.
export const ADD_TODO = 'todo/ADD_TODO';
export const TOGGLE_TODO = 'todo/TOGGLE_TODO';
export const REMOVE_TODO = 'todo/REMOVE_TODO';

// 할일 목록이 추가될때마다 카운트하여 id값으로 들어갈 변수 선언
let nextId = 1;

// 컨테이너 컴포넌트에서 사용될 액션객체 생성함수
export const addTodo = createAction(ADD_TODO, (text: string) => ({
  id: nextId++,
  text,
  done: false,
}))<Todo>();

export const toggleTodo = createAction(TOGGLE_TODO)<number>();
export const removeTodo = createAction(REMOVE_TODO)<number>();
```

### modules/todo/types.ts

```typescript
import * as actions from './actions';
import { ActionType } from 'typesafe-actions';

// type및 interface가 모여있는 파일

// 할일에 대한 interface
export interface Todo {
  id: number;
  text: string;
  done: boolean;
}

// 스토어의 초기상태 interface
export interface TodoState {
  todos: Todo[];
}

// 액션 생성 객체들의 타입
// 액션생성에 관련된 actions.ts의 액션객체 생성 함수들의 return type을 union으로 만들어준다.
export type TodoAction = ActionType<typeof actions>;
```

### modules/todo/reducer.ts

```typescript
import { TodoState, TodoAction } from './types';
import { createReducer } from 'typesafe-actions';
import { ADD_TODO, TOGGLE_TODO, REMOVE_TODO } from './actions';
import produce from 'immer';

// 리듀서 관련 모음 파일

// store의 초기state
const initialState: TodoState = {
  todos: [],
};

// reducer. generic안에 store의 초기state type, action의  type을 넣어준다.
// 첫번째 파라미터로 store의 초기state, 두번째로 해당 액션별 store의 state를 return 하는 구문을 작성.
// 불변성 유지를 쉽게하는 immer 라이브 러리 사용.
const todo = createReducer<TodoState, TodoAction>(initialState, {
  [ADD_TODO]: (
    state,
    { payload: todo } // todo 추가 액션
  ) =>
    produce(state, (draft) => {
      draft.todos = state.todos.concat(todo);
    }),
  [TOGGLE_TODO]: (
    state,
    { payload: id } // todo toggle 줄표시 액션
  ) =>
    produce(state, (draft) => {
      draft.todos = state.todos.map((todo) =>
        todo.id === id ? { ...todo, done: !todo.done } : todo
      );
    }),
  [REMOVE_TODO]: (
    state,
    { payload: id } // todo 지우는 액션
  ) =>
    produce(state, (draft) => {
      draft.todos = state.todos.filter((todo) => todo.id !== id);
    }),
});

export default todo;
```

### modules/index.ts

```typescript
import { combineReducers } from 'redux';
import todo from './todo/reducer';

// combineReducer 로 스토어를 모듈들을 합쳐준다.
const rootReducer = combineReducers({
  todo,
});
// ReturnType<typeof ~~~>는 함수의 return값을 추론해준다.
// useSelector에서 타입으로 사용하기위해 rootReducer의 리턴타입을 export 해준다.
export type RootReducer = ReturnType<typeof rootReducer>;
export default rootReducer;
```

# container 컴포넌트

### containers/TodoContainer.tsx

```typescript
import React, { useCallback } from 'react';
import TodoList from '../components/TodoList';
import TodoInsert from '../components/TodoInsert';
import { useSelector, useDispatch } from 'react-redux';
import { RootReducer } from '../modules/index';
import { addTodo, toggleTodo, removeTodo } from '../modules/todo/actions';
import styled from 'styled-components';

// 스토어와 연결되어있는 todo 최상위 컴포넌트
// 해당 컴포넌트 내부에서 선언한 Styled컴포넌트와 다른파일에서 import해온 컴포넌트를 구분하기 위하여
// 해당컴포넌트 내부에서 선언한 Styled컴포넌트에 S_표시.
// useCallback으로 함수 재사용 최적화.

function TodoContainer() {
  const todos = useSelector(({ todo }: RootReducer) => todo.todos);
  const dispatch = useDispatch();
  // 등록버튼 클릭시 todoList에 새로운 todo추가 액션을 dispatch 하는 함수.
  const handleSubmit = useCallback(
    (text: string) => {
      dispatch(addTodo(text));
    },
    [dispatch]
  );
  // todo 클릭시 toggle 줄표시 액션을 dispatch 하는 함수.
  const handleToggle = useCallback(
    (id: number) => {
      dispatch(toggleTodo(id));
    },
    [dispatch]
  );
  // X버튼 클릭시 해당 todo 삭제하는 액션을 dispatch 하는 함수.
  const handleRemove = useCallback(
    (id: number) => {
      dispatch(removeTodo(id));
    },
    [dispatch]
  );
  return (
    <S_TodoContainerBlock>
      <TodoInsert handleSubmit={handleSubmit} />
      <TodoList
        todos={todos}
        handleToggle={handleToggle}
        handleRemove={handleRemove}
      />
    </S_TodoContainerBlock>
  );
}

export default TodoContainer;

const S_TodoContainerBlock = styled.div`
  display: inline-block;
`;
```

# presentational 컴포넌트

### components/TodoInsert.tsx

```typescript
import React, { useState, ChangeEvent, FormEvent, useRef } from 'react';

// props로 받는 것에 대한 interface
interface TodoInsertProps {
  handleSubmit: (text: string) => void;
}

function TodoInsert({ handleSubmit }: TodoInsertProps) {
  // 컴포넌트 내부에서 input값 핸들링을 위한 useState
  const [value, setValue] = useState<string>('');
  // dom조작으로 focus()를 하기 위한 useRef
  const inputRef = useRef<HTMLInputElement>(null);

  // 등록 버튼 클릭시 상위 컨테이너 컴포넌트로 input의 value값을 올려준다.
  // 그리고 input을 비우고 focus한다.
  const onSubmit = (e: FormEvent<HTMLFormElement>) => {
    e.preventDefault();
    handleSubmit(value);
    setValue('');
    inputRef.current && inputRef.current.focus();
  };
  // input value핸들링 하기위한 메서드.
  const onChange = (e: ChangeEvent<HTMLInputElement>) => {
    setValue(e.target.value);
  };
  return (
    <form onSubmit={onSubmit}>
      <input
        ref={inputRef}
        placeholder="할 일을 입력하세요."
        value={value}
        onChange={onChange}
      />
      <button type="submit">등록</button>
    </form>
  );
}

export default TodoInsert;
```

### components/TodoList.tsx

```typescript
import React from 'react';
import TodoItem from './TodoItem';
import { Todo } from '../modules/todo/types';

// 아이템들을 map으로 뿌려주는 래핑 컴포넌트

// props로 받는 것에 대한 interface
interface TodoListProps {
  todos: Todo[];
  handleToggle: (id: number) => void;
  handleRemove: (id: number) => void;
}

function TodoList({ todos, handleToggle, handleRemove }: TodoListProps) {
  if (todos.length === 0) return <p>등록된 항목이 없습니다.</p>;

  return (
    <ul>
      {todos.map((todo) => (
        <TodoItem
          key={todo.id}
          todo={todo}
          handleToggle={handleToggle}
          handleRemove={handleRemove}
        />
      ))}
    </ul>
  );
}

export default TodoList;
```

### components/TodoItem.tsx

```typescript
import React from 'react';
import { Todo } from '../modules/todo/types';
import styled from 'styled-components';

// todo 각각인 최 하위 컴포넌트
// React.memo로 이전 state와 비교하여 같으면 리렌더링하지않는다.

// props로 받는 것에 대한 interface
interface TodoItemProps {
  todo: Todo;
  handleToggle: (id: number) => void;
  handleRemove: (id: number) => void;
}

// StyledComponent의 props interface
interface SCprops {
  toggle?: string;
}

function TodoItem({ todo, handleToggle, handleRemove }: TodoItemProps) {
  console.log('todo.id', todo.id); // 리렌더링 확인 로그
  return (
    <S_TodoItemBlock>
      <S_Toggle
        toggle={todo.done ? 'line-through' : 'none'}
        onClick={() => handleToggle(todo.id)}
      >
        {todo.text}
      </S_Toggle>
      <S_Remove onClick={() => handleRemove(todo.id)}>(X)</S_Remove>
    </S_TodoItemBlock>
  );
}

export default React.memo(TodoItem);

const S_TodoItemBlock = styled.li`
  display: flex;
  justify-content: space-between;
`;

const S_Toggle = styled.span<SCprops>`
  text-decoration: ${(props) => props.toggle};
`;

const S_Remove = styled.span`
  color: red;
`;
```
