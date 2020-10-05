---
title: "동적 라우팅"
excerpt: "클라이언트에서 라우팅 기능을 담당하는 React Router와 유동 라우터"

categories:
  - TIL
  - React
tags:
  - TIL
  - Routing
  - React Router
last_modified_at: 2020-10-05T08:06:00-05:00
---

```jsx
// Routes.js
import React from "react";
import { BrowserRouter as Router, Route, Switch } from "react-router-dom";
import Home from "./Pages/Home";
import List from "./Pages/List";
import Detail from "./Pages/Detail";

const Routes = () => {
  return (
    <Router>
      <Switch>
        <Route exact path="/" component={Home} />
        <Route exact path="/list" component={List} />
        <Route exact path="/detail" component={Detail} />
      </Switch>
    </Router>
  );
};
```

위의 코드를 보면 현재 리스트 페이지와 상세 페이지가 각각 컴포넌트로 구현되어 있고, url path로 /list 혹은 /detail 설정 시 해당 컴포넌트로 이동하도록 되어 있다.

하지만, 상세 페이지로 이동할 경우 상세 페이지가 화면에 렌더링되겠지만 어떤 상품에 대한 상세 페이지인지 알 수 있는 방법이 없다.

이때 유동 라우터를 이용하면 어떤 상품의 상세 페이지인지 알 수 있다.

동적 라우팅이란 위와 같이 라우트의 경로(path)에 특정 값을 넣어 해당 페이지로 이동할 수 있게 하는 것을 말한다.

React 프로젝트에서는 라우팅 기능을 구현할 때 흔히 <strong>React Router</strong>를 사용한다.

React Router에서는 두 가지 방법을 통해 유동 라우팅 기능을 구현할 수 있다.

<ol>
  <li>Query parameters</li>
  <li>URL parameters</li>
</ol>

라우트로 설정한 컴포넌트는 다음 3가지의 props를 전달받게 된다.

<ol>
  <li>
    history
    <ul>
      <li>이 객체의 프로퍼티인 push, replace 를 통해 다른 경로로 이동하거나 앞뒤 페이지로 전환할 수 있다.</li>
      <li>다른 컴포넌트로 이동(페이지 전환) 시에 컴포넌트 내에서 흔히 this.props.push('/page') 이렇게 사용한다.</li>
    </ul>
  </li>
  <li>
    location
    <ul>
      <li>이 객체는 현재 경로에 대한 정보를 지니고 있고, URL 쿼리 (/movie_list?page=1)에 대한 정보도 가지고 있다.</li>
    </ul>
  </li>
  <li>
    match
    <ul>
      <li>이 객체에는 어떤 라우트에 매칭이 되었는지에 대한 정보가 있고 params(/movie_detail/:id) 정보를 가지고 있다.</li>
    </ul>
  </li>
</ol>

주의) URL 쿼리의 경우에는 컴포넌트 내에서 동적으로 사용할 수 있고, params의 경우에는 사용하기 전에 꼭 라우트에서 지정해주어야 한다.

URL Parameters Example)

```jsx
// Routes.js - MovieDetailPage 라우트 설정
<Route exact path="/movie_detail/:id" component={MovieDetailPage} />

// MovieItem.js - List 페이지에서 영화 이미지 클릭 시 상세 페이지 이동 경로 설정
<Link to={`/movie_detail/${this.props.movieId}`}></Link>

// 상세 페이지 진입 시 API 호출 주소
fetch(`https://api.themoviedb.org/3/movie/${this.props.match.params.id}?api_key=${API_KEY}&language=en-US`)
```

List 페이지에서 영화 이미지 클릭 시 해당 영화의 movieId를 포함한 url 주소로 이동한다.

MovieDetailPage에서는 url 주소에 담긴 movieId를 <strong>this.props.match.params.id</strong>를 통해 접근할 수 있다.

접근해서 얻은 movieID를 통해 API를 호출하여 해당 데이터롤 얻고 화면에 나타낸다.

Query Parameters Example)

```jsx
// NavBar.js
// search Input에서 검색어를 입력하고 검색 버튼을 누르거나 Enter 키를 치면 submit 이벤트 발생
const handleSubmit = e => {
  e.preventDefault();
  // 뒤의 query String을 포함한 /search/movie의 url 주소로 이동한다.
  props.history.push(`/search/movie/?query=${inputVal}`);
}

// SearchResult.js
function SearchResult(props) {
  // inputVal이 abc라면, expected output: ?query=abc
  console.log(props.location.search);
  // keyword => abc
  const keyword = props.location.search.split('=')[1];

  useEffect(() => {
    fetch(`https://api.themoviedb.org/3/movie/search/movie?api_key=${API_KEY}&query=abc`)
      .then(response => response.json())
      .then(response => response.results)
      .catch(err => console.log(err));
  }, []);
  return (...)
}
```

NavBar의 검색창에 검색어를 입력한 후 엔터를 치면 검색 결과를 보여주는 페이지가 화면에 렌더링된다. 이때, 검색어를 토대로 API통신을 통해 검색어가 들어가 있는 영화 제목들을 전부 결과값으로 가져온다.

API통신을 할 때 endpoint에서 우리가 필요한 것은 query parameters 중에서 = 뒤에 있는 'abc'이다. 먼저 query parameters 값을 가져오기 위해 <strong>props.location.search</strong>에 접근하였다. 이 값을 split 메서드를 사용하여 '=' 을 기준으로 문자열을 나누어 두 개의 요소를 가진 배열이 반환된다.

['?query', 'abc'] 이 중에서 내가 원하는 요소는 index: 1에 해당하는 요소이다. 따라서 두 번째 요소를 이용하여 API통신을 하였다.

Query Parameters를 사용하는 것의 장점은 route의 path를 수정해줄 필요가 없다는 것이다.

## Summary

- SPA(Single Page Application)을 구축하기 위해서는 라우팅 기능이 필요한데, 클라이언트에서 React Router 라이브러리를 사용하여 해당 기능을 구현할 수 있다.
- 유동라우터란 라우트의 경로에 특정 값을 넣어 해당 페이지로 이동할 수 있게 하는 것인데, React Router에서는 두 가지 방법을 통해 유동 라우팅 기능을 구현할 수 있다.

      1) Query String 2) URL Parameters

- 클라이언트의 유동 라우팅 기능은 백엔드의 API 통신과는 상관이 없다. 오로지 클라이언트의 서로 다른 페이지 이동 시 서로 소통하기 위한 것이다. API 호출 시에는 url 주소에서 필요한 데이터만 골라내 API 호출 시 사용하는 것이다.
- 라우터로 설정한 컴포넌트들은 3가지의 Props(history, location, match)를 기본적으로 전달받는다.
- params를 사용하려면 this.props.match.params.PARAM_NAME 을 이용해야 한다.
- query를 사용하려면 this.props.location.search 를 이용해야 한다.
