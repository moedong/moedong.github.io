---
title: 函数拓展之：rest参数（...变量名）
date: 2017-04-07 17:04:54
tags: [es6,rest]
---

ES6 引入rest参数（...变量名）用于获取函数多余的参数。
rest参数是一个数组，将多余的参数放入到数组中。平时可以使用rest参数来代替arguments变量。
**rest 参数之后不能再有其他参数（即只能是最后一个参数），否则会报错。**

```javascript

function add(...values) {
  let sum = 0;

  for (var val of values) {
    sum += val;
  }

  return sum;
}

add(2, 5, 3) // 10

```
<!-- more -->

rest 参数代替arguments变量的例子

```javascript

// arguments变量的写法
function sortNumbers() {
  return Array.prototype.slice.call(arguments).sort();
}

// rest参数的写法
const sortNumbers = (...numbers) => numbers.sort();

```

rest 参数中的变量代表一个数组，所以数组特有的方法都可以用于这个变量。利用 rest 参数改写数组push方法的例子:

```javascript

function push(array, ...items) {
  items.forEach(function(item) {
    array.push(item);
    console.log(item);
  });
}

var a = [];
push(a, 1, 2, 3)

```
> * rest 参数之后不能再有其他参数（即只能是最后一个参数），否则会报错。
```javascript
// 报错
function f(a, ...b, c) {
  // ...
}
```
>* 函数的length属性，不包括 rest 参数。
```javascript

(function(a) {}).length  // 1
(function(...a) {}).length  // 0
(function(a, ...b) {}).length  // 1

```