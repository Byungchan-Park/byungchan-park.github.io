---
title: "정규표현식 by JavaScript"
excerpt: "정규표현식"

categories:
  - JavaScript
  - TIL
tags:
  - JavaScript
  - TIL
last_modified_at: 2020-11-05T08:06:00-05:00
---

## 정규 표현식이란?

정규 표현식이란 특정한 규칙을 가진 문자열의 집합을 표현하는 데 사용하는 형식 언어이다.

정규 표현식은 일반적으로 슬래쉬(/) 문자로 감싸진 형태로 표현된다.

## test 메서드

.test() 메서드는 정규 표현식을 인자로 들어가는 문자열에 적용한다. 그리고 문자열 안에서 정규 표현식에 들어맞는 무언가를 찾는다면 true를 반환하고 그렇지 않다면 false를 반환한다.

```jsx
let myString = "Hello, World!";
let myRegex = /Hello/;
myRegex.test(myString); // Returns true

let testStr = "Hello, my name is Kevin.";
let testRegex = /Kevin/;
testRegex.test(testStr); // Returns true

let wrongRegex = /kevin/;
wrongRegex.test(testStr); // Returns false
```

이제까지 살펴봤던 정규식 패턴으로는 하나의 패턴에만 맞는 문자열들만을 탐색할 수 있다.

OR 연산자(|)를 사용한다면 다수의 패턴으로 탐색하는 것이 가능하다.

```jsx
let petString = "James has a pet cat.";
let petRegex = /dog|cat|bird|fish/;
// 주어진 문자열 안에 dog, cat, bird, fish 문자열 중 하나라도 존재하면
// Returns true
let result = petRegex.test(petString);
```

## i 플래그

i 플래그를 사용하면 주어진 문자열에서 대문자, 소문자에 관계없이 정규표현식 패턴을 적용하여 탐색하는 것이 가능하다.

```jsx
let myString = "my Name is Ron";
let myRegex = /name/i;
let result = myRegex.test(myString); // Returns true
```

## match 메서드

.match( ) 메서드를 이용하여 정규식 패턴에 맞는 실제 문자열들을 추출해낼 수 있다.

```jsx
"Hello, World!".match(/hello/i);
// Returns ["Hello"]
let ourStr = "Regular expressions";
let ourRegex = /expressions/;
ourStr.match(ourRegex);
// Returns ["expressions"]
```

.match( ) 메서드는 .test( ) 메서드와 사용법이 약간 다르다.

.match( ) 메서드는 정규 표현식 객체의 메서드가 아닌 문자열 객체의 메서드이므로 맨 앞에는 대상이 되는 문자열이 오고, 인자로 정규 표현식을 가진다.

## g 플래그

아직까지는 주어진 문자열 안에서 정규 표현식 패턴을 만족하는 문자열들을 여러 개 찾는다 하더라도 오로지 하나의 문자열만을 추출해올 수 있다.

여러 개의 문자열을 추출하기 위해서는 정규 표현식의 뒤에 g 플래그를 붙여주면 된다.

```jsx
let testStr = "Repeat, Repeat, Repeat";
let ourRegex = /Repeat/;
testStr.match(ourRegex);
// Returns ["Repeat"]
let repeatRegex = /Repeat/g;
testStr.match(repeatRegex);
// Returns ["Repeat", "Repeat", "Repeat"]

let twinkleStar = "Twinkle, twinkle, little star";
let starRegex = /twinkle/gi;
let result = twinkleStar.match(starRegex);
// Returns ["Twinkle", "twinkle"]
```

## 와일드카드 문자열 .

와일드카드 문자열 **.** 은 어떤 문자에 상관없이 하나의 문자열과 일치할 것이다.

만약 "hug", "huh", "hut", 그리고 "hum"과 같은 문자열과 일치시키고 싶다면, 정규 표현식으로 /hu./ 을 사용할 수 있다.

```jsx
let humStr = "I'll hum a song";
let hugStr = "Bear hug";
let huRegex = /hu./;
huRegex.test(humStr); // Returns true
huRegex.test(hugStr); // Returns true

let exampleStr = "Let's have fun with regular expressions!";
let unRegex = /.un/; // "run", "sun", "fun", "pun"을 만족하는 정규 표현식 패턴이다.
let result = unRegex.test(exampleStr);
```

## Character 클래스

Character 클래스는 정규표현식 패턴 내에서 어떤 문자의 탐색 범위를 제한시켜 탐색할 수 있게 해준다.

예를 들어 "bog"와는 일치하지 않으며 "bag", "big", "bug"와 일치하는 정규표현식 패턴을 만들어낸다면, /b[aiu]g/ 와 같이 만들어낼 수 있다.

[aiu]는 오로지 "a", "i", "u"와만 일치하는 character 클래스이다.

```jsx
let bigStr = "big";
let bagStr = "bag";
let bugStr = "bug";
let bogStr = "bog";
let bgRegex = /b[aiu]g/;
bigStr.match(bgRegex); // Returns ["big"]
bagStr.match(bgRegex); // Returns ["bag"]
bugStr.match(bgRegex); // Returns ["bug"]
bogStr.match(bgRegex); // Returns null

let quoteSample =
  "Beware of bugs in the above code; I have only proved it correct, not tried it.";
let vowelRegex = /[aeiou]/gi;
let result = quoteSample.match(vowelRegex);
// ['e','a','e','o','u','i','e','a','o','e','o','e','I','a','e','o','o','e','i','o','e','o','i','e','i']
```

## Hypen character -

hypen character - 를 사용하여 일치할 문자들의 범위를 정의할 수 있다.

예를 들어, 소문자 a 부터 e 까지 일치시키기 위해서는 [a-e]를 사용하면 된다.

```jsx
let catStr = "cat";
let batStr = "bat";
let matStr = "mat";
let bgRegex = /[a-e]at/;
catStr.match(bgRegex); // Returns ["cat"]
batStr.match(bgRegex); // Returns ["bat"]
matStr.match(bgRegex); // Returns null

let quoteSample = "The quick brown fox jumps over the lazy dog.";
let alphabetRegex = /[a-z]/gi;
// 문자열 안에서 모든 알파벳을 찾아낸다.
let result = quoteSample.match(alphabetRegex);
```

hypen character - 는 문자뿐만 아니라 숫자에도 적용이 가능하다.

예를 들어, /[0-5]/ 는 0과 5를 포함하여 0부터 5 사이의 숫자와 일치한다.

또한, 하나의 character set 안에서 문자와 숫자의 범위를 조합하는 것이 가능하다.

```jsx
let jennyStr = "Jenny8675309";
let myRegex = /[a-z0-9]/gi;
// jennyStr 안에서 모든 알파벳과 숫자를 찾아낸다.
jennyStr.match(myRegex);
```

## Negated character sets

이제까지는 우리가 일치시키고 싶은 문자열들에 대한 세트만을 만들어왔다. 하지만 우리가 일치시키고 싶지 않은 문자열들에 대한 세트 또한 만들어낼 수 있다. 이러한 유형의 문자열들 세트들은 negated character sets라 불리운다.

negated character set를 만들기 위해, [ 의 바로 뒤 그리고 우리가 일치시키고 싶지 않은 첫 번째 문자의 바로 앞에 caret character(^)를 붙여주면 된다.

예를 들어, /[^aeiou]/gi는 모음이 아닌 모든 문자열들과 일치한다.

심지어 . ! { @ / 와 공백동안 일치한다. 오로지 모음인 문자들만이 제외된다.

```jsx
let quoteSample = "3 blind mice.";
// a single regex that matches all characters that are not a number or a vowel
let myRegex = /[^0-9aeiou]/gi;
let result = quoteSample.match(myRegex);
```

## "+" character

"+" character 는 앞에 존재하는 문자가 1번 혹은 그 이상 반복되는 문자를 찾을 때 사용한다.

```jsx
const myStr1 = "abc";
const myStr2 = "aabc";
const myStr3 = "abab";
// find "a" character that appears one or more times in a row
const aRegex = /a+/g;
myStr1.match(aRegex); // Returns ["a"]
myStr2.match(aRegex); // Returns ["aa"]
myStr3.match(aRegex); // Returns ["a", "a"]

let difficultSpelling = "Mississippi";
// find "a" character that appears one or more times in a row
let myRegex = /s+/g;
let result = difficultSpelling.match(myRegex); // Returns ["ss", "ss"]
```

## "\*" character

"\*" character는 앞에 존재하는 문자가 0번 혹은 그 이상 반복되는 문자를 찾을 때 사용한다.

```jsx
let soccerWord = "gooooooooal!";
let gPhrase = "gut feeling";
let oPhrase = "over the moon";
// "g" 혹은 "go", "goo" ... 과 일치한다. 반드시 첫 번째 문자는 g여야 한다.
let goRegex = /go*/;
soccerWord.match(goRegex); // Returns ["goooooooo"]
gPhrase.match(goRegex); // Returns ["g"]
oPhrase.match(goRegex); // Returns null
```

## Greedy Matching 과 Lazy Matching

정규 표현식은 기본적으로 탐욕적(Greedy) 방식으로 동작한다. 문자열에서 정규 표현식을 만족하는 가장 긴 문자열을 반환한다.

하지만 때로는 정규 표현식을 만족하는 가장 짧은 문자열을 반환하고 싶을 것이다. 이는 ? 문자를 사용하면 된다. ? 문자를 해당 메타 문자 뒤에 붙이면 게으른(lazy) 방식으로 동작한다.

- \*?
- +?
- {n, }?

예를 들어 o로 시작하고 n으로 끝나는 모든 문자를 찾는다고 가정해보자. 코드는 다음과 같다.

```jsx
let word = "organization";
let greedyRegex = /o\w+n/;
word.match(greedyRegex); // ["organization"]
```

결과는 organization으로 모두가 일치했다. o로 시작하고 n으로 끝나는 모든 문자를 찾고 싶었지만, 일치하는 것은 organization 통째로 하나뿐이었다.

이번에는 게으른 방식으로 바꿔보겠다.

```jsx
let word = "organization";
let lazyRegex = /o\w+?n/;
word.match(greedyRegex); // ["organ"]
```

앞의 예제와의 차이점은 단 하나 ? 문자이다. ?를 붙임으로 인해서 게으른 방식으로 동작하고 있다. organization은 o로 시작하고 n으로 끝나는 문자이긴 하지만, 그 사이에도 o로 시작하고 n으로 끝나는 문자가 존재한다.

이렇게 요구에 따라 탐욕적 혹은 게으른 방식의 찾기를 할 수 있다.

## 가장 처음의 문자열 패턴 검사하기

character set 밖에서 caret character ^는 문자열의 처음 부분에만 정규 표현식 패턴을 적용하는데 사용된다.

```jsx
let firstString = "Ricky is first and can be found";
let firstRegex = /^Ricky/;
firstRegex.test(firstString); // Returns true
let notFirst = "You can't find Ricky now.";
firstRegex.test(notFirst); // Returns false
```

## 가장 뒤의 문자열 패턴 검사하기

dollar sign character \$는 문자열의 뒷부분에만 정규 표현식 패턴을 적용하는데 사용된다.

```jsx
let theEnding = "This is a never ending story";
let storyRegex = /story$/;
storyRegex.test(theEnding); // Returns true
let noEnding = "Sometimes a story will have to end";
storyRegex.test(noEnding); // Returns false
```

## \w shortcuts

\w shortcuts은 [A-Za-z0-9_]와 같다. 이 shortcuts은 대문자, 소문자, 숫자 그리고 \_ 를 포함한다.

```jsx
let longHand = /[A-Za-z0-9_]+/;
let shortHand = /\w+/;
let numbers = "42";
let varNames = "important_var";
longHand.test(numbers); // Returns true
shortHand.test(numbers); // Returns true
longHand.test(varNames); // Returns true
shortHand.test(varNames); // Returns true
```

## \W shortcuts

\W shortcuts는 [^a-za-z0-9_]와 같다. 이 shortcuts은 \w character class와 반대이다. 대문자, 소문자, 숫자 그리고 \_ 를 포함하지 않는다.

```jsx
let shortHand = /\W/; // === [^A-Za-z0-9_]
let numbers = "42%";
let sentence = "Coding!";
numbers.match(shortHand); // Returns ["%"]
sentence.match(shortHand); // Returns ["!"]
```

## \d shortcuts

\d shortcuts는 [0-9]와 같다. 즉 숫자와 일치한다.

```jsx
let movieName = "2001: A Space Odyssey";
// looks for a single character that is a number between zero and nine.
let numRegex = /\d/g;
let result = movieName.match(numRegex); // ["2", "0", "0", "1"]
```

## \D shortcuts

\D shortcuts는 [^0-9]와 같다.

## \s shortcuts

\s shortcuts는 여러가지 공백 문자와 일치한다. (스페이스, 탭, 기타 공백문자)

\s shortcuts는 [ \r\t\f\n\v]와 같다.

```jsx
let whiteSpace = "Whitespace. Whitespace everywhere!";
let spaceRegex = /\s/g;
whiteSpace.match(spaceRegex);
// Returns [" ", " "]
```

## \S shortcuts

\S shortcuts는 공백 문자를 제외한 문자와 일치한다.

\S shortcuts는 [^ \r\t\f\n\v]와 같다.

```jsx
let whiteSpace = "Whitespace. Whitespace everywhere!";
let nonSpaceRegex = /\S/g;
whiteSpace.match(nonSpaceRegex).length; // Returns 32
```

## 상한값과 하한값 설정으로 특정 문자 수를 지정하기

이전까지 우리는 하나의 문자가 한 번 이상 반복되는 것을 찾기 위해 + 를 사용하였고 하나의 문자가 한 번 이상 반복되거나 존재하지 않을 수도 있는 것을 찾기 위해 \* 를 사용하였다.

때로는 문자의 반복 횟수를 우리가 원하는 만큼 설정하여 일치하는 문자열을 찾아내고 싶을 것이다.
이때 우리는 {3, 5}와 같이 설정하면 앞에 존재하는 문자가 3번 이상 5번 이하 반복되는 문자와 일치한다.

예를 들어 문자 a가 문자열 "ah"에서 3번 이상 5번 이하로 반복되는 문자열을 찾는 정규식은 /a{3,5}/ 이다.

```jsx
let A4 = "aaaah";
let A2 = "aah";
let multipleA = /a{3,5}h/;
multipleA.test(A4); // Returns true
multipleA.test(A2); // Returns false
```

## 상한값의 설정 없이 하한값만 설정하여 특정 문자 수를 지정하기

```jsx
let A4 = "haaaah";
let A2 = "haah";
let A100 = "h" + "a".repeat(100) + "h";
let multipleA = /ha{3,}h/;
multipleA.test(A4); // Returns true
multipleA.test(A2); // Returns false
multipleA.test(A100); // Returns true
```

## 상한값, 하한값 설정이 아닌 특정 문자수를 지정하기

```jsx
let A4 = "haaaah";
let A3 = "haaah";
let A100 = "h" + "a".repeat(100) + "h";
let multipleHA = /ha{3}h/;
multipleHA.test(A4); // Returns false
multipleHA.test(A3); // Returns true
multipleHA.test(A100); // Returns false
```

## Check for All or None

x? : 존재여부를 표현하며 x 문자가 존재할 수도, 존재하지 않을 수도 있음을 의미한다.

```jsx
let american = "color";
let british = "colour";
// 알파벳 u는 존재할 수도, 존재하지 않을 수도 있다.
let rainbowRegex = /colou?r/;
rainbowRegex.test(american); // Returns true
rainbowRegex.test(british); // Returns true
```

## 전방탐색과 후방탐색

원하는 문자를 검색하기 위하여 정규식을 사용하였지만 어디서부터 문자를 찾을지 정해야 할 때도 있다. 이때 전,후방탐색을 사용해야 한다.

전방탐색은 특정한 문자 앞에 일치하는 문자만 찾고 후방탐색은 특정한 문자 뒤에 일치하는 문자만 찾는다. 전방탐색과 후방탐색으로 일치된 문자는 그룹처럼 보이지만 결과에는 포함되지 않는다.

- (?=) : 전방탐색. 찾고자 하는 표현식 뒤에 전방탐색 표현식을 넣으며 (?= 와 ) 사이에 표현식을 넣는다. 전방탐색 표현식을 통해 문자가 존재하고, 그 앞에 찾고자 하는 문자가 존재할 때 일치한다. 특정 문자가 포함된 문자를 찾고 싶지만 결과에 포함하고 싶지는 않을 때 사용한다.
- (?<=) : 후방탐색. 후방탐색 표현식 (?<= 와 ) 사이에 표현식을 넣고, 찾고자 하는 표현식을 작성한다. 후방탐색 표현식을 통해 문자가 존재하고, 그 뒤에 찾고자 하는 문자가 존재할 때 일치한다. 전방탐색과 유사하다.

이와 반대인 탐색이 있다.

- (?!) : 부정형 전방탐색. 전방탐색과 반대로, 부정형 전방탐색 내의 표현식이 일치하지 않고, 찾고자 하는 문자가 존재할 때 일치한다.
- (?<!) : 부정형 후방탐색. 후방탐색과 반대로, 부정형 후방탐색 내의 표현식이 일치하지 않고, 찾고자 하는 문자가 존재할 때 일치한다.

## 캡쳐 그룹으로 패턴 재사용하기

반복되는 부분을 굳이 정규 표현식 안에서 반복하여 작성할 필요가 없다. 반복되는 부분은 캡쳐 그룹이 대신할 것이다.

괄호 ( 와 )이 반복되는 문자열을 찾는 데 사용된다. 괄호 사이에 반복되는 문자열을 넣어 정규 표현식에서 사용하면 된다.

반복되는 문자열이 어디에 나타는지 지정하기 위해 백슬래쉬(/\) 그리고 이어서 숫자를 사용한다.

이 숫자는 캡쳐 그룹의 개수가 늘어나면 그에 따라 1씩 증가한다. 즉 각 캡쳐 그룹은 자신만의 식별번호(ex) \1)를 가지게 되어 정규 표현식 안에서 해당 캡쳐 그룹을 반복할 때 식별 번호를 이용한다.

```jsx
let repeatStr = "regex regex";
// \1은 첫번째 캡쳐 그룹의 식별 번호이다.
let repeatRegex = /(\w+)\s\1/;
repeatRegex.test(repeatStr); // Returns true
// match 메서드는 괄호 속 패턴과 일치하는 문자열과 정규 표현식 전체 패턴과 일치하는 문자열을 요소로 가지는 배열을 반환한다.
repeatStr.match(repeatRegex); // Returns ["regex regex", "regex"]

let repeatNum = "42 42 42";
// \1은 첫번째 캡쳐 그룹(숫자를 1개 이상 포함)의 식별 번호이다.
// \2는 두번째 캡쳐 그룹(공백 문자)의 식별 번호이다.
let reRegex = /^(\d+)(\s)\1\2\1$/;
let result = reRegex.test(repeatNum); // Returns true

let testString = "test test test test test test";
let reRegex = /(test)(\s)\1\2\1/g;
let result = reRegex.test(testString); // Returns true
```

## 원하는 문자열을 찾고 교체하기 위해 캡쳐 그룹 사용하기

```jsx
// 여기서 첫 번째 캡쳐 그룹은 "Code"를 두 번째 캡쳐 그룹은 "Camp"를 일치 문자열로 찾아낸다.
// 교체하려는 문자열 안에서 $를 사용하여 각 캡쳐 그룹이 찾아낸 일치 문자열에 접근할 수 있다.
// 각각 첫 번째 캡쳐 그룹은 $1, 두 번째 캡쳐 그룹은 $2이다.
// 만약 교체하려는 문자열에 '$2 $1'이라면 두 캡쳐 그룹에 의해 찾은 문자열의 위치가 서로 바뀌게 된다.
"Code Camp".replace(/(\w+)\s(\w+)/, "$2 $1");
// Returns "Camp Code"

// 캡쳐 그룹들은 각각 "one" "two" "three"를 일치 문자열로 찾아낸다.
// 그리고 각각 교체 문자열 안에서 $1 $2 $3로 이 일치 문자열에 접근이 가능하다.
// 이를 이용하여 교체 문자열 안에서 서로 순서를 바꾸는 것이 가능하다.
let str = "one two three";
let fixRegex = /(\w+)\s(\w+)\s(\w+)/;
let replaceText = "$3 $2 $1";
let result = str.replace(fixRegex, replaceText);
```

## 문자열의 앞쪽과 뒤쪽 부분의 공백 제거

공백을 제거하는 방법에는 문자열의 메서드 중 trim( ) 메서드가 있지만 이번에는 정규 표현식과 replace( ) 메서드를 사용해보려고 한다.

```jsx
let hello = "   Hello, World!  ";
// 문자열의 시작 부분부터 공백이 1개 이상 존재하는지 전부 찾아낸다.
// 혹은 문자열의 끝 부분부터 공백이 1개 이상 존재하는지 전부 찾아낸다.
// 반드시 g 플래그를 붙여주어야 한다.
// g 플래그를 붙여주지 않으면,
// 앞부분의 공백만 찾아낸다.
let wsRegex = /^\s+|\s+$/g;
// 찾아낸 공백들을 전부 제거한다.
let result = hello.replace(wsRegex, "");
```

출처

- <a href="https://medium.com/@originerd/%EC%A0%95%EA%B7%9C%ED%91%9C%ED%98%84%EC%8B%9D-%EC%A2%80-%EB%8D%94-%EA%B9%8A%EC%9D%B4-%EC%95%8C%EC%95%84%EB%B3%B4%EA%B8%B0-5bd16027e1e0">정규표현식 정리</a>
- <a href="https://www.freecodecamp.org/learn/javascript-algorithms-and-data-structures/regular-expressions">freecodecamp 정규 표현식 by JavaScript</a>
