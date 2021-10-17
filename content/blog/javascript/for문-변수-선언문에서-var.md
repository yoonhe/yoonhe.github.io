---
title: for문의 변수 선언문에서 var를 사용했을때 발생하는 문제, 그리고 해결방법
date: 2021-10-16 16:10:30
category: javascript
thumbnail: { thumbnailSrc }
draft: false
---

[프론트엔드 개발을 위한 자바스크립트(feat. VanillaJS)](https://programmers.co.kr/learn/courses/12819)에 참여하면서 공부한 내용을 정리합니다.<br />
이번 글에서는 for문의 변수 선언문에서 var를 사용했을때 발생하는 문제, 그리고 해결방법에 대해 정리하겠습니다

## for문의 변수 선언문에서 var를 사용했을때 발생하는 문제

```jsx
const numbers = [1, 2, 3, 4, 5];
for (var i = 0; i < numbers.length; i++) {
  setTimeout(function() {
    console.log(`number index ${i}`);
  }, 1000);
}
```

위의 코드는 아래처럼 동작합니다.

```jsx
const numbers = [1, 2, 3, 4, 5];
var i;
for (i = 0; i < numbers.length; i++) {
  setTimeout(function() {
    console.log(`number index ${i}`);
  }, 1000);
}
```

1. `var`는 `함수 레벨 스코프`를 가지기 때문에 `var`로 선언한 `i`는 전역변수가 됩니다
2. for문의 코드 블록이 반복 실행될 때마다 참조하는 i는 전역변수이고 `전역변수` `i`는 계속 `증가`됩니다
3. for문이 끝나면 setTimeout 안에 전달한 콜백 함수가 실행됩니다. 콜백 함수는 전역변수 `i`를 참조합니다 <br />
4. 그렇기 때문에 콘솔에는 `number index 5`가 5번 찍히게 됩니다.

var로 선언한 i는 for문에서만 유효한 것이 아닙니다. 전역변수가 되어 코드 전체에 영향을 미치게 됩니다. <br />

## 해결방법

### `let`

- for문의 변수 선언문에서 `let` 키워드로 선언한 변수를 사용하면 for문의 코드 블록이 반복 실행될 때마다 for문 코드 블록의 새로운 렉시컬 환경이 생성됩니다
- for문의 코드 블록 안에서 선언한 함수의 상위 스코프는 for문의 코드 블록이 반복 실행될 때마다 생성된 for문 코드 블록의 새로운 렉시컬 환경입니다.
- for문이 반복될 때마다 독립적인 렉시컬 환경을 생성하여 식별자 값을 관리합니다
  - `렉시컬 환경` : `식별자`와 `식별자에 바인딩된 값` 그리고 `상위 스코프에 대한 참조를 기록`하는 자료구조입니다
  - `렉시컬 스코프` : 자바스크립트 엔진은 함수를 어디에 정의했는지에 따라 상위 스코프를 결정합니다. 이를 렉시컬 스코프(정적 스코프)라 합니다

```jsx
const numbers = [1, 2, 3, 4, 5];
for (let i = 0; i < numbers.length; i++) {
  setTimeout(function() {
    console.log(`number index ${i}`);
  }, 1000);
}
```

- 참고 : 자바스크립트 딥 다이브 `24.6`

## 🙋🏻‍♀️ let으로 선언한 변수는 블록 스코프 안에서만 유효한데 for문이 종료되고 setTimeout 함수에 전달된 콜백함수가 실행되는 시점에 어떻게 i에 접근이 가능한 것인가요?

> 클로저는 함수와 함수가 선언된 렉시컬 환경과의 조합이다.<br /> - [mdn 클로저](https://developer.mozilla.org/ko/docs/Web/JavaScript/Closures)

- `클로저`때문에 가능한 것입니다.
  - 외부 함수보다 중첩 함수가 더 오래 유지되는 경우 중첩 함수는 이미 생명주기가 종료된 외부 함수의 변수를 참조할 수 있습니다. 이러한 중첩 함수를 `클로저` 라고 합니다
- 함수는 자신이 선언되었을때의 상위 스코프를 기억하고 있습니다. 이러한 이유로 for문이 종료되어도 콜백함수가 실행될때 i에 접근이 가능한 것입니다.
- 함수는 자신의 내부 슬롯 `[[Environment]]`에 자신이 정의된 환경, 즉 상위 스코프의 참조를 저장합니다

* 참고 : 자바스크립트 딥 다이브 `24.3`

### `IIFE` 사용 (예전에 사용한던 방법)

- `IIFE`가 내부의 setTimeout 안에서 사용되는 콜백 함수는 자신의 상위 스코프를 기억하는 클로저입니다.
  매개변수로 전달된 `i`는 for문이 종료되고 실행될 콜백 함수의 상위 스코프에 존재합니다

```jsx
const numbers = [1, 2, 3, 4, 5];
for (var i = 0; i < numbers.length; i++) {
  (function(i, number) {
    setTimeout(function() {
      console.log(`number [${i}] ${number}`);
    }, 1000);
  })(i, numbers[i]);
}
```

### `forEach` 사용

```jsx
const numbers = [1, 2, 3, 4, 5];

numbers.forEach((number, index) => {
  setTimeout(() => {
    console.log(number);
  }, 1000);
});
```

### 👉🏻 모든 방법의 공통점

변수 i의 스코프를 제한하고있다

### 👉🏻 실제로 어떠한 문제를 해결하는가?

- loop를 이용하여 ajax를 한번에 여러개 실행할때
- 생각보다 우리의 발목을 많이 잡을수도 있다
- 실제로 실행되는 순간에 바뀐 값을 참조하기 때문에, 이해를 잘 해야 한다
