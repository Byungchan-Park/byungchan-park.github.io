---
title: "개미수열"
excerpt: "개미수열"

categories:
  - TIL
  - Algorithm
tags:
  - TIL
  - Algorithm
last_modified_at: 2020-10-23T08:06:00-05:00
---

## 개미수열이란?

개미수열은 다음과 같은 수열이다. (이 수열은 베르나르 베르베르의 소설 개미에서 소개되었기 때문에 개미 수열이라고 불린다.)

1, 11, 12, 1121, 122111 ... 이 수열은 앞의 수의 연속된 같은 숫자를 묶어서 숫자와 그 개수를 읽는 방식으로 만들어진다. 1을 1이 한 개 혹은 11로 읽는다. 11을 1이 두 개 혹은 12로 읽는다. 12를 1이 한 개, 2가 한 개 혹은 1121로 읽는다. 1121을 1이 두 개, 2가 한 개, 1이 한 개 혹은 122111로 읽는다.

이와 같은 방법으로 계속해서 다음 수를 만들어 간다. 입력으로 n이 주어질 때 n번째 개미 수열을 문자열 형태로 return해라.

### 내가 작성한 의사코드

입력 값 : n(몇 번째 수열?)

결과 값 : n번째 수열(문자열)

i : 몇 번 진행될 것인가?

각 단계에서,

j : 모든 요소의 번호를 가리킴. count : 동일한 숫자의 개수. currentTarget : 현재 비교 대상이 되는 값

currentStr을 1로 초기화해준 채 시작한다.

각 단계를 진행할 때, 처음에 currentTarget을 첫 번째 요소의 값으로, count는 1로 이미 첫 번째 요소의 개수를 반영한 채 시작된다.

현재 비교 대상인 값과 현재 요소의 값이 일치할 경우, count가 1 증가한다.

일치하지 않을 경우, 이전까지 저장되어 있던 비교 대상의 값과 count를 차례로 output에 저장해두고, 비교 대상을 현재 요소로, 그리고 count는 현재 요소의 개수를 반영하여 1로 초기화한다.

마지막 요소까지 비교가 끝난 이후 마지막 단계에서 저장되어 있던 비교 대상의 값과 count를 차례로 output에 저장한 후 이를 최종 결과값으로 currentStr에 저장하고, output은 다시 공백으로 초기화시킨다.

이 과정이 사용자가 입력한 n번만큼 진행될 것이고, n번째 수열의 결과를 보여줄 것이다.

### 처음에 내가 짠 코드

```jsx
let input = parseInt(prompt("몇 번째 배열을 보여주세요", ""));
if (!input && typeof input !== "number") {
  alert("반드시 숫자만을 입력해주세요.");
  input = parseInt(prompt("몇 번째 배열을 보여주세요", ""), 10);
}

function ant(number) {
  let currentStr = "1";
  let output = "";

  for (let i = 0; i < number; i++) {
    // 진행횟수
    let count = 1;
    let currentTarget = currentStr[0];
    for (let j = 1; j < currentStr.length; j++) {
      if (currentStr[j] === currentTarget) {
        count++;
      } else {
        output += currentTarget;
        output += count;
        currentTarget = currentStr[j];
        count = 1;
      }
    }
    output += currentTarget;
    output += count;
    currentStr = output;
    output = "";
  }

  return currentStr;
}

let result = ant(input);
console.log(result);
```

### 위의 코드를 두 개의 함수로 분리하기

```jsx
// 개미 수열의 n번 줄을 계산
function ant(n) {
  let currentStr = "1";
  for (let i = 0; i < n; i++) {
    currentStr = next(currentStr);
  }
  return currentStr;
}

function next(s) {
  let count = 1;
  let head = s.charAt[0];
  let result = "";

  for (let i = 1; i < s.length; i++) {
    if (s.charAt[i] == head) {
      count++;
    } else {
      result += head;
      result += count;
      count = 1;
      head = s.charAt[i];
    }
  }
  result += head;
  result += count;
  return result;
}
```

### next( )가 하는 일

next( )는 여전히 여러가지 일을 한꺼번에 하고 있다.

- 루프 - 당연하게도 반복문을 포함하고 있다. 숫자를 하나씩 끝까지 읽어야 한다.
- 카운트 - 같은 숫자의 반복 횟수를 카운트한다.
- 비교 - 새로 읽은 숫자가 현재 카운트 중인 숫자와 같은지 비교한다.
- 문자열 생성 - 반복된 숫자의 갯수와 그 숫자를 이용하여 결과 문자열을 누적 생성한다.

각각의 일은 대수롭지 않지만 이런 일들을 한꺼번에, 그것도 서로 뒤엉킨 채로 처리한다는 점이 문제이다.

next( )를 리스트를 처리하는 함수로 본다면 다음과 같이 단계를 나누어 볼 수 있다.

예를 들어, [1, 2, 2, 2, 1, 1, 3, 1]를 입력으로 가정해 보자.

1. 리스트를 같은 숫자들의 그룹으로 나눈다. [1, 2, 2, 2, 1, 1, 3, 1] → [[1], [2, 2, 2], [1, 1], [3], [1]]
2. 각 그룹을 그룹의 숫자와 길이의 리스트로 바꾼다. → [[1, 1], [2, 3], [1, 2], [3, 1], [1, 1]]
3. 리스트의 리스트를 다시 하나의 리스트로 이어붙인다. → [1, 1, 2, 3, 1, 2, 3, 1, 1, 1]

재귀 함수와 위의 로직으로 구성된 코드

```jsx
function next(s) {
  // 각각 arr2는 같은 숫자들의 그룹이고, arr1은 그것을 모두 감싸는 배열이다.
  let arr1 = [];
  let arr2 = [];

  // 현재와 다음 문자열이 같다면 arr2에 맨 처음 숫자를 저장하고,
  // s에서 맨 처음 숫자를 잘라낸 채 다시 저장한다.
  // 만약 같지 않더라도 동일한 로직을 수행하며, 이후 이때까지 저장한 arr2를 arr1에 push하고, arr2는 초기화한다.
  while (s.length !== 0) {
    if (s[0] === s[1]) {
      arr2.push(s[0]);
      s = s.replace(s[0], ""); // s[0]을 잘라낸다.
    } else {
      arr2.push(s[0]);
      s = s.replace(s[0], "");
      arr1.push(arr2);
      arr2 = [];
    }
  }

  let answer = "";
  arr1.forEach((val) => {
    answer += val[0];
    answer += val.length;
  });
  return answer;
}

function ant(n) {
  if (n === 1) return "1";

  return solution(ant(n - 1));
}

console.log(ant(10));
```

출처 : https://lelana.tistory.com/73, book : 개미 수열을 푸는 10가지 방법
