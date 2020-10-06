---
title: "Node - Express [Router 객체로 라우팅 분리]"
excerpt: "효율적인 코드 관리를 위하여 라우터를 별도로 분리하게 도와주는 Router 객체"

categories:
  - TIL
  - Express
tags:
  - TIL
  - Router
last_modified_at: 2020-10-05T08:06:00-05:00
---

익스프레스를 사용하지 않았다면 라우터를 만들 때 요청 메서드와 주소별로 분기 처리를 하느라 코드가 매우 복잡했을 것이다.

if문으로 분기해 코딩하여 보기에도 좋지 않고 확장하기도 어렵다.

익스프레스를 사용하는 이유가 바로 라우팅을 깔끔하게 관리할 수 있다는 점 때문이다.

하지만 익스프레스만 사용하더라도 라우터들이 점점 많아지게 되면 이또한 코드 관리하기가 힘들어진다.

이때 **코드 관리를 위해 Router 객체를 사용하여 라우터들을 별도로 분리할 수 있다.**

만약, user, favorite에 관한 각각의 라우트들이 존재한다면, routers 폴더를 생성하고 user.js, favorite.js 파일 안에 각각에 관한 라우터들을 작성하여 라우터 분리가 가능해진다. 이렇게 하면 코드 관리가 훨씬 수월해질 것이다.

```jsx
// index.js
const express = require('express');
const app = express();

app.use('/api/users', require('./routes/users'));
app.use('/api/favorite', require('./routes/favorite'));

// users.js
const express = require('express');
const router = express.Router();

router.get('/register', (req, res) => { ... });

// favorite.js
const express = require('express');
const router = express.Router();

router.get('/favoriteNumber', (req,res) => { ... });
```

회원가입 과정에서 API 통신을 할 경우 다음 endpoint로 GET 요청을 보낸다.

```jsx
const endpoint = "http://localhost:5000/api/users/register";
```

서버는 우선 다음 엔드포인트의 존재 여부를 판단한다.

서버의 라우터 중에서 /users url이 일치하는 것이 있는데, index.js에 있는 /users 와 user.js에 있는 /register이 합쳐져 /users/register가 다음 엔드포인트와 일치한다고 판단한 후 이 라우터의 콜백 함수가 실행된다.

위의 코드에서는 각각 라우터 하나에 하나의 미들웨어가 장착되어 있지만 **미들웨어를 여러 개 장착할 수 있다.**

실제 라우터 로직이 실행되는 미들웨어 전에 로그인 여부 또는 관리자 여부를 체크하는 미들웨어를 중간에 넣어두곤 한다.

```jsx
router.get("/", middleware1, middleware2, middleware3);
// router.get('/')이면 / 주소로 GET 요청을 하는 것과 같다.
```

라우터에서는 반드시 요청에 대한 응답을 보내거나 에러 핸들러로 요청을 넘겨야 한다.

응답을 보내지 않으면 브라우저는 계속 응답을 기다린다. 응답에 제한 시간이 있으므로 영원히 기다리지는 않지만, 기다리는 동안 다른 동작을 수행할 수 없다. res 객체에 들어 있는 메서드들을 이용하여 응답을 보낸다.

next함수에는 라우터에서만 동작하는 특수 기능이 있다. **next('route')**이다. **라우터에 연결된 나머지 미들웨어를 건더뛰고 싶을 때 사용한다.**

```jsx
router.get(
  "/",
  function (req, res, next) {
    next("route");
  },
  function (req, res, next) {
    console.log("실행되지 않았습니다.");
  },
  function (req, res, next) {
    next();
  }
);

router.get("/", function (req, res) {
  console.log("실행됩니다.");
  res.status(200).json({ success: false });
});
```

같은 주소의 라우터를 2개 만들었다. 첫 번째 라우터의 첫 번째 미들웨어에서 next('route')를 호출하자 두 번째, 세 번째 미들웨어는 실행되지 않는다.

대신 주소와 일치하는 다음 라우터로 넘어간다.
