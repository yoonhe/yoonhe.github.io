---
title: this - 메서드 호출
date: 2021-10-16 12:10:13
category: javascript
thumbnail: { thumbnailSrc }
draft: false
---

## 메서드 내부의 this

메서드 내부의 this에는 `메서드를 호출한 객체`가 바인딩된다

### 예시코드

```js
const food = {
  type: 'fruit',
  fruits: {
    apple: {
      name: '사과',
      introduce: function() {
        console.log(`${this.name}는 ${this.type}이다`);
      },
    },
  },
};

food.fruits.apple.introduce();
// 사과는 undefined이다
```

- 메서드 내부의 this에는 메서드를 호출한 객체가 바인딩된다
  위의 코드에서 this는 `food.fruits.apple` 이다. `food.fruits.apple` 에는 `type` 이라는 프로퍼티가 없으므로 `this.type` 은 undefined를 반환한다

### 해결방법

type을 가지고 있는 food2 객체를 사용한다

```jsx
const food2 = {
  type: 'fruit',
  fruits: {
    apple: {
      name: '사과',
      introduce: function() {
        console.log(`${this.name}는 ${food2.type}이다`);
      },
    },
  },
};

food2.fruits.apple.introduce();
// 사과는 fruit이다
```
