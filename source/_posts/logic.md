---
title: 逻辑思维
date: 2016-12-25 00:34:46
tags: [js]
---

#### 逻辑1

>实现判断传入的两个数组是否相似。具体需求：
> 1. 数组中的成员类型相同，顺序可以不同。例如[1, true] 与 [false, 2]是相似的。
> 2. 数组的长度一致。
> 3. 类型的判断范围，需要区分:String, Boolean, Number, undefined, null, 函数，日期, window.

<!-- more -->
```java

思路：

1、判断传入的参数是否为数组 (使用 instanceof 方法)
2、检查两个数组长度是否一致
3、分别判断数组内元素的基本数据类型 (使用 typeof 方法)
4、因为 typeof 只能检查基本数据类型，对于 null, Date, window 返回的都是 object，所以使用 Object.prototype.toString.apply() 来检查这些对象类型，其返回值为：'[object Null]', '[object Date]', '[object global]'
5、分别比较每个数组内元素的各种类型的个数，如果都相等，那么这两个数组是相似的。

```

```javascript

 /*
 * param1 Array 
 * param2 Array
 * return true or false
 */
function arraysSimilar(arr1, arr2){
    if(arr1 instanceof Array && arr2 instanceof Array){
        var key1 = [],key2 = [],len = arr1.length,len2=arr2.length;
        // 数组的长度相等判断
        if(len!=len2){return false;}
        // 类型相同判断
        if(len){
            // 获取类型列表
            for(var i= 0;i<len;i++){
                // 数组1的类型列表字串
                var item1 = arr1[i], typeFirst = typeOf(item1);
                if(key1.join().indexOf(typeFirst)<0){
                    key1.push(typeFirst);
                }
                
                // 数组2的类型列表字串
                var item2 = arr2[i],typeSecond = typeOf(item2);
                if(key2.join().indexOf(typeSecond)<0){
                    key2.push(typeSecond);
                } 
            }
            key1 = key1.sort();
            key2 = key2.sort();
            // 类型字串比较
            if(key1.join() == key2.join()){
                return true;
            }else{
                return false;
            }
        }else{
            // 空数组相等
            return true;
        }
    }else{
        // 非数组
        return false;
    }

}

/**
 * 类型判断方法
 * param item 
 * return type(string,function,boolean,number,undefined,null,window,Date,Array,object)
 */
function typeOf(item){
    var type = typeof item;
    if(type != "object"){
        // 判断基本类型string,function,boolean,number,undefine
    }else if(item === null){
        // check null
        type = "null";
    }else if(item === window){
        // check window
        type ="window";
    }else{
        // 判断object类型object,date,array
        if(item instanceof Date){
            type = "date";
        }else if(item instanceof Array){
            type = 'array';
        }else{
            type = 'object';
        }
    }
    return type;
}
        
```

#### 逻辑2

>把一个数组arr按照指定的数组大小size分割成若干个数组块。
>例如:  chunk([1,2,3,4],2)=[[1,2],[3,4]];   chunk([1,2,3,4,5],2)=[[1,2],[3,4],[5]];

```javascript

function chunk(arr, size) {
    var arr1 = [];
    for (var i = 0; i < arr.length; i = i + size) {
        var arr2 = arr;
        arr1.push(arr2.slice(i, i + size));
    }
    return arr1;
}

chunk(["a", "b", "c", "d"], 2);

```

#### 逻辑3

>实现一个摧毁(destroyer)函数，第一个参数是待摧毁的数组，其余的参数是待摧毁的值。如：
>destroyer([1, 2, 3, 1, 2, 3], 2, 3) 应该返回 [1, 1].
destroyer([1, 2, 3, 5, 1, 2, 3], 2, 3) 应该返回 [1, 5, 1].

```javascript

function destroyer(arr) {
  // Remove all the values
  var tempArguments = arguments;
  return arr.filter(function(entry) {
    for(var i = 1; i< tempArguments.length; i++) {
      if (entry == tempArguments[i]) {
        return false;
      }
    }
    return true;
  });
}

destroyer([1, 2, 3, 1, 2, 3], 2, 3);


```

#### 逻辑4

>先给数组排序，然后找到指定的值在数组的位置，最后返回位置对应的索引。
举例：where([1,2,3,4], 1.5) 应该返回 1。因为1.5插入到数组[1,2,3,4]后变成[1,1.5,2,3,4]，而1.5对应的索引值就是1。

```javascript

function where(arr, num) {
  // Find my place in this sorted array.
  //注意sort() 排序规则
 arr.sort(function(a,b){
      return a- b;
  });

  for(var i =0;i<arr.length;i++){
      
    if(arr[i]>num | arr[i] == num){
        
      return i;
    }
  }
  return arr.length;
}

where([40, 60], 50);

```

#### 逻辑5

>移位密码也就是密码中的字母会按照指定的数量来做移位。

>一个常见的案例就是ROT13密码，字母会移位13个位置。由'A' ↔ 'N', 'B' ↔'O'，以此类推。

>写一个ROT13函数，实现输入加密字符串，输出解密字符串。

>所有的字母都是大写，不要转化任何非字母形式的字符(例如：空格，标点符号)，遇到这些特殊字符，跳过它们。如：
rot13("SERR PBQR PNZC") 应该解码为 "FREE CODE CAMP"
rot13("SERR CVMMN!") 应该解码为 "FREE PIZZA!"

```javascript

function rot13(str) { // LBH QVQ VG!
    var arr = str.toUpperCase().split(" ");
    var str1 = [];
    for (var i = 0; i < arr.length; i++) {
        var arr1 = arr[i].split("");
        for (var j = 0; j < arr1.length; j++) {
            var num = arr1[j].charCodeAt();
            if (num >= 65 && num <= 90) {
                arr1[j] = num + 13 > 90 ? String.fromCharCode(64 + (num + 13 - 90)):String.fromCharCode(num + 13);  //64 + (num + 13 - 90) 要明白为什么是64 ，
            }

        }
        str1.push(arr1.join(""));
    }
    return str1.join(" ");
}

// Change the inputs below to test
rot13("SERR PBQR PNZC");


```

#### 逻辑6

>数组随机排序

```javascript

/* Fisher–Yates shuffle */
Array.prototype.shuffle = function() {
    var input = this;

    for (var i = input.length-1; i >=0; i--) {

        var randomIndex = Math.floor(Math.random()*(i+1));
        var itemAtIndex = input[randomIndex];

        input[randomIndex] = input[i];
        input[i] = itemAtIndex;
    }
    return input;
}

[1,2,3,4,5,6,7,8].shuffle()

//[4, 6, 3, 2, 5, 1, 7, 8] // 每次结果都是随机的


```

####  逻辑7

>数组去重

```javascript

Array.prototype.unique = function(){
 var res = [];
 var json = {};
 for(var i = 0; i < this.length; i++){
  if(!json[this[i]]){
   res.push(this[i]);
   json[this[i]] = 1;
  }
 }
 return res;
}

var arr = [112,112,34,'你好',112,112,34,'你好','str','str1'];


```

#### 逻辑8

>旋转字符串


```javascript

//先把字符串转化成数组，再借助数组的reverse方法翻转数组顺序，最后把数组转化成字符串。

function reverseString(str) {
  str = str.split('').reverse().join('');
 
  return str;
}

reverseString("hello");

```

#### 逻辑9

>确保字符串的每个单词首字母都大写，其余部分小写。（eg:titleCase("I'm a little tea pot") 应该返回 "I'm A Little Tea Pot".   titleCase("sHoRt AnD sToUt") 应该返回 "Short And Stout".）

```javascript

function titleCase(str) {
 str = str.split(" ");//按照空格把字符串分割成数组
    for (var i = 0; i < str.length; i++) {
        str[i] = str[i].toLowerCase();
        str[i] = str[i].substring(0, 1).toUpperCase() + str[i].substring(1);
    }
    return str.join(" ");//通过空格把数组连接成字符串
}

titleCase("I'm a little tea pot");

```

####  逻辑10

>找出最长的单词

```javascript

//基本答案

functionfindLongestWord(str){
    // 按照空格分割字符串，生成数组
    var strArr = str.split(" ");
    // 初始化 length 为 0
    var length = 0;

    for (var i = 0; i < strArr.length; i++) {
        // 遍历过程中，若当前字符串长度比 length 大，就更新 length
        if (strArr[i].length > length) {
            length = strArr[i].length;
        }
        // 不需要 else，因为如果比 length 小，继续执行遍历就可以了
    }
    
    // 循环结束，返回 length 作为结果
    return length;
}

// 优化，数组内置方法 .reduce() 来实现

functionfindLongestWord(str){
    var stringArr = str.split(" ");

    return stringArr.reduce(function(prev, next){
        // 返回值为参数与当前字符串中较大的数
        // 返回值会作为下次计算的 prev 传入
        return Math.max(prev, next.length);
    }, 0)
}

// 再优化，

functionfindLongestWord(str){
    return Math.max.apply(null, str.split(" ").map(function(e){
        return e.length;
    }))
}

```

#### 逻辑11

>如果数组第一个字符串元素包含了第二个字符串元素的所有字符，函数返回true。举例:

>["hello", "Hello"] 应该返回true，因为在忽略大小写的情况下，第二个字符串的所有字符都可以在第一个字符串找到。

>["hello", "hey"] 应该返回false，因为字符串"hello"并不包含字符"y"。

>["Alien", "line"] 应该返回true，因为"line"中所有字符都可以在"Alien"找到。


```javascript

function mutation(arr) {
  var a = arr[0].toLowerCase();
  var b = arr[1].toLowerCase();
  for(var i = 0; i < b.length; i++){
    if(a.indexOf(b[i]) < 0){
      return false;
    }
  }
  return true;
}


```

#### 逻辑12

>生成a-b之间的随机数

```javascript

function getRandomInt(min, max) {
  return Math.floor(Math.random() * (max - min + 1)) + min;
}

getRandomInt(a,b) // 5


```

#### 逻辑13

>生成n个随机字符

```javascript

function random_str(length) {
  var ALPHABET = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ';
  ALPHABET += 'abcdefghijklmnopqrstuvwxyz';
  ALPHABET += '0123456789-_';
  var str = '';
  for (var i=0; i < length; ++i) {
    var rand = Math.floor(Math.random() * ALPHABET.length);
    str += ALPHABET.substring(rand, rand + 1);
  }
  return str;
}

random_str(6) // "NdQKOr"

```

#### 逻辑14

>冒泡排序

```javascript

function bubbleSort(arr) {
    var len = arr.length;
    for (var i = 0; i < len; i++) {
        for (var j = 0; j < len - 1 - i; j++) {
            if (arr[j] > arr[j+1]) {        //相邻元素两两对比
                var temp = arr[j+1];        //元素交换
                arr[j+1] = arr[j];
                arr[j] = temp;
            }
        }
    }
    return arr;
}

```

#### 逻辑15

>选择排序

```javascript

function selectionSort(arr) {
    var len = arr.length;
    var minIndex, temp;
    for (var i = 0; i < len - 1; i++) {
        minIndex = i;
        for (var j = i + 1; j < len; j++) {
            if (arr[j] < arr[minIndex]) {     //寻找最小的数
                minIndex = j;                 //将最小数的索引保存
            }
        }
        temp = arr[i];
        arr[i] = arr[minIndex];
        arr[minIndex] = temp;
    }
    return arr;
}

```

####  逻辑16

>插入排序

```javascript

function insertionSort(arr) {
    var len = arr.length;
    var preIndex, current;
    for (var i = 1; i < len; i++) {
        preIndex = i - 1;
        current = arr[i];
        while(preIndex >= 0 && arr[preIndex] > current) {
            arr[preIndex+1] = arr[preIndex];
            preIndex--;
        }
        arr[preIndex+1] = current;
    }
    return arr;
}

```

####  二分法排序

```javascript

function quickSort(arr){
    if(arr.length<=1){
        return arr;
    }
    var nowNode=arr.splice(Math.floor(arr.length/2),1); //获取数组中间的值
    var leftArr=[];
    var rightArr=[];
    for(var i=0;i<arr.length;i++){
        if(parseInt(arr[i])<=nowNode){
            leftArr.push(arr[i]);
        }else{
            rightArr.push(arr[i]);
        }
    }
    return quickSort(leftArr).concact(nowNode,quickSort(rightArr));
}

```


####  逻辑17

>实现一个函数clone，可以对JavaScript中的5种主要的数据类型（包括Number、String、Object、Array、Boolean）进行值复制

```javascript

Object.prototype.clone=function(){

    var o=this.constructor===Array?[]:{};
    for(var e in this){
        o[e]=typeOf this[e]==="object"?this[e].clone():this[e];
    }
    return o;
}

```