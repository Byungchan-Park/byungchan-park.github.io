---
title: "freecodecamp 자바스크립트 디버깅 학습 정리"
excerpt: "자바스크립트 디버깅"

categories:
  - TIL
  - JavaScript
tags:
  - TIL
  - JavaScript
last_modified_at: 2020-11-05T08:06:00-05:00
---

## console.log()와 typeof 메소드

console.log( ) 와 typeof 메소드는 각각 중간값과 프로그램 출력값의 자료형을 검사할 수 있는 대표적인 디버깅 방법이다.

typeof 메소드를 사용할 때 주의할 점이 있다.

자바스크립트에서 배열은 객체이다.

```javascript
console.log(typeof []);
// outputs : "object
```

## Single Quotes('')와 Double Quotes("")를 혼용해서 사용할 때 주의하자.

자바스크립트는 문자열을 선언할 때 single quotes와 double quotes의 사용을 허용한다. 때로는 문자열 안에 또다른 문자열이 들어가는 경우가 있다. 이때 single quotes와 double quotes를 섞어서 사용하거나 둘 중 하나만 사용했을 때는 내부의 또다른 문자열을 감싸는 quotes의 앞에 각각 이스케이프 문자(\)를 붙여준다.

여기 몇 가지 예시를 살펴보자.

```javascript
// These are correct:
const grouchoContraction = "I've had a perfectly wonderful evening, but this wasn't it.";
const quoteInString = "Groucho Marx once said 'Quote me as saying I was mis-quoted.'";
// This is incorrect:
const uhOhGroucho = 'I've had a perfectly wonderful evening, but this wasn't it.';

// Correct use of same quotes:
const allSameQuotes = 'I\'ve had a perfectly wonderful evening, but this wasn\'t it.';
```

## =, ==, ===의 차이

= : 할당 연산자

== : 일치 연산자이며 비교 대상의 자료형에 상관없이 값의 일치만을 검사한다.

=== : 일치 연산자이며 값과 비교 대상의 자료형 또한 일치 여부를 검사한다.

```javascript
let x = 1;
let y = 2;
if (x = y) {
  ...
} else {
  ...
}
```

위의 코드에서 if의 조건문에서 좌변과 우변의 값을 서로 비교하는 것이 아닌 x에 2라는 값이 저장되고 if 문의 본문이 실행된다.

좌변과 우변의 값을 서로 비교하고 싶다면 다음과 같이 작성해야 한다.

```javascript
let x = 1;
let y = 2;
if (x == y) {
  ...
} else {
  ...
}
```

이 코드는 if 조건문에서 x와 y의 일치여부를 확인하는데 false이므로 else의 본문이 실행된다.

인자가 없는 함수를 호출할 때 ( ) 를 함수 이름 뒤에 붙이는 것을 잊어버리지 말자.

```javascript
function myFunction() {
  return "You rock!";
}
let varOne = myFunction; // varOne 변수에는 함수의 참조가 저장된다.
let varTwo = myFunction(); // varTwo 변수에는 함수가 호출되면서 반환되는 "You rock!"이 저장된다.
```

## Off by one errors(OBOE) 에러

이 에러는 배열에서 존재하지 않는 인덱스에 접근하였을 때 출력된다. 자바스크립트의 배열에서 요소의 인덱스 번호는 0부터 시작하며, 마지막 인덱스 번호는 배열의 길이 - 1 값이다.

만약 배열의 길이 값을 가지는 인덱스에 접근한다면, 프로그램은 "index out of range" reference error 메세지를 출력하거나 undefined를 출력할 것이다.

배열, 문자열의 각 요소들에 루프를 통해 접근할 때 접근 범위 설정에 주의할 필요가 있다.

```javascript
function countToFive() {
  let firstFive = "12345";
  let len = firstFive.length;

  for (let i = 1; i <= len; i++) {
    console.log(firstFive[i]);
  }
}

countToFive();
```

for loop의 마지막 순서에서 배열의 길이값인 인덱스에 접근하므로 "index out of range" reference error 메세지가 콘솔창에 출력될 것이다.

```javascript
function countToFive() {
  let firstFive = "12345";
  let len = firstFive.length;
  // 배열의 길이값인 인덱스는 배열 안에 존재하지 않는다.
  for (let i = 1; i < len; i++) {
    console.log(firstFive[i]);
  }
}

countToFive();
```

## 루프 내부에서 변수를 재설정해야 하는 경우

```javascript
// 각각 m은 행, n은 열을 의미한다.
// 모든 요소들이 0인 이차원 배열을 생성한다.
function zeroArray(m, n) {
  let newArray = [];
  let row = [];

  for (let i = 0; i < m; i++) {
    for (let j = 0; j < n; j++) {
      row.push(0);
    }
    newArray.push(row);
    // 다음 행에 접근할 때는 row가 빈 배열 상태여야 한다.
    // row를 빈 배열로 재초기화해준다.
    row = [];
  }
  return newArray;
}

let matrix = zeroArray(3, 2);
console.log(matrix);
// outputs: [[0,0],[0,0],[0,0]]
```

## 무한 루프 내부에서 반드시 종결 조건을 꼭 넣어주어라!

무한 루프는 브라우저의 동작을 망가뜨리고 아무도 원하지 않는 프로그램 실행에 손상을 일으킬 수 있다. 그러므로 반드시 무한 루프 내부에는 종결 조건을 설정해주는 것이 필요하다.

```javascript
function myFunc() {
  // i 값은 홀수부터 시작하며 2씩 증가하므로 i != 4라는 조건에 대해 항상 true이다.
  // 그러므로 무한루프를 돌게 된다.
  for (let i = 1; i != 4; i += 2) {
    console.log("Still going!");
  }
}

function myFunc() {
  // 반드시 false인 경우가 존재하도록 종결 조건을 설정해준다.
  for (let i = 1; i <= 4; i++) {
    console.log("Still going!");
  }
}
```
