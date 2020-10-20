---
title: "재귀함수 feat.JavaScript"
excerpt: "재귀함수"

categories:
  - TIL
  - Algorithm
tags:
  - TIL
  - Algorithm
last_modified_at: 2020-10-20T08:06:00-05:00
---

## 재귀함수란?

재귀함수란 어떤 함수에서 자신을 다시 호출하여 작업을 수행하는 방식의 함수를 의미한다. 반복문을 사용하는 코드는 항상 재귀함수를 통해 구현하는 것이 가능하며 그 반대도 가능하다.

재귀의 반복 개념은 다음과 같이 두 개의 간단한 규칙으로 간추릴 수 있다.

- 재귀의 호출은 같은 문제 내에서 더 범위가 작은 값, 즉 하위 문제에 대해 이루어져야 한다.
- 재귀함수 호출은 더 이상 반복되지 않는 base case에 도달해야 한다.

## 재귀함수의 특징

- 무한 루프에 빠지지 않기 위해 일정한 탈출 조건이 있어야 한다.
- 코드를 단순화할 수 있다.
- 재귀 함수는 호출 시마다 스택 공간을 이용하므로 무리하게 호출하면 스택 오버플로우가 일어날 수 있다.
- 재귀 함수의 호출 횟수는 스택의 남은 공간과 재귀 함수의 지역 변수 사이즈에 따라 달라진다.
- 디버깅 및 실행 흐름을 파악하기 힘들다.

재귀함수

```jsx
function recursive(인자) {
  작업수행
  if (조건충족) {
    return 결과;
  } else {
    return recursive(작업된 인자);
  }
}
```

```jsx
function answerByStory(question) {
  // 임의의 이야기에서 답 찾기
  let answer = findFromRandomStory();

  // 이야기 속에서 답이 나오지 않으면
  if (answer === null) {
    return answerByStory(question); // 재귀 호출
    // 답이 나오면
  } else {
    return answer; // 답을 반환
  }
}
```

## 재귀함수의 사례

### 재귀 팩토리얼

```jsx
function factorial(n) {
  // 탈출 조건
  if (n === 0) {
    return 1;
  }

  return n * factorial(n - 1);
}
```

### 한 배열 총합 구하기

```jsx
let numbers = [3, 1, 4, 1, 5, 9];

function recursive(acc, array) {
  if (array.length === 0) {
    return acc;
  } else {
    return recursive(acc + array[0], array.slice(1));
  }
}
recursive(0, numbers);
```

### 배열 안에 있는 숫자의 총합 구하기

```jsx
let numAndArrayHell = [
  3,
  [1, 4, [3, [6, 2], 5], 1, 3],
  4,
  [8, 1, [2, 1, 9], 5],
  5,
  9,
];

function recursive_deep(acc, array) {
  if (array.length === 0) {
    return acc;
  } else {
    return recursive_deep(
      acc +
        (typeof array[0] === "number" ? array[0] : recursive_deep(0, array[0])),
      array.slice(1)
    );
  }
}

console.log(recursive_deep(0, numAndArrayHell));
```

### 하노이의 탑

원리 : n개의 원반을 특정 기둥으로 옮기려면 맨 아래를 제외한 원반들을 다른 기둥으로 옮기고 맨 아래 원반을 목적지 기둥으로 옮기고 다른 기둥에 옮겼던 원반을 그 위에 얹는 것이다.

```jsx
function hanoi(num, from, to, other) {
  // num: 원반의 수
  // from: 원반들이 위치한 곳의 번호
  // to: 원반들을 옮길 목적지 번호
  // other: 나머지 한 곳(목적지가 아닌) 곳 번호

  // 모두 옮겼으면 종료
  if (num === 0) return;

  // 가장 아래 원반을 제외한 원반들을 재귀적으로 목적지가 아닌 곳으로 옮김
  hanoi(num - 1, from, other, to);

  // 가장 아래 원반을 목적지로 옮김
  console.log(`${from}번에서 ${to}로 옮긴다.`);

  // 목적지가 아닌 곳으로 옮겼던 원반들을 재귀적으로 목적지로 옮김
  hanoi(num - 1, other, to, from);
}
hanoi(4, 0, 1, 2);
```

```jsx
// 실행 결과
0번에서 2로 옮긴다.
0번에서 1로 옮긴다.
2번에서 1로 옮긴다.
0번에서 2로 옮긴다.
1번에서 0로 옮긴다.
1번에서 2로 옮긴다.
0번에서 2로 옮긴다.
0번에서 1로 옮긴다.
2번에서 1로 옮긴다.
2번에서 0로 옮긴다.
1번에서 0로 옮긴다.
2번에서 1로 옮긴다.
0번에서 2로 옮긴다.
0번에서 1로 옮긴다.
2번에서 1로 옮긴다.
```

### 숫자의 거듭제곱 계산

```jsx
function recursive(x, n) {
  if (n === 0) {
    return 1;
  }

  // n이 짝수일 경우
  if (n % 2 === 0) {
    let num = recursive(x, n / 2);
    return num * num;
    // n이 홀수일 경우
  } else if (n % 2 !== 0) {
    return x * recursive(x, n - 1);
    // n이 음수일 경우
  } else if (n < 0) {
    return 1 / recursive(x, -n);
  }
}

console.log(recursive(3, 3));
// expected output : 27
```

### 회문(Palindrome) : 앞으로 혹은 뒤로 읽어도 똑같은 단어들을 의미함.

```jsx
function firstCharacter(word) {
  return word.slice(0, 1);
}

function lastCharacter(word) {
  return word.slice(-1);
}

function isPalindrome(word) {
  if (word.length <= 1) {
    return true;
  }

  if (firstCharacter(word) !== lastCharacter(word)) {
    return false;
  }

  return isPalindrome(word.slice(1, -1));
}

console.log(isPalindrome("auto"));
console.log(isPalindrome("arra"));
```

## setTimeout을 사용한 재귀함수 콜스택 터짐 방지

재귀 함수를 사용할 때 setTimeout을 사용하면 콜스택이 터지는 것을 막을 수 있다.

```jsx
function a() {
  setTimeout(function () {
    a();
  }, 0);
}

a();
```

a함수가 호출되면, Web API에게 setTimeout 이하 콜백을 0초 후에 실행하라고 예약을 걸어 놓으며 a함수의 호출이 종료된다. setTimeout의 콜백은 0초 후에 큐에 들어가게 되고, 이벤트 루프가 우선 스택이 비어 있는지 확인을 한다. 만약 스택이 비어 있다면 큐에 들어가 있던 setTimeout의 콜백을 콜스택에 집어넣고, 실행시킨다.

위와 같이 비동기를 이용하면 콜스택이 터지는 것을 방지할 수 있다.

## 꼬리 재귀 최적화(Tail Recurse Optimization)

꼬리 재귀는 재귀함수의 단점을 보완하기 위한 최적화 방법이다.
꼬리 재귀란, 재귀 호출이 끝난 후 현재 함수에서 추가 연산을 요구하지 않도록 구현하는 재귀의 형태이다.
이를 이용하면 함수 호출이 반복되어 스택이 깊어지는 문제를 컴파일러가 선형으로 처리하도록 알고리즘을 바꿔 스택을 재사용할 수 있게 된다.
꼬리 재귀를 사용하기 위해서는 내가 사용하는 컴파일러가 꼬리 재귀 최적화 기능을 지원하는지 먼저 확인해야 한다.

내가 사용하는 언어가 꼬리 재귀 최적화가 가능한가?

```jsx
function canTailRecurse(arg) {
  // ...
  return canTailRecurse(arg);
}
// 함수 자체만 return: 꼬리재귀

function canTailRecurse(arg) {
  let n;
  // ...
  return n * canTailRecurse(arg);
}
// 다른 것(들)이 섞여 return: 꼬리재귀 불가
```

### 일반적인 재귀함수와 꼬리 재귀함수의 차이

#### 일반적인 재귀함수

```jsx
function factorial(n) {
  if (n === 0) return 1;
  return n * factorial(n - 1);
}
```

이는 우리가 흔히 알고 있는 재귀함수이다.
재귀함수는 반환하면서 계산된다.

```md
<일반 재귀함수>
호출 : factorial(3)

3 _ factorial(2)
3 _ (2 _ factorial(1))
3 _ 2 _ (1 _ factorial(0))
3 _ 2 _ 1 _ 1
3 _ 2
6
```

#### 꼬리 재귀함수

```jsx
function factorial(acc, n) {
  if (n === 0) return acc;
  return factorial(acc * n, n - 1);
}
```

일반 재귀에서의 n \* factorial(n-1)와 달리 반환값에서 추가 연산을 필요로 하지 않는다.
꼬리 재귀함수는 acc 변수에 계산한 값을 저장한 후 값을 한번에 반환한다.

```md
<꼬리 재귀함수>
호출 : factorial(1, 3)

factorial(3, 2)
factorial(6, 1)
6
6
6
```
