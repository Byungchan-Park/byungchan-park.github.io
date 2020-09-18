---
title: "React와 React-Redux 함께 사용"
excerpt: "NomadCoders 강의 학습 - Redux, React-Redux, Redux toolkit Part2"

categories:
  - TIL
tags:
  - TIL
  - React-Redux
last_modified_at: 2020-09-18T08:06:00-05:00
---

## React와 Redux 함께 사용(React and React-Redux)

### ToDoList with React and React-Redux

```jsx
// src/index.html
<body>
  <div id="root"></div>
</body>
```

```jsx
// src/index.js
import React from "react";
import ReactDOM from "react-dom";
import { Provider } from "react-redux";
import App from "./components/App";
import store from "./store";

ReactDOM.render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById("root")
);
```

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

```jsx
// src/components/App.js
import React from "react";
import { HashRouter as Router, Route } from "react-router-dom";
import Detail from "../routes/Detail";
import Home from "../routes/Home";

function App() {
  return (
    <Router>
      <Route path="/" exact component={Home} />
      <Route path="/:id" component={Detail} />
    </Router>
  );
}

export default App;
```

```jsx
// src/page/Home.js
import React, { useState } from "react";
import { connect } from "react-redux";
import ToDo from "../components/ToDo";
import { actionCreators } from "../store";

function Home({ toDos, addToDo }) {
  const [text, setText] = useState("");
  function onChange(e) {
    setText(e.target.value);
  }
  function onSubmit(e) {
    e.preventDefault();
    addToDo(text);
    setText("");
  }
  return (
    <>
      <h1>To Do</h1>
      <form onSubmit={onSubmit}>
        <input type="text" value={text} onChange={onChange} />
        <button>Add</button>
      </form>
      <ul>
        {toDos?.map((toDo) => (
          <ToDo key={toDo.id} {...toDo} />
        ))}
      </ul>
    </>
  );
}

// literally get current Redux state from Redux store to Component
// it is so-called mapStateToProps
// first argument : current state of Redux store
// second argument : Component's own props
function mapStateToProps(state) {
  return { toDos: state };
}

function mapDispatchToProps(dispatch) {
  return {
    addToDo: (text) => dispatch(actionCreators.addToDo(text)),
  };
}

export default connect(mapStateToProps, mapDispatchToProps)(Home);
```

위의 Home 컴포넌트는 현재 React-Redux가 적용되어 있다.

만약 React-Redux가 적용되어 있지 않고 Pure Redux만으로 코드를 짜야 한다면 어떠한 일이 일어날까?

예를 들면 아래와 같이 Home 컴포넌트의 재사용성을 위해 Container 컴포넌트와 Presentational 컴포넌트를 분리하여 짤 것이고, Home 컴포넌트의 재사용성을 고려하지 않으면 Home 컴포넌트 내부에는 Redux와 관련된 로직과 UI와 관련된 로직이 혼재되어 나타날 것이다.

```jsx
import React, { useState } from 'react';
import store, { actionCreators } "../store";

function HomeContainer() {
  const [toDos, setToDos] = useState(store.getState());
  store.subscribe(function() {
    setToDos(store.getState());
  });
  const addToDo = function(text) {
    dispatch(actionCreators.addToDo(text));
  }
  return <Home toDos={toDos} addToDo={addToDo} />
}

function Home({ toDos, addToDo }) {
  const [text, setText] = useState("")
  function onChange(e) {
    setText(e.target.value)
  }
  function onSubmit(e) {
    e.preventDefault()
    addToDo(text)
    setText("")
  }
  return (
    <>
      <h1>To Do</h1>
      <form onSubmit={onSubmit}>
        <input type="text" value={text} onChange={onChange} />
        <button>Add</button>
      </form>
      <ul>
        {toDos?.map((toDo) => (
          <ToDo key={toDo.id} {...toDo} />
        ))}
      </ul>
    </>
  )
}

```

이러한 번거로움을 connect 함수가 해결해준다.

connect 함수가 내부적인 동작을 거쳐 Redux의 State를 컴포넌트에게 props의 형태로 전달해준다.

```jsx
connect(mapStateToProps, mapDispatchToProps)(WrappedComponent);
```

첫번째 괄호 안에서,

mapStateToProps, mapDispatchToProps 각각은 함수이다.

```jsx
const mapStateToProps = (state, ownProps) => {
  return { [propsName]: state };
}
// 여기서 state는 현재 Redux store의 state이다.
// ownProps는 Wrapping되는 Component의 props이다.

const mapDispatchToProps = (dispatch, ownProps) => {
  return { [propsName]: (dispatch) => dispatch([action object]) };
}
// dispatch된 함수를 정의해서 해당되는 props를 호출하면 손쉽게 action을 dispatch할 수 있다.
```

```jsx
// src/components/ToDo.js
import React from "react";
import { connect } from "react-redux";
import { Link } from "react-router-dom";
import { actionCreators } from "../store";

function ToDo({ id, text, onBtnClick }) {
  return (
    <li>
      {text}
      <button onClick={onBtnClick}>DEL</button>
    </li>
  );
}

function mapDispatchToProps(dispatch, ownProps) {
  return {
    onBtnClick: () => dispatch(actionCreators.deleteToDo(ownProps.id)),
  };
}
// This is the combination with dispatch and ownProps.
// ToDo component already have id value from props. so we can use by using the argument ownProps.

export default connect(null, mapDispatchToProps)(ToDo);
```

다음 코드와 같이 컴포넌트에 state를 전달할 필요가 없다면 null을 넣어주면 된다. 그리고, action을 dispatch하는 onBtnClick 함수를 보면 이미 props로 전달받은 id값이 있기 때문에 ownProps.id를 사용하면 해당 값을 사용할 수 있다. 여기서 ownProps는 ToDo 컴포넌트가 부모 컴포넌트로부터 받은 props 값을 의미한다.
