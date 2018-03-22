---
title: JavaScript浮点数运算的精度问题
date: 2018-03-22 15:34:00
tags: [js]
---
在 JavaScript 中整数和浮点数都属于 Number 数据类型，所有数字都是以 64 位浮点数形式储存。当做浮点数
运算时，会发现一些问题

```javascript
// 加法 =====================
// 0.1 + 0.2 = 0.30000000000000004
// 0.7 + 0.1 = 0.7999999999999999
// 0.2 + 0.4 = 0.6000000000000001
// 2.22 + 0.1 = 2.3200000000000003
 
// 减法 =====================
// 1.5 - 1.2 = 0.30000000000000004
// 0.3 - 0.2 = 0.09999999999999998
 
// 乘法 =====================
// 19.9 * 100 = 1989.9999999999998
// 19.9 * 10 * 10 = 1990
// 1306377.64 * 100 = 130637763.99999999
// 1306377.64 * 10 * 10 = 130637763.99999999
// 0.7 * 180 = 125.99999999999999
// 9.7 * 100 = 969.9999999999999
// 39.7 * 100 = 3970.0000000000005
 
// 除法 =====================
// 0.3 / 0.1 = 2.9999999999999996
// 0.69 / 10 = 0.06899999999999999
```
<!-- more -->

简单的总结一下：

JavaScript 里的数字是采用 ``IEEE 754`` 标准的 64 位双精度浮点数。对于64位的浮点数在内存中的表示，最高的1位是符号位，接着的11位是指数，剩下的52位为有效数字，具体：

第0位：符号位， s 表示 ，0表示正数，1表示负数，决定了一个数的正负；
第1位到第11位：储存指数部分， e 表示，决定了数值的大小；
第12位到第63位：储存小数部分（即有效数字），f 表示，决定了数值的精度；

``IEEE 754``规定，有效数字第一位默认总是1，不保存在64位浮点数之中。也就是说，有效数字总是1.xx…xx的形式，其中xx..xx的部分保存在64位浮点数之中，最长可能为52位。因此，JavaScript提供的有效数字最长为53个二进制位（64位浮点的后52位+有效数字第一位的1）


```javascript

//0.1+0.2=0.30000000000000004

0.1 和 0.2 都转化成二进制后再进行运算

0.00011001100110011001100110011001100110011001100110011010 +
0.0011001100110011001100110011001100110011001100110011010 =
0.0100110011001100110011001100110011001100110011001100111

IEEE 754 标准的 64 位双精度浮点数的小数部分最多支持 53 位二进制位，所以两者相加之后得到二进制为：
0.0100110011001100110011001100110011001100110011001100 

//转成十进制正好是 0.30000000000000004

```
整数精度同样存在问题，先来看看问题：
```javascript
console.log(19571992547450991); //=> 19571992547450990
console.log(19571992547450991===19571992547450992); //=> true
```
同样的原因，在 JavaScript 中 Number类型统一按浮点数处理，整数是按最大54位来算
最大(253 - 1，Number.MAX_SAFE_INTEGER,9007199254740991) 和
最小(-(253 - 1)，Number.MIN_SAFE_INTEGER,-9007199254740991) 安全整数范围的。所以只要超过这个范围，就会存在被舍去的精度问题。


#### 解决办法

**数据展示类**

当你拿到 1.4000000000000001 这样的数据要展示时，建议使用 toPrecision 凑整并 parseFloat 转成数字后再显示，如下：
```javascript
parseFloat(1.4000000000000001.toPrecision(12)) === 1.4  // True
```
封装成方法就是：
```javascript
function strip(num, precision = 12) {
  return +parseFloat(num.toPrecision(precision));
}
```
为什么选择 12 做为默认精度？这是一个经验的选择，一般选12就能解决掉大部分0001和0009问题，而且大部分情况下也够用了，如果你需要更精确可以调高。

**数据运算类**
对于运算类操作，如 +-*/，就不能使用 toPrecision 了。正确的做法是把小数转成整数后再运算。以加法为例：
```javascript
/**
 * 精确加法
 */
function add(num1, num2) {
  const num1Digits = (num1.toString().split('.')[1] || '').length;
  const num2Digits = (num2.toString().split('.')[1] || '').length;
  const baseNum = Math.pow(10, Math.max(num1Digits, num2Digits));
  return (num1 * baseNum + num2 * baseNum) / baseNum;
}
```
以上方法能适用于大部分场景。遇到科学计数法如 2.3e+1（当数字精度大于21时，数字会强制转为科学计数法形式显示）时还需要特别处理一下。


以下是实现精确加减乘除的函数

```javascript

/**
 ** 加法函数，用来得到精确的加法结果
 ** 说明：javascript的加法结果会有误差，在两个浮点数相加的时候会比较明显。这个函数返回较为精确的加法结果。
 ** 调用：accAdd(arg1,arg2)
 ** 返回值：arg1加上arg2的精确结果
 **/
function accAdd(arg1, arg2) {
    var r1, r2, m, c;
    try {
        r1 = arg1.toString().split(".")[1].length;
    }
    catch (e) {
        r1 = 0;
    }
    try {
        r2 = arg2.toString().split(".")[1].length;
    }
    catch (e) {
        r2 = 0;
    }
    c = Math.abs(r1 - r2);
    m = Math.pow(10, Math.max(r1, r2));
    if (c > 0) {
        var cm = Math.pow(10, c);
        if (r1 > r2) {
            arg1 = Number(arg1.toString().replace(".", ""));
            arg2 = Number(arg2.toString().replace(".", "")) * cm;
        } else {
            arg1 = Number(arg1.toString().replace(".", "")) * cm;
            arg2 = Number(arg2.toString().replace(".", ""));
        }
    } else {
        arg1 = Number(arg1.toString().replace(".", ""));
        arg2 = Number(arg2.toString().replace(".", ""));
    }
    return (arg1 + arg2) / m;
}
 
//给Number类型增加一个add方法，调用起来更加方便。
Number.prototype.add = function (arg) {
    return accAdd(arg, this);
};

/**
 ** 减法函数，用来得到精确的减法结果
 ** 说明：javascript的减法结果会有误差，在两个浮点数相减的时候会比较明显。这个函数返回较为精确的减法结果。
 ** 调用：accSub(arg1,arg2)
 ** 返回值：arg1加上arg2的精确结果
 **/
function accSub(arg1, arg2) {
    var r1, r2, m, n;
    try {
        r1 = arg1.toString().split(".")[1].length;
    }
    catch (e) {
        r1 = 0;
    }
    try {
        r2 = arg2.toString().split(".")[1].length;
    }
    catch (e) {
        r2 = 0;
    }
    m = Math.pow(10, Math.max(r1, r2)); //last modify by deeka //动态控制精度长度
    n = (r1 >= r2) ? r1 : r2;
    return ((arg1 * m - arg2 * m) / m).toFixed(n);
}
 
// 给Number类型增加一个mul方法，调用起来更加方便。
Number.prototype.sub = function (arg) {
    return accMul(arg, this);
};

/**
 ** 乘法函数，用来得到精确的乘法结果
 ** 说明：javascript的乘法结果会有误差，在两个浮点数相乘的时候会比较明显。这个函数返回较为精确的乘法结果。
 ** 调用：accMul(arg1,arg2)
 ** 返回值：arg1乘以 arg2的精确结果
 **/
function accMul(arg1, arg2) {
    var m = 0, s1 = arg1.toString(), s2 = arg2.toString();
    try {
        m += s1.split(".")[1].length;
    }
    catch (e) {
    }
    try {
        m += s2.split(".")[1].length;
    }
    catch (e) {
    }
    return Number(s1.replace(".", "")) * Number(s2.replace(".", "")) / Math.pow(10, m);
}
 
// 给Number类型增加一个mul方法，调用起来更加方便。
Number.prototype.mul = function (arg) {
    return accMul(arg, this);
};

/** 
 ** 除法函数，用来得到精确的除法结果
 ** 说明：javascript的除法结果会有误差，在两个浮点数相除的时候会比较明显。这个函数返回较为精确的除法结果。
 ** 调用：accDiv(arg1,arg2)
 ** 返回值：arg1除以arg2的精确结果
 **/
function accDiv(arg1, arg2) {
    var t1 = 0, t2 = 0, r1, r2;
    try {
        t1 = arg1.toString().split(".")[1].length;
    }
    catch (e) {
    }
    try {
        t2 = arg2.toString().split(".")[1].length;
    }
    catch (e) {
    }
    with (Math) {
        r1 = Number(arg1.toString().replace(".", ""));
        r2 = Number(arg2.toString().replace(".", ""));
        return (r1 / r2) * pow(10, t2 - t1);
    }
}
 
//给Number类型增加一个div方法，调用起来更加方便。
Number.prototype.div = function (arg) {
    return accDiv(this, arg);
};
```



---
参考文章

[http://www.css88.com/archives/7340](http://www.css88.com/archives/7340)
[https://github.com/camsong/blog/issues/9](https://github.com/camsong/blog/issues/9)



Math.js
Math.js 是专门为 JavaScript 和 Node.js 提供的一个广泛的数学库。它具有灵活的表达式解析器，支持符号计算，配有大量内置函数和常量，并提供集成解决方案来处理不同的数据类型
像数字，大数字(超出安全数的数字)，复数，分数，单位和矩阵。 功能强大，易于使用。

官网：http://mathjs.org/
GitHub：https://github.com/josdejong/mathjs