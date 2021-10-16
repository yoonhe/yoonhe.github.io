---
title: this - 일반함수 호출
date: 2021-10-16 13:10:32
category: javascript
thumbnail: { thumbnailSrc }
draft: false
---

## 일반 함수 호출에서 this

- 일반 함수로 호출하면 함수 내부의 this에는 기본적으로 전역객체가 바인딩된다
- 메서드 내에서 정의한 중첩 함수도 일반 함수로 호출되면 중첩 함수 내부의 this에는 전역객체가 바인딩된다.

## setTimeout의 콜백함수 내부의 this

- 콜백 함수가 일반 함수로 호출된다면 콜백 함수의 내부의 this에는 전역객체가 바인딩된다(자바스크립트 딥다이브 `22.2.1 일반 함수 호출` 참고)

  ```js
  function Person(name) {
    this.name = name;
    this.getName = function() {
      setTimeout(function() {
        console.log('this ? ', this);
        console.log('this.name ? ', this.name);
      }, 1000);
    };
  }

  const heaeun = new Person('heaeun');

  heaeun.getName();
  /* 
  	this ?  Window {0: Window, window: Window, self: Window, document: document, 
  	name: '', location: Location, …} 
  */

  // this.name ? ''
  ```

## 중첩 함수 또는 콜백함수가 일반 함수로 호출되는 경우 함수 내부의 this가 전역 객체를 바인딩하는 문제를 해결하는 방법

### bind를 이용한 해결법

`bind` 메서드를 사용하여 인스턴스 객체가 바인딩된 `this`를 명시적으로 바인딩한다

```js
function Person(name) {
  this.name = name;
  this.getName = function() {
    setTimeout(
      function() {
        console.log('this ? ', this);
        console.log('this.name ? ', this.name);
      }.bind(this), // this의 명시적 바인딩
      1000
    );
  };
}

const heaeun = new Person('heaeun');
heaeun.getName();
// this ?  Person {name: 'heaeun', getName: ƒ}
// heaeun
```

### arrow function

`arrow function` 내부의 `this`는 상위 스코프의 `this`를 바인딩합니다

```js
function Person(name) {
  this.name = name;
  this.getName = function() {
    setTimeout(() => {
      console.log('this ? ', this);
      console.log('this.name ? ', this.name);
    }, 1000);
  };
}

const heaeun = new Person('heaeun');
heaeun.getName();
// this ?  Person {name: 'heaeun', getName: ƒ}
// this.name ? heaeun
```

## 결론

- 일반 함수로 호출된 모든 함수(중첩 함수, 콜백 함수 포함) 내부의 this에는 전역 객체가 바인딩됩니다
- 중첩 함수, 콜백 함수 내부의 this가 의도한대로 바인딩이 되기를 원한다면 `arrow function` 혹은 `bind` 메서드를 사용하면 됩니다
