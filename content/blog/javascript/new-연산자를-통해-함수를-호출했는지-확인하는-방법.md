---
title: new 연산자를 통해 함수를 호출했는지 확인하는 방법
date: 2021-10-16 11:10:75
category: javascript
thumbnail: { thumbnailSrc }
draft: false
---

`new.target`, `instanceof` 로 new 연산자를 통해 함수를 호출했는지 확인이 가능합니다

## new.target

> [new.target](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Operators/new.target)
> new.target 속성(property)은 함수 또는 생성자가 new 연산자를 사용하여 호출됐는지를 감지할 수 있습니다. new 연산자로 인스턴스화된 생성자 및 함수에서, new.target은 생성자 또는 함수 참조를 반환합니다. 일반 함수 호출에서는, new.target은 undefined입니다.
> 👉🏻 출처 - mdn

ES6에서는 `new.target`을 지원합니다

함수 내부에서 `new.target`을 사용하면 `new` 연산자와 함께 생성자 함수로서 호출되었는지를 확인할 수 있습니다

```js
function Person(name) {
  console.log('new.target ? ', new.target)

  this.name = name
}
```

### new 연산자와 함께 실행 했을때

new 연산자와 함께 생성자 함수로 호출되면 `new.target`은 함수 자신을 가리킵니다

```js
new Person('heaeun')

/*
new.target ?  ƒ Person(name) {
  console.log('new.target ? ', new.target);

  this.name = name;
}
*/
```

console.dir로 찍어보면 constructor가 Person 생성자 함수를 가리킵니다

> 생정자 함수?
> new 연산자와 함께 호출하여 객체(`인스턴스`)를 생성하는 함수를 말한다
> 생성자 함수에 의해 생성된 객체를 `인스턴스`라 한다
> 👉🏻 출처 - 자바스크립트 딥다이브 도서

```js
console.dir(new.target)
```

![](https://images.velog.io/images/yhe228/post/667ab458-7dee-459d-a2ac-f946eef23e1e/image.png)

### new 연산자 없이 실행 했을때

new 연산자 없이 일반 함수로 호출되면 `new.target`은 `undefined` 입니다

```jsx
Person('heaeun')
// new.target ?  undefined
```

`console.dir(new.target)` 도 `undefined` 가 찍힙니다

위와같은 특징을 사용하여 생성자 함수가 `new` 연산자 존재 여부에 따라 코드를 작성할 수 있습니다

```jsx
function Person(name) {
  if (new.target) {
    console.log('new 연산자 사용')
    return
  }

  console.log('new 연산자 없음')

  this.name = name
}

new Person('heaeun') // new 연산자 사용
Person('heaeun') // new 연산자 없음
```

### new 연산자 없이 호출된 일반 함수를 재귀호출을 통해 new 연산자와 함께 생성자 함수로 만드는 방법

1. `new.target`을 사용하여 `new 연산자`와 함께 호출되었는지를 확인한다.
2. 만약 아니라면 `new 연산자` 와 함께 생성자 함수로서 호출하고 반환한다

```js
function Cat(name, age) {
  if (!new.target) {
    return new Cat(name, age)
  }

  this.name = name
  this.age = age
}

const malang = Cat('malang', 1)

console.log(malang)
// Cat {name: 'malang', age: 1}
```

## instanceof

`new.target`을 `IE`에서는 지원하지 않으므로 이러한 경우에는 `instanceof` 연산자를 사용하면 된다

> [instanceof 연산자](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Operators/instanceof)
> instanceof 연산자는 생성자의 prototype 속성이 객체의 프로토타입 체인 어딘가 존재하는지 판별합니다.
> 👉🏻 출처 - mdn

생성자 함수의 this는 생성자 함수가 생성할 인스턴스를 가리키는데
이 인스턴스의 프로토타입 체인에 Person 생성자가 있는지 확인하는 것이다 !

```js
function Person(name) {
  console.dir(this)

  if (this instanceof Person) {
    console.log('new 키워드 호출')
    return
  }

  console.log('new 키워드 없음')

  this.name = name
}
```

### new 연산자와 함께 실행 했을때

```js
new Person('heaeun') // new 키워드 호출
```

`this`는 `Person` 생성자 함수를 가리킨다
![](https://images.velog.io/images/yhe228/post/b1347a2e-1f77-45ab-883f-2cea1e283d7e/image.png)

### new 연산자 없이 실행 했을때

```js
Person('heaeun')
```

`this`는 `전역객체`를 가리킨다
![](https://images.velog.io/images/yhe228/post/3ba075fe-a3e9-4105-93d1-be1a155a5219/image.png)

## 참고

- [How to detect if a function is called as constructor?](https://stackoverflow.com/questions/367768/how-to-detect-if-a-function-is-called-as-constructor)
