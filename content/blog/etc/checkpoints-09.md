---
title: "Checkpoints 09 TIL"
date: 2019-12-24 17:05:28
category: etc
thumbnail: { thumbnailSrc }
draft: false
---

πββοΈ μ€λ μκ²λ μ¬μ€  
- κ°μ²΄μ μλ‘μ΄ κ°μ μ€μ ν΄ μ£Όλ©΄ νμ¬ μ°Έμ‘°νκ³  μλ μ£Όμκ°μ μ κ±°λκ³  λ€λ₯Έ reference typeμ κ°λ¦¬ν€κ² λλ€.

__3λ² - After the following code runs, what will be the value of x.foo?__  
ππ» x.foo === 3

```js
var x = { foo: 3 };
var y = x;
y = 2;
```

__4λ² - After the following code runs, what will be the value of myArray?__
ππ» myArray === [2, 3, 4, 5]

```js
var myArray = [2, 3, 4, 5];
var ourArray = myArray;
ourArray = [];
```

__5λ² - After the following code runs, what will be the value of myArray?__  
ππ» myArray === [2, 3, 4, 5]

```js
var myArray = [2, 3, 25, 5];
var ourArray = myArray;
ourArray[2] = 25;
ourArray = undefined;
```

__7λ² - After the following code runs, what will be the value of myArray?__  
ππ» myArray === [2, 3, 4, 5]

```js
var myArray = [2, 3, 4, 5];
function doStuff(arr) {
  arr = [];
}

doStuff(myArray);
```

__8λ² - After the following code runs, what will be the value of player.score?__   
ππ» player.score === 2 

```js
var player = { score: 3 };
function doStuff(obj) {
  obj.score = 2;
  obj = undefined;
}

doStuff(player);
```

__9λ² - After the following code runs, what will be the value of player?__  
ππ» player === undefined

```js
var player = { score: 3 };

function doStuff(obj) {
  obj = {};
}

player = doStuff(player);
```
