---
title: 重新认识定时器
date: 2017-04-20 10:06:33
tags: [js,setTimeou,setInterval]
---

### 重新认识一

> 一般，`setTimeout`函数接受两个参数，第一个参数func|code是将要推迟执行的函数名或者一段代码（引擎内部使用eval函数，将字符串转为代码），第二个参数delay是推迟执行的毫秒数。但是，`setTimeout` 还可以添加`更多参数`。第二个之后的参数都将作为 推迟执行函数的 参数 传入。

```javascript

// 传入4个参数

setTimeout(function(a,b){

  // a=1,b=2
  console.log(a+b);

},1000,1,2);

// 3

```
<!-- more -->

### 重新认识二

>IE 9.0及以下版本，只允许setTimeout有两个参数，不支持更多的参数。这时有三种解决方法。

第一种是在一个匿名函数里面，让回调函数带参数运行，再把匿名函数输入setTimeout。
```javascript
setTimeout(function() {
  myFunc("one", "two", "three");
}, 1000);
```
上面代码中，myFunc是真正要推迟执行的函数，有三个参数。如果直接放入setTimeout，低版本的IE不能带参数，所以可以放在一个匿名函数。


第二种解决方法是使用bind方法，把多余的参数绑定在回调函数上面，生成一个新的函数输入setTimeout。
```javascript
setTimeout(function(arg1){}.bind(undefined, 10), 1000);
```
上面代码中，bind方法第一个参数是undefined，表示将原函数的this绑定全局作用域，第二个参数是要传入原函数的参数。它运行后会返回一个新函数，该函数不带参数。

### 重新认识三

>如果被setTimeout推迟执行的`回调函数是某个对象的方法`，那么该方法中的`this`关键字将`指向全局环境`，而不是定义时所在的那个对象。

举例1

```javascript

var x = 1;

var o = {
  x: 2,
  y: function(){
    console.log(this.x);
  }
};

setTimeout(o.y,1000);
// 1
// 上面代码输出的是1，而不是2，这表示o.y的this所指向的已经不是o，而是全局环境了。

```
---

举例2

```javascript

function User(login) {
  this.login = login;
  this.sayHi = function() {
    console.log(this.login);
  }
}

var user = new User('John');

setTimeout(user.sayHi, 1000);

// undefined
// 上面代码只会显示undefined，因为等到user.sayHi执行时，它是在全局对象中执行，所以this.login取不到值。

```
解决办法：

方法一 将user.sayHi放在函数中执行，sayHi是在user作用域内执行，而不是在全局作用域内执行，所以能够显示正确的值

```javascript
setTimeout(function() {
  user.sayHi();
}, 1000);
```
方法二  使用bind方法，将绑定sayHi绑定在user上面
```javascript
setTimeout(user.sayHi.bind(user), 1000);
```

### 重新认识四

> HTML 5标准规定，`setTimeout的最短时间间隔是4毫秒。`为了节电，对于那些`不处于当前窗口的页面，浏览器会将时间间隔扩大到1000毫秒`。另外，如果笔记本电脑处于电池供电状态，Chrome和IE 9以上的版本，会将时间间隔切换到系统定时器，大约是15.6毫秒。`setInterval的最短间隔时间是10毫秒`，也就是说，小于10毫秒的时间间隔会被调整到10毫秒。

### 重新认识五

> * setInterval函数的用法与setTimeout完全一致。
> * `setInterval`指定的是“开始执行”之间的间隔，并不考虑每次任务执行本身所消耗的时间。因此实际上，两次执行之间的间隔会小于指定的时间。比如，`setInterval`指定每`100ms`执行一次，每次执行需要`5ms`，那么`第一次执行结束后95毫秒`，第二次执行就会开始。`如果某次执行耗时特别长，比如需要105毫秒，那么它结束后，下一次执行就会立即开始`。

>* 为了确保两次执行之间有固定的间隔，可以不用setInterval，而是每次执行结束后，使用setTimeout指定下一次执行的具体时间。

写个demo,确保 下一个对话框总是在关闭上一个对话框之后2000毫秒弹出：

```javascript

var i=1;
var timer=setTimeout(function(){

    alert(i);
    timer =setTimeout(arguments.callee,2000);

},2000)

```
用setTimeout模拟了setInterval

```javascript
function interval(func,wait){

    var interv=function(){
        func.call();
        setTimeout(interv,wait);
    }

    setTimeout(interv,wait);
}

interval(function(){console.log(1)},1000)

```

### 重新认识六

> setTimeout和setInterval返回的整数值是连续的，也就是说，第二个setTimeout方法返回的整数值，将比第一个的整数值大1。利用这一点，可以写一个函数，取消当前所有的setTimeout。

clearTimeout实际应用的例子。有些网站会实时将用户在文本框的输入，通过Ajax方法传回服务器，jQuery的写法如下。

```javascript

$('textarea').on('keydown', ajaxAction);

```
这样写有一个很大的缺点，就是如果用户连续击键，就会连续触发keydown事件，造成大量的Ajax通信。这是不必要的，而且很可能会发生性能问题。正确的做法应该是，设置一个门槛值，表示两次Ajax通信的最小间隔时间。如果在设定的时间内，发生新的keydown事件，则不触发Ajax通信，并且重新开始计时。如果过了指定时间，没有发生新的keydown事件，将进行Ajax通信将数据发送出去。

debounce 防抖动方法

```javascript

function debounce(fn, delay){

  var timer = null; // 声明计时器

  return function(){

    //保存当前作用域的 this和 arguments
    var context = this;
    var args = arguments;

    clearTimeout(timer);
    timer = setTimeout(function(){

      fn.apply(context, args);

    }, delay);

  };
}

// 用法示例
$('textarea').on('keydown', debounce(ajaxAction, 2500))

```

### 重新认识七

> * setTimeout和setInterval的运行机制是，将`指定的代码移出本次执行`，`等到下一轮Event Loop`时，再检查是否到了指定时间。如果到了，就执行对应的代码；如果不到，就等到再下一轮Event Loop时重新判断。

> * 这意味着，`setTimeout和setInterval指定的代码，必须等到本轮Event Loop的所有同步任务都执行完，再等到本轮Event Loop的“任务队列”的所有任务执行完，才会开始执行`。由于前面的任务到底需要多少时间执行完，是不确定的，所以没有办法保证，setTimeout和setInterval指定的任务，一定会按照预定时间执行。

> * `setIntervel具有累积效应`，如果某个操作特别耗时，`超过了setInterval的时间间隔，排在后面的操作会被累积起来，然后在很短的时间内连续触发`，这可能或造成性能问题（比如集中发出Ajax请求）。

```javascript

setInterval(function () {
  console.log(2);
}, 1000);

(function () {
  sleeping(3000);
})();

// 2,2,2
// 2
// ...

结果就是等到第二行语句运行完成以后，立刻连续输出三个2，然后开始每隔1000毫秒，输出一个2。

```

### 重新认识八

> * 等到当前脚本的同步任务和“任务队列”中已有的事件，全部处理完以后，才会执行`setTimeout`指定的任务。

> * 也就是说，setTimeout的真正作用是，在“消息队列”的现有消息的后面再添加一个消息，规定在指定时间执行某段代码。setTimeout添加的事件，会在下一次Event Loop执行。

> *  `setTimeout(f, 0)`将第二个参数设为0，作用是让f在现有的任务（脚本的同步任务和“消息队列”指定的任务）一结束就立刻执行。

> * 也就是说，setTimeout(f, 0)的作用是，尽可能早地执行指定的任务。而并不是会立刻就执行这个任务。

> * setTimeout(f, 0)指定的任务，最早也要到下一次Event Loop才会执行

```javascript

setTimeout(function() {
  console.log("Timeout");
}, 0);

function a(x) {
  console.log("a() 开始运行");
  b(x);
  console.log("a() 结束运行");
}

function b(y) {
  console.log("b() 开始运行");
  console.log("传入的值为" + y);
  console.log("b() 结束运行");
}

console.log("当前任务开始");
a(42);
console.log("当前任务结束");

// 当前任务开始
// a() 开始运行
// b() 开始运行
// 传入的值为42
// b() 结束运行
// a() 结束运行
// 当前任务结束
// Timeout

```

### 重新认识九

> 可以调整事件的发生顺序。

例子1

网页开发中，某个事件先发生在子元素，然后冒泡到父元素，即子元素的事件回调函数，会早于父元素的事件回调函数触发。如果，我们先让父元素的事件回调函数先发生，就要用到setTimeout(f, 0)。

```javascript

var input = document.getElementsByTagName('input[type=button]')[0];

input.onclick = function A() {
  setTimeout(function B() {
    input.value +=' input';
  }, 0)
};

document.body.onclick = function C() {
  input.value += ' body'
};

上面代码在点击按钮后，先触发回调函数A，然后触发函数C。在函数A中，setTimeout将函数B推迟到下一轮Loop执行，这样就起到了，先触发父元素的回调函数C的目的了。

```

例子2

用户在输入框输入文本，keypress事件会在浏览器接收文本之前触发。想在用户输入文本后，立即将字符转为大写。但是实际上，它只能将上一个字符转为大写，因为浏览器此时还没接收到文本。

```javascript

document.getElementById('my-ok').onkeypress = function() {
  var self = this;
  setTimeout(function() {
    self.value = self.value.toUpperCase();
  }, 0);
}

```

例子3

```javascript

var div = document.getElementsByTagName('div')[0];

// 写法一  造成浏览器“堵塞”，因为JavaScript执行速度远高于DOM，会造成大量DOM操作“堆积”

for (var i = 0xA00000; i < 0xFFFFFF; i++) {
  div.style.backgroundColor = '#' + i.toString(16);
}




// 写法二
var timer;
var i=0x100000;

function func() {
  timer = setTimeout(func, 0);
  div.style.backgroundColor = '#' + i.toString(16);
  if (i++ == 0xFFFFFF) clearTimeout(timer);
}

timer = setTimeout(func, 0);

```

### 重新认识十

正常任务（task）与微任务（microtask）。它们的区别在于，“正常任务”在下一轮Event Loop执行，“微任务”在本轮Event Loop的所有任务结束后执行。


正常任务：

> setTimeout
> setInterval
> setImmediate
> I/O
> 各种事件（比如鼠标单击事件）的回调函数

微任务：

> process.nextTick 
> Promise

```javascript

console.log(1);

setTimeout(function() {
  console.log(2);
}, 0);

Promise.resolve().then(function() {
  console.log(3);
}).then(function() {
  console.log(4);
});

console.log(5);

// 1
// 5
// 3
// 4
// 2

```
