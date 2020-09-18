---
title: "Vanilla JavaScript와 Pure Redux 함께 사용"
excerpt: "NomadCoders 강의 학습 - Redux, React-Redux, Redux toolkit Part1"

categories:
  - TIL
tags:
  - TIL
  - Redux
last_modified_at: 2020-09-18T08:06:00-05:00
---

## Vanilla JavaScript와 Redux 함께 사용

### Vanilla JavaScript Counter with Redux

리덕스가 없다면,

```html
<!-- src/index.html -->
<body>
  <button id="plus">Plus</button>
  <span>0</span>
  <button id="minus">Minus</button>
</body>
```

```jsx
// src/index.js
const plus = document.querySelector("plus");
const minus = document.querySelector("minus");
const number = document.querySelector("number");
let count = 0;

number.innerHTML = count;

function upgradeNumber() {
  number.innerHTML = count;
}

plus.addEventListener('click', function() {
  count = count + 1;
  upgradeNumber();
}
minus.addEventListener('click', function() {
  count = count - 1;
  upgradeNumber();
}

```

각각 클릭 이벤트에 따라 count 변수의 데이터가 변경되는데, 이때 이를 UI에 반영해주는 함수가 필요하다.

리덕스가 있다면,

```html
<!-- src/index.html -->
<!-- Redux CDN으로부터 Redux를 불러옴. -->
<body>
  <button id="plus">Plus</button>
  <span>0</span>
  <button id="minus">Minus</button>
</body>
```

```jsx
// src/index.js
const plus = document.querySelector("plus");
const minus = document.querySelector("minus");
const number = document.querySelector("number");

const ADD = "ADD";
const MINUS = "MINUS"

const reducer = function(count = 0, action) {
  switch(action.type) {
    case ADD:
      return count + 1;
    case SUBTRACT:
      return count - 1;
    default:
      return count;
  }
}

const store = Redux.createStore(reducer);

store.subscribe(function() {
  number.innerText = store.getState()
});

plus.addEventListener('click', store.dispatch({ type: ADD });
minus.addEventListener('click', store.dispatch({ type: MINUS });
```

카운터에서 유일하게 변경되는 데이터는 한 개이며, count이다.
count는 현재 store에 저장되어 있다.

각 버튼을 클릭하면 이벤트 핸들러에 의해 dispatch가 호출된다. dispatch는 reducer를 호출한다.

reducer는 전달받은 action의 type에 따라 store에 저장되어 있는 현재 데이터를 새로운 데이터로 변경한다.

reducer 호출이 끝나면 store의 데이터는 reducer로부터 반환된 새로운 데이터로 변경된다.

새로운 데이터를 UI에 반영하기 위해서는, subscribe의 도움이 있어야 한다. 만약에 store의 변화를 계속해서 알고 싶으면, 그 state가 UI에 반영되는 코드를 subscribe 안에 넣어주면 된다.

### Vanilla JavaScript ToDoList with Pure Redux

```jsx
// src/index.html
<body>
  <h1>Todos</h1>
  <form>
    <input type="text" value="Write to do" />
    <input type="submit" value="Add" />
  </form>
  <ul></ul>
</body>
```

```jsx
// src/index.js
import { createStore } from "redux"

const form = document.querySelector("form");
const input = document.querySelector("input");
const ul = document.querySelector("ul");

// action type
const ADD_TO_DO = "ADD_TO_DO";
const DELETE_TO_DO = "DELETE_TO_DO";

// action creators
const addToDo = (text) => {
  return { type: ADD_TO_DO, text };
}

const deleteToDo = (id) => {
  return { type: DELETE_TO_DO, id };
}

// reducer
const reducer = (state = [], action) {
  switch(action.type) {
    case ADD_TO_DO:
      const newToDoObj = { id: Date.now(), text: action.text };
      return [...state, newToDoObj];
    case DELETE_TO_DO:
      const cleaned = state.filter((toDo) => toDo.id !== action.id);
      return cleaned;
    default:
      return state;
  }
}

// store
const store = createStore(reducer);

// action Dispatcher
const dispatchAddToDo = (text) => {
  store.dispatch(addToDo(text));
}

const dispatchDeleteToDo = (e) => {
  const id = parseInt(e.target.parentNode.id);
  // raw data is string. need to change to number.
  store.dispatch(deleteToDo(id));
}

const paintToDos = () => {
  const toDos = store.getState();
  ul.innerHTML = "";
  // to initialize childNodes of ul.
  toDos.forEach((toDo) => {
    const li = document.createElement("li");
    const btn = document.createElement("button");
    btn.innerText = "DEL";
    btn.addEventListener("click", dispatchDeleteToDo);
    li.id = toDo.id;
    li.innerText = toDo.text;
    li.append(btn);
    ul.appendChild(li);
  })
}

// Every time Redux state changes, paintToDos is invoked.
store.subscribe(paintToDos);

const onSubmit = (e) => {
  e.preventDefault();
  const toDo = input.value;
  input.value = "";
  dispatchAddToDo(toDo);
}

form.addEventListener("submit", onSubmit);
```

## Redux Summary

By using Redux, user can separate data logic with UI. Especially the combination with React and Redux is so powerful because both are able to remarkably decrease the complexity of the project.

### Redux 3 principles

- The global state of your application is stored in an object tree within a single store.
- State is read-only. The only way to change the state is to emit an action, an object describing what happened.
- Changes are made with pure functions.

action must be plain object which has action type.

action is an only way to communicate with reducer.

when the user invokes dispatch, then the reducer is invoked.

dispatch is invoked with action object which contains action type and other useful data about action.

reducer only deal with current state of store and returns an new data. the data always needs to be immutable.

As the data in the redux store is immutable, so user can track all data flows. Even by using Redux devtools, user can time-travel so it is very useful to debug errors.

If you want to listen your change in the store, then you need to subscribe about the state.

Redux data flow is one-way.
