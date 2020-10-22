---
title: "이진탐색"
excerpt: "이진탐색"

categories:
  - TIL
  - Algorithm
tags:
  - TIL
  - Algorithm
last_modified_at: 2020-10-23T08:06:00-05:00
---

이진 검색은 정렬된 리스트에서 원하는 항목을 찾기에 효율적인 알고리즘이다. 이 검색법은 후보 범위가 한 항목으로 좁아질 때까지 찾고자 하는 항목을 포함하고 있는 리스트를 반으로 나누는 과정을 계속 반복한다.

입력값은 array라고 부르는 배열, array의 요소의 개수 n, 검색 대상의 수 target이다. 결과값은 array 속 target의 인덱스 값이다.

1. min = 0 이고 max = n - 1이라고 한다.
2. max < min이면 검색을 멈춘다. target이 array에 존재하지 않는다. -1을 반환한다.
3. guess의 값은 max와 min의 평균값을 정수가 되도록 버림한 값이다.
4. array[guess]의 값이 target과 같다면 검색을 멈춘다. 타겟을 찾았다. guess를 결과값으로 반환한다.
5. 추측값이 더 작다면 즉, array[guess] < target이라면, min = guess + 1으로 바꾼다.
6. 아니면 추측값이 더 크다. 그러면 max = guess - 1로 바꾼다.
7. 2 단계로 돌아간다

```jsx
function binarySearch(arr, target) {
  let min = 0;
  let max = arr.length - 1;
  let guess;

  while (max >= min) {
    guess = Math.floor((min + max) / 2);

    if (arr[guess] === target) {
      return guess;
    } else if (arr[guess] > target) {
      max = guess - 1;
    } else {
      min = guess + 1;
    }
  }

  return -1;
}
```

이진 탐색에서는 기준값과 배열의 정중앙에 있는 데이터를 비교하여 맞지 않을 경우 배열에 있는 기준값과 일치하지 않는 다른 쪽에 있는 값들은 모두 버린다. 따라서 남은 값들에 대한 탐색을 다시 시작할 때 옳은 값을 찾을 확률은 이전 탐색에 비해 최대 두 배가 올라간다.

배열이 두 배 커질때마다 최대 탐색 횟수는 한 번 늘어난다.

[https://m.blog.naver.com/horajjan/220310806806](https://m.blog.naver.com/horajjan/220310806806)
