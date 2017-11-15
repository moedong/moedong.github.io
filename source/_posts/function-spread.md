---
title: 函数拓展之：拓展运算符（...）
date: 2017-04-09 17:05:27
tags: [es6,...]
---

扩展运算符（spread）是三个点（...）。

它好比 rest 参数的逆运算，将**一个数组转为用逗号分隔的参数序列。**

```javascript

console.log(...[1, 2, 3])
// 1 2 3

console.log(1, ...[2, 3, 4], 5)
// 1 2 3 4 5

```
<!-- more -->

> 主要作用于函数调用，引用对象主要是数组。

```javascript


function add(x, y) {
  return x + y;
}

var numbers = [4, 38];
add(...numbers) // 42

```

## 应用

### 1、替代数组的apply方法

> 由于扩展运算符可以展开数组，所以不再需要apply方法，将数组转为函数的参数了。

```javascript
// ES5的写法
function f(x, y, z) {
  // ...
}
var args = [0, 1, 2];
f.apply(null, args);

// ES6的写法
function f(x, y, z) {
  // ...
}
var args = [0, 1, 2];
f(...args);

// ES5的写法
var arr1 = [0, 1, 2];
var arr2 = [3, 4, 5];
Array.prototype.push.apply(arr1, arr2);

// ES6的写法
var arr1 = [0, 1, 2];
var arr2 = [3, 4, 5];
arr1.push(...arr2);


```

ES5写法中，push方法的参数不能是数组，所以只好通过apply方法变通使用push方法。有了扩展运算符，就可以直接将数组传入push方法。

```javascript

// ES5的写法
var arr1 = [0, 1, 2];
var arr2 = [3, 4, 5];
Array.prototype.push.apply(arr1, arr2);

// ES6的写法
var arr1 = [0, 1, 2];
var arr2 = [3, 4, 5];
arr1.push(...arr2); // arr1.push(3,4,5);

```
### 2、合并数组

```javascript

// ES5
[1, 2].concat(more)
// ES6
[1, 2, ...more]

var arr1 = ['a', 'b'];
var arr2 = ['c'];
var arr3 = ['d', 'e'];

// ES5的合并数组
arr1.concat(arr2, arr3);
// [ 'a', 'b', 'c', 'd', 'e' ]

// ES6的合并数组
[...arr1, ...arr2, ...arr3]
// [ 'a', 'b', 'c', 'd', 'e' ]

```
### 3、与结构赋值相结合

``` javascript
const [first, ...rest] = [1, 2, 3, 4, 5];
first // 1
rest  // [2, 3, 4, 5]

const [first, ...rest] = [];
first // undefined
rest  // []:

const [first, ...rest] = ["foo"];
first  // "foo"
rest   // []


/*如果将扩展运算符用于数组赋值，只能放在参数的最后一位，否则会报错。*/

const [...butLast, last] = [1, 2, 3, 4, 5];
// 报错

const [first, ...middle, last] = [1, 2, 3, 4, 5];
// 报错

```

### 4、字符串，字符串转为真正的数组。

```javascript
[...'hello']
// [ "h", "e", "l", "l", "o" ]
```

### 5、实现了Iterator接口的对象

> 任何Iterator接口的对象，都可以用扩展运算符转为真正的数组。
```javascript
var nodeList = document.querySelectorAll('div');
var array = [...nodeList];
```
> 上面代码中，querySelectorAll方法返回的是一个nodeList对象。它不是数组，而是一个类似数组的对象。这时，扩展运算符可以将其转为真正的数组，原因就在于NodeList对象实现了Iterator接口。

---

> 对于那些没有部署Iterator接口的类似数组的对象，扩展运算符就无法将其转为真正的数组。
```javascript
let arrayLike = {
  '0': 'a',
  '1': 'b',
  '2': 'c',
  length: 3
};

// TypeError: Cannot spread non-iterable object.
let arr = [...arrayLike];
```
> 上面代码中，arrayLike是一个类似数组的对象，但是没有部署Iterator接口，扩展运算符就会报错。这时，可以改为使用Array.from方法将arrayLike转为真正的数组。

### 6、Map和Set结构，Generator函数

扩展运算符内部调用的是数据结构的Iterator接口，因此只要具有Iterator接口的对象，都可以使用扩展运算符，比如Map结构。

```javascript
let map = new Map([
  [1, 'one'],
  [2, 'two'],
  [3, 'three'],
]);

let arr = [...map.keys()]; // [1, 2, 3]

```