---
title: 对象的拓展
date: 2017-04-18 10:55:33
tags: [es6,object]
---

## 一、属性的简洁表示法

ES6允许直接写入`变量`和`函数` 作为`对象`的`属性和方法`。

直接写变量时，属性名为变量名，属性值为变量值。

```javascript

//例子1，属性简写

var foo = 'bar';
var baz = {foo};
baz // {foo: "bar"}

// 等同于
var baz = {foo: foo};

//例子2，属性简写

function f(x, y) {
  return {x, y};
}

// 等同于
function f(x, y) {
  return {x: x, y: y};
}
f(1, 2) // Object {x: 1, y: 2}

//例子3,方法简写

var o = {
  method() {
    return "Hello!";
  }
};

// 等同于

var o = {
  method: function() {
    return "Hello!";
  }
};


```
<!-- more -->

## 二、属性名表达式

js定义对象的属性有两种方法，一种是 直接用标识符作为属性，另一种是 用表达式作为属性名，这时要将表达式放在方括号之内。

```javascript

// 方法一
obj.foo = true;

// 方法二
obj['a' + 'bc'] = 123;

```

> 使用字面量方式定义对象时（使用大括号），在ES5中只能使用第一种方法（标识符）定义属性。ES6允许字面量定义对象时，用第二种方法（表达式）作为对象的属性名，即把表达式放在方括号内。

```javascript

//ES5

var ojb={
    foo:true,
    abc:123
}


//ES6

let proKey ="foo";

let obj={
    [proKey]:true,
    ['a'+'bc']:123
};

//另一个例子

var lastWord = 'last word';

var a = {
  'first word': 'hello',
  [lastWord]: 'world'
};

a['first word'] // "hello"
a[lastWord] // "world"
a['last word'] // "world"


//表达式还可以用于定义方法名。

let obj = {
  ['h' + 'ello']() {
    return 'hi';
  }
};

obj.hello() // hi

```

## 三、Object.assign()

`Object.assign`方法用于对象的合并，将源对象的所有可枚举属性，复制到目标对象（target）。其第一个参数是目标对象，后面的参数都是源对象。`如果目标对象与源对象有同名属性，或多个源对象有同名属性，则后面的属性会覆盖前面的属性。`

```javascript

var target = { a: 1, b: 1 };

var source1 = { b: 2, c: 2 };
var source2 = { c: 3 };

Object.assign(target, source1, source2);
target // {a:1, b:2, c:3}


```

> 如果只有一个参数，Object.assign会直接返回该参数。

```javascript

var obj = {a: 1};
Object.assign(obj) === obj // true

```

> 如果该参数不是对象，则会先转成对象，然后返回。

```javascript

typeof Object.assign(2) // "object"

```

> 由于undefined和null无法转成对象，所以如果它们作为参数，就会报错。

```javascript

Object.assign(undefined) // 报错
Object.assign(null) // 报错

```

>如果非对象参数出现在源对象的位置（即非首参数），那么处理规则有所不同。首先，这些参数都会转成对象，如果无法转成对象，就会跳过。这意味着，如果undefined和null不在首参数，就不会报错。

```javascript

let obj = {a: 1};
Object.assign(obj, undefined) === obj // true
Object.assign(obj, null) === obj // true

```
---

> ** 特别注意 **
Object.assign方法实行的是浅拷贝，而不是深拷贝。也就是说，如果源对象某个属性的值是对象，那么目标对象拷贝得到的是这个对象的引用。
```javascript
var obj1 = {a: {b: 1}};
var obj2 = Object.assign({}, obj1);

obj1.a.b = 2;
obj2.a.b // 2
```
>上面代码中，源对象obj1的a属性的值是一个对象，Object.assign拷贝得到的是这个对象的引用。这个对象的任何变化，都会反映到目标对象上面。

>对于这种嵌套的对象，一旦遇到同名属性，Object.assign的处理方法是替换，而不是添加。
```javascript
var target = { a: { b: 'c', d: 'e' } }
var source = { a: { b: 'hello' } }
Object.assign(target, source)
// { a: { b: 'hello' } }
```

### Object.assign()的多用途

#### 1、为对象添加属性
```javascript
class Point {
  constructor(x, y) {
    Object.assign(this, {x, y});
  }
}
```
上面方法通过Object.assign方法，将x属性和y属性添加到Point类的对象实例。

#### 2、为对象添加方法
```javascript
Object.assign(SomeClass.prototype, {
  someMethod(arg1, arg2) {
    ···
  },
  anotherMethod() {
    ···
  }
});

// 等同于下面的写法
SomeClass.prototype.someMethod = function (arg1, arg2) {
  ···
};
SomeClass.prototype.anotherMethod = function () {
  ···
};
```
上面代码使用了对象属性的简洁表示法，直接将两个函数放在大括号中，再使用assign方法添加到SomeClass.prototype之中。

#### 3、克隆对象
```javascript
function clone(origin) {
  return Object.assign({}, origin);
}
```
上面代码将原始对象拷贝到一个空对象，就得到了原始对象的克隆。

不过，采用这种方法克隆，只能克隆原始对象自身的值，不能克隆它继承的值。如果想要保持继承链，可以采用下面的代码。

```javascript
function clone(origin) {
  let originProto = Object.getPrototypeOf(origin);
  return Object.assign(Object.create(originProto), origin);
}
```
#### 4、合并多个对象

将多个对象合并到某个对象。

```javascript

const merge =(target, ...sources) => Object.assign(target, ...sources);

```
如果希望合并后返回一个新对象，可以改写上面函数，对一个空对象合并。

```javascript
const merge =(...sources) => Object.assign({}, ...sources);
```

#### 5、为属性指定默认值
```javascript
const DEFAULTS = {
  logLevel: 0,
  outputFormat: 'html'
};

function processContent(options) {
  options = Object.assign({}, DEFAULTS, options);
  console.log(options);
  // ...
}
```
上面代码中，DEFAULTS对象是默认值，options对象是用户提供的参数。Object.assign方法将DEFAULTS和options合并成一个新对象，如果两者有同名属性，则option的属性值会覆盖DEFAULTS的属性值。


### 四、属性遍历

遍历对象的属性，都遵守同样的属性遍历的次序规则。

>* 首先遍历所有属性名为数值的属性，按照数字排序。
>* 其次遍历所有属性名为字符串的属性，按照生成时间排序。
>* 最后遍历所有属性名为Symbol值的属性，按照生成时间排序。


常用的五种方法:

（1）for...in

> for...in循环遍历对象自身的和继承的可枚举属性（不含Symbol属性）。

（2）Object.keys(obj)

> Object.keys返回一个数组，包括对象自身的（不含继承的）所有可枚举属性（不含Symbol属性）。

（3）Object.getOwnPropertyNames(obj)

> Object.getOwnPropertyNames返回一个数组，包含对象自身的所有属性（不含Symbol属性，但是包括不可枚举属性）。

（4）Object.getOwnPropertySymbols(obj)

> Object.getOwnPropertySymbols返回一个数组，包含对象自身的所有Symbol属性。

（5）Reflect.ownKeys(obj)

> Reflect.ownKeys返回一个数组，包含对象自身的所有属性，不管是属性名是Symbol或字符串，也不管是否可枚举。


### 五、Object.keys()

ES5 引入了Object.keys方法，返回一个数组，成员是参数对象自身的（不含继承的）所有可遍历（enumerable）属性的键名。

```javascript

var obj = { foo: 'bar', baz: 42 };
Object.keys(obj)
// ["foo", "baz"]

```

### 六、Object.values()

Object.values方法返回一个数组，成员是参数对象自身的（不含继承的）所有可遍历（enumerable）属性的键值。

基本用途：

> 返回一个包含所有属性值的数组，返回数组的成员顺序，跟`Object.keys()`排列规则一致
> 一个字符串，会返回各个字符组成的一个数组


```javascript

var obj = { foo: 'bar', baz: 42 };
Object.values(obj)
// ["bar", 42]


var obj = { 100: 'a', 2: 'b', 7: 'c' };
Object.values(obj)
// ["b", "c", "a"]

//上面代码中，属性名为数值的属性，是按照数值大小，从小到大遍历的，因此返回的顺序是b、c、a。

```


如果Object.values方法的参数是一个字符串，会返回各个字符组成的一个数组。

```javascript
Object.values('foo')
// ['f', 'o', 'o']
```

### 七、Object.entries

`Object.entries`方法返回一个数组，成员是参数对象本身的(不含继承)的所有可以遍历的属性的键值对数组。

基本用途：
> 1、遍历对象的属性
> 2、将对象转为真正的Map结构

遍历对象的属性
```javascript
let obj={one:1,two:2}
for(let [k,v] of Object.entries(obj)){
    console.log(`${JSON.stringify(k)}:${JSON.stringify(v)}`)
}
// "one":1
// "two":2

```

将对象转为真正的Map结构
```javascript
var obj={foo:'bar',baz:'1'}
var map=new Map(Object.entries(obj));
map // {foo:'bar',baz:'1'}
```
