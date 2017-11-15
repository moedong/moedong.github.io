---
title: 变量解构赋值
date: 2017-04-03 11:10:46
tags: [es6,解构赋值]
---

ES6 允许按照一定的模式，从数组和对象中提取值，对变量进行赋值，这个被称为解构。

解构的用途
> + 交换变量的值
> + 从函数返回多个值
> + 函数参数的定义
> + 提取JSON数据
> + 函数参数的默认值
> + 遍历Map结构
> + 输入模块的指定方法

<!-- more -->

## 1、数组解构赋值

(1) 完全解构，这种写法属于 “模式匹配”，只要等号两边的模式相同，左边的变量就会被赋予对应的值。如：


``` javascript
let [foo, [[bar], baz]] = [1, [[2], 3]];
foo // 1
bar // 2
baz // 3

let [ , , third] = ["foo", "bar", "baz"];
third // "baz"

let [x, , y] = [1, 2, 3];
x // 1
y // 3

let [head, ...tail] = [1, 2, 3, 4];
head // 1
tail // [2, 3, 4]

let [x, y, ...z] = ['a'];
x // "a"
y // undefined
z // []
```

(2) 不完全解构，等号左边的模式只匹配到一部分等号右边的数组。这种情况下依然可以解构成功。

```javascript
let [x, y] = [1, 2, 3];
x // 1
y // 2

let [a, [b], d] = [1, [2, 3], 4];
a // 1
b // 2
d // 4

```
>如果等号的右边不是数组的话，就会报错。
>```javascript
// 报错
let [foo] = 1;
let [foo] = false;
let [foo] = NaN;
let [foo] = undefined;
let [foo] = null;
let [foo] = {};
```
> 事实上，只要某种数据结构具有 Iterator 接口，都可以采用数组形式的解构赋值。
```javascript
function* fibs() {
  let a = 0;
  let b = 1;
  while (true) {
    yield a;
    [a, b] = [b, a + b];
  }
}

let [first, second, third, fourth, fifth, sixth] = fibs();
sixth // 5
```
>上面代码中，fibs是一个 Generator 函数，原生具有 Iterator 接口。解构赋值会依次从这个接口获取值。



## 2、对象的解构赋值

对象解构赋值和数组的解构赋值不一样。数组的元素是按次序排列的，变量的取值由它的位置决定；而对象的属性没有次序，变量必须与属性同名，才能取到正确的值。

```javascript

//具体版
let { foo: foo, bar: bar } = { foo: "aaa", bar: "bbb" };

//简写版
let { bar, foo } = { foo: "aaa", bar: "bbb" };
foo // "aaa"
bar // "bbb"

let { baz } = { foo: "aaa", bar: "bbb" };
baz // undefined

```

如果变量名与属性名不一致，必须写下面这样。

```javascript

var { foo: baz } = { foo: 'aaa', bar: 'bbb' };
baz // "aaa"

let obj = { first: 'hello', last: 'world' };
let { first: f, last: l } = obj;
f // 'hello'
l // 'world'

```
> 对象的解构赋值机制是 先找到同名属性，然后再赋值给对应的变量。前者是匹配的模式，后者才是真正的变量。真正被赋值的应该是变量。

```javascript

let { foo: baz } = { foo: "aaa", bar: "bbb" };
baz // "aaa"
foo // error: foo is not defined



var node = {
  loc: {
    start: {
      line: 1,
      column: 5
    }
  }
};

var { loc: { start: { line }} } = node;
line // 1
loc  // error: loc is undefined
start // error: start is undefined

//这由于只有line是变量，loc和start都是模式，所以不会被赋值啦。

```
如果解构失败，变量的值等于undefined。
```javascript
let {foo} = {bar: 'baz'};
foo // undefined
```

如果解构模式是嵌套的对象，而且子对象所在的父属性不存在，那么将会报错。等号左边对象的foo属性，对应一个子对象。该子对象的bar属性，解构时会报错。原因很简单，因为foo这时等于undefined，再取子属性就会报错，请看下面的代码。如：

```javascript
let {foo: {bar}} = {baz: 'baz'};
// 报错
```

## 3、字符串的解构赋值

字符串也可以解构赋值。这是因为此时，字符串被转换成了一个类似数组的对象。类似数组的对象都有一个length属性，因此还可以对这个属性解构赋值。

```javascript

const [a, b, c, d, e] = 'hello';
a // "h"
b // "e"
c // "l"
d // "l"
e // "o"

let {length : len} = 'hello';
len // 5

```

## 4、函数参数解构赋值

函数参数的解构也可以使用默认值

```javascript

function add([x, y]){
  return x + y;
}

add([1, 2]); // 3

function move({x = 0, y = 0} = {}) {
  return [x, y];
}

move({x: 3, y: 8}); // [3, 8]
move({x: 3}); // [3, 0]
move({}); // [0, 0]
move(); // [0, 0]

```

## 5、默认值

>（1）解构赋值允许指定默认值。因为ES6内部使用严格的相等运算符（===），如果一个数组成员不严格等于undefined,默认值不会生效的。如：

```javascript

let [x = 1] = [undefined];
x // 1

let [x = 1] = [null];
x // null, 因为null不严格等于 undefined

```
>（2）如果默认值是一个表达式的话，那么表达式是惰性求值的，即只有在用到的时候，才会求值。

```javascript

function f() {
  console.log('aaa');
}

let [x = f()] = [1];

//等价于

let x;
if ([1][0] === undefined) {
  x = f();
} else {
  x = [1][0];
}
```
因为x能取到值，所以函数f根本不会执行。

## 6、具体用途

（1）交换变量的值

```javascript

let x = 1;
let y = 2;

[x, y] = [y, x];

```

（2）从函数返回多个值

```javascript

// 返回一个数组

function example() {
  return [1, 2, 3];
}
let [a, b, c] = example();

a // 1
b // 2
c // 3


// 返回一个对象

function example() {
  return {
    foo: 1,
    bar: 2
  };
}
let { foo, bar } = example();

foo // 1
bar // 2


```
（3）函数参数的定义

```javascript
// 参数是一组有次序的值
function f([x, y, z]) { ... }
f([1, 2, 3]);

// 参数是一组无次序的值
function f({x, y, z}) { ... }
f({z: 3, y: 2, x: 1});

```
（4）**提取JSON数据** ,快速提取 JSON 数据的值。解构赋值对提取JSON对象中的数据，尤其有用。

```javascript
let jsonData = {
  id: 42,
  status: "OK",
  data: [867, 5309]
};

let { id, status, data: number } = jsonData;

console.log(id, status, number);
// 42, "OK", [867, 5309]
```
（5）**函数参数的默认值。** 指定参数的默认值，就避免了在函数体内部再写var foo = config.foo || 'default foo';这样的语句。

```javascript

jQuery.ajax = function (url, {
  async = true,
  beforeSend = function () {},
  cache = true,
  complete = function () {},
  crossDomain = false,
  global = true,
  // ... more config
}) {
  // ... do stuff
};

```
（6）**遍历Map结构**, 任何部署了Iterator接口的对象，都可以用for...of循环遍历。Map结构原生支持Iterator接口，配合变量的解构赋值，获取键名和键值就非常方便。

```javascript
var map = new Map();
map.set('first', 'hello');
map.set('second', 'world');

for (let [key, value] of map) {
  console.log(key + " is " + value);
}
// first is hello
// second is world

// 获取键名
for (let [key] of map) {
  // ...
}

// 获取键值
for (let [,value] of map) {
  // ...
}
```
（7）**输入模块的指定方法**加载模块时，往往需要指定输入哪些方法。解构赋值使得输入语句非常清晰。

```javascript
const { SourceMapConsumer, SourceNode } = require("source-map");
```