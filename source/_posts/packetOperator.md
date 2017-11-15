---
title: 分组操作符
date: 2017-02-05 17:51:40
tags: [(),js]
---

> 括号() 是一个分组操作符，它的内部只能包含表达式（由 运算元 和 运算符(可选) 构成，并产生运算结果的语法结构）,不能包含语句 


---

函数表达式和函数声明，两者如何区别：

函数声明:

&emsp;&emsp;function 函数名称 (参数：可选){ 函数体 }

函数表达式：

&emsp;&emsp;function (参数：可选){ 函数体 }

<!-- more -->

如果不声明函数名称，它肯定是表达式。

如果声明了函数名称的话，ECMAScript是通过上下文来区分的：

&emsp;&emsp;如果function foo(){}是作为**赋值表达式**的一部分的话，那它就是一个函数表达式，
&emsp;&emsp;如果function foo(){}被**包含在一个函数体内**，或者位于**程序的最顶部**的话，那它就是一个函数声明。

```javascript

function foo(){} // 声明，因为它是程序的一部分

var bar = function foo(){}; // 表达式，因为它是赋值表达式的一部分

new function bar(){}; // 表达式，因为它是new表达式

(function(){
    function bar(){} // 声明，因为它是函数体的一部分
})();

(function foo(){}); // 函数表达式：包含在分组操作符内


(var x = 5); // Unexpected token var。  分组操作符，只能包含表达式而不能包含语句：这里的var就是语句


```

>在一个`表达式`后面加上括号()，该表达式会`立即执行`，但是在一个`语句`后面加上括号()，是完全不一样的意思，他的`只是分组操作符`

```javascript

// 下面这个function在语法上是没问题的，但是依然只是一个语句
// 加上括号()以后依然会报错，因为分组操作符需要包含表达式
 
function foo(){ /* code */ }(); // SyntaxError: Unexpected token )
 
// 但是如果你在括弧()里传入一个表达式，将不会有异常抛出
// 但是foo函数依然不会执行
function foo(){ /* code */ }( 1 );
 
// 因为它完全等价于下面这个代码，一个function声明后面，又声明了一个毫无关系的表达式： 
function foo(){ /* code */ }
 
( 1 );

```

>JavaScript里括弧()里面不能包含语句，所以在这一点上，解析器在解析function关键字的时候，会将相应的代码解析成function表达式，而不是function声明

```javascript

// 由于括弧()和JS的&&，异或，逗号等操作符是在函数表达式和函数声明上消除歧义的
// 所以一旦解析器知道其中一个已经是表达式了，其它的也都默认为表达式了
// 不过，请注意下一章节的内容解释

var i = function () { return 10; } ();
true && function () { /* code */ } ();
0, function () { /* code */ } ();

// 如果你不在意返回值，或者不怕难以阅读
// 你甚至可以在function前面加一元操作符号

!function () { /* code */ } ();
~function () { /* code */ } ();
-function () { /* code */ } ();
+function () { /* code */ } ();

```