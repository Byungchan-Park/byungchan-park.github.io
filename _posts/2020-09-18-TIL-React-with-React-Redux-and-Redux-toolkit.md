---
title: "코드 리팩토링 with Redux toolkit"
excerpt: "NomadCoders 강의 학습 - Redux, React-Redux, Redux toolkit Part3"

categories:
  - TIL
tags:
  - TIL
  - Redux toolkit
last_modified_at: 2020-09-18T08:06:00-05:00
---

```jsx
// src/store.js
import { createStore } from "redux";

// action type
const ADD = "ADD";
const DELETE = "DELETE";

// action creators: it returns an action object
const addToDo = (text) => {
  return {
    type: ADD,
    text,
  };
};

const deleteToDo = (id) => {
  return {
    type: DELETE,
    id: parseInt(id),
  };
};

// reducer : it changes currentState with dispatched action to a new State.
// return state must be immutable
const reducer = (state = [], action) => {
  switch (action.type) {
    case ADD:
      return state.concat({ text: action.text, id: Date.now() });
    case DELETE:
      return state.filter((toDo) => toDo.id !== action.id);
    default:
      return state;
  }
};

const store = createStore(reducer);

export const actionCreators = {
  addToDo,
  deleteToDo,
};

export default store;
```

다음 코드는 리덕스를 프로젝트에서 사용하기 위하여 필요한 보일러 플레이트 코드들이다. 각각 action type, action creator, reducer, store 등이 있다. 프로젝트에 매번 리덕스를 적용하기 위하여 이 코드들이 필요하다. 필요한 action들이 훨씬 더 많이 늘어난다면 기하급수적으로 코드의 양은 불어날 것이다.

이를 줄이기 위해서 Redux 공식문서에도 나와 있는 **Redux Toolkit**을 사용하면 된다.

먼저 reduxjs/toolkit을 설치한다.

```jsx
// npm
npm install @reduxjs/toolkit
// yarn
yarn add @reduxjs/toolkit
```

configureStore, createAction, createReducer를 사용하면,

```jsx
import { configureStore, createAction, createReducer } from "@reduxjs/toolkit";

const addToDo = createAction("ADD");
const deleteToDo = createAction("DELETE");

const reducer = createReducer([], {
  [addToDo]: (state, action) => {
    state.push({ text: action.payload, id: Date.now() });
  },
  [deleteToDo]: (state, action) => {
    return state.filter((toDo) => toDo.id !== action.payload);
  },
});

export default configureStore(reducer);
// configureStore로 store를 생성하면,
// Redux developer tool를 default로 사용할 수 있게 해준다.
```

```jsx
// 위와 같이 createAction을 사용하면
const addToDo = createAction("ADD");
// { type: "ADD", payload: undefined }를 반환한다.
console.log(addToDo("abc"));
// 이런식으로 addToDo를 호출한다면,
// { type: "ADD", payload: "abc" } 와 같이 기본적으로 값은 payload의 value로 저장된다.
```

위와 같이,
**createReducer**를 사용하면 swtich문을 사용할 필요가 없다.

또한 더이상 불변성을 신경써서 지켜줄 필요가 없다. 그 이유는 Redux toolkit은 내부적으로 Immer.js와 함께 동작하고 있기 때문이다.

기존 데이터를 조작하는 메서드를 사용할 때는 return해 줄 필요가 없다.

새로운 데이터를 반환하는 메서드를 사용할 때는 반드시 return해 주어야 한다.

createSlice를 사용하면,

```jsx
// src/store.js
import { configureStore, createSlice } from "@reduxjs/toolkit";

const toDos = createSlice({
  name: "toDosReducer",
  initialState: [],
  reducers: {
    add: (state, action) => {
      state.push({ text: action.payload, id: Date.now() });
    },
    remove: (state, action) =>
      state.filter((toDo) => toDo.id !== action.payload),
  },
});

export const { add, remove } = toDos.actions;

export default configureStore({ reducer: toDos.reducer });
```
