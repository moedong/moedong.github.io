---
title: 关于H5平时工作中的一些知识点总结
date: 2017-01-02 00:27:25
tags: [html5,css3]
description: H5项目常见问题汇总及解决方案
---

### 1、flex 弹性布局

>flex-direction
>flex-wrap
>flex-flow
>justify-content (水平)
>align-items (竖直 单轴)
>align-content (竖直 多轴)

<!-- more -->

###### 1.1、flex-direction
>row（默认值）：主轴为水平方向，起点在左端。
>row-reverse：主轴为水平方向，起点在右端。
>column：主轴为垂直方向，起点在上沿。
>column-reverse：主轴为垂直方向，起点在下沿。

![flex-direction](http://www.ruanyifeng.com/blogimg/asset/2015/bg2015071005.png)


###### 1.2、flex-wrap

> nowrap（默认）：不换行。
![nowrap](http://www.ruanyifeng.com/blogimg/asset/2015/bg2015071007.png)



> wrap：换行，第一行在上方。
![wrap](http://www.ruanyifeng.com/blogimg/asset/2015/bg2015071008.jpg)



> wrap-reverse：换行，第一行在下方。
![wrap-reverse](http://www.ruanyifeng.com/blogimg/asset/2015/bg2015071009.jpg)


###### 1.3、flex-flow

> flex-flow属性是flex-direction属性和flex-wrap属性的简写形式，默认值为row nowrap。

```css
.box {
  flex-flow: <flex-direction> || <flex-wrap>;
}
```

###### 1.4、justify-content
>flex-start（默认值）：左对齐
>flex-end：右对齐
>center： 居中
>space-between：两端对齐，项目之间的间隔都相等。
>space-around：每个项目两侧的间隔相等。所以，项目之间的间隔比项目与边框的间隔大一倍。

![justify-content](http://www.ruanyifeng.com/blogimg/asset/2015/bg2015071010.png)

###### 1.5、align-items
>flex-start：交叉轴的起点对齐。
>flex-end：交叉轴的终点对齐。
>center：交叉轴的中点对齐。
>baseline: 项目的第一行文字的基线对齐。
>stretch（默认值）：如果项目未设置高度或设为auto，将占满整个容器的高度。

![align-content](http://www.ruanyifeng.com/blogimg/asset/2015/bg2015071011.png)

###### 1.6、align-content

>flex-start：与交叉轴的起点对齐。
>flex-end：与交叉轴的终点对齐。
>center：与交叉轴的中点对齐。
>space-between：与交叉轴两端对齐，轴线之间的间隔平均分布。
>space-around：每根轴线两侧的间隔都相等。所以，轴线之间的间隔比轴线与边框的间隔大一倍。
>stretch（默认值）：轴线占满整个交叉轴。

![align-content](http://www.ruanyifeng.com/blogimg/asset/2015/bg2015071012.png)

### 2、文本垂直居中

```html
<div class="box box1"><span>我是垂直居中元素</span><i></i></div>

```

```css

/* 方法1：dispaly:table-cell */

.box1{ text-align:center; display:table-cell; vertical-align:middle; }

/* 方法2：display:flex */

.box2{ display:flex; justify-content:center; align-items:center; text-align:center; }

/* 方法3：translate(-50%,-50%) */

.box3{ position:relative;}
.box3 span{ position:absolute; left:50%; top:50%; -webkit-transform:translate(-50%,-50%); width:100%; text-align:center; }

/* 方法4：兼容 ie ，添加一个 空标签 i */

.box1{height:300px;width: 400px;text-align: center;}

.box1 span{vertical-align: middle; display: inline-block; *display: inline; *zoom: 1;}

.box1 i{ width: 0; height: 100%; vertical-align: middle;display:inline-block;}

```

### 3、伪元素实现换行，替代换行标签

> * 《CSS SECRET》 中对 br 标签的描述是，这种方法不仅在可维护性方面是一种糟糕的实践，而且污染了结构层的代码，运用 after 伪元素，可以有一种非常优雅的解决方案。
> * 通过给元素的 after 伪元素添加 content 为 “\A” 的值。这里 \A 表示的是什么呢？有一个 Unicode 字符是专门代表换行符的：0x000A 。 在 CSS 中，这个字符可以写作 “\000A”， 或简化为 “\A”。这里我们用它来作为 ::after 伪元素的内容。也就是在元素末尾添加了一个换行符的意思。而 white-space: pre; 的作用是保留元素后面的空白符和换行符，结合两者，就可以轻松实现在行内级元素末尾实现换行。

```css

inline-element ::after{
       content:"\A";
       white-space: pre;
}

```
### 4、will-change提高页面滚动、动画等渲染性能

```css

/* 关键字值 */
will-change: auto;
will-change: scroll-position;
will-change: contents;
will-change: transform;        /* <custom-ident>示例 */
will-change: opacity;          /* <custom-ident>示例 */
will-change: left, top;        /* 两个<animateable-feature>示例 */

```
will-change的使用也要谨慎，遵循最小化影响原则，不要这样直接写在默认状态中：

```css

.will-change {
  will-change: transform;
  transition: transform 0.3s;
}
.will-change:hover {
  transform: scale(1.5);
}

```

可以让父元素hover的时候，声明will-change，这样，移出的时候就会自动remove，触发的范围基本上是有效元素范围。

```css

.will-change-parent:hover .will-change {
  will-change: transform;
}
.will-change {
  transition: transform 0.3s;
}
.will-change:hover {
  transform: scale(1.5);
}

```

####  5、pc和wap通用的性能优化

> * 利用缓存
```
缓存Ajax

使用CDN (CDN的基本原理是广泛采用各种缓存服务器，将这些缓存服务器分布到用户访问相对集中的地区或网络中，在用户访问网站时，利用全局负载技术将用户的访问指向距离最近的工作正常的缓存服务器上，由缓存服务器直接响应用户请求)

服务端配置Etags (实体标签，HTTP协议的一部分，用一个特殊的字符串来标识某个资源的“版本”，客户端（浏览器）来请求的时候，可以比较，如果ETag一致，则表示该资源并没有修改过，客户端（浏览器）可以使用自己缓存的版本)

减少DNS查找
```
> * 减少HTTP请求（雪碧图，文件合并等),初始首屏之外的图片资源按需加载。
> * 代码层面的优化：少用全局变量、减少DOM操作次数、缓存DOM节点查找的结果、避免图片和iFrame等的空Src(空Src会重新加载当前页面，影响速度和效率)、避免使用CSS Expression



####  6、移动页面性能优化

![1](http://ossweb-img.qq.com/upload/webplat/info/tgideas/20141205/1417746123387641.jpg)

加载中的优化：

> * 预加载: 显式预加载（loading提示） 、隐性加载（slide滚动图的后面图片的加载）

> * 按需加载: 首屏加载  、 响应式加载

> * 压缩图片

> * 尽量避免重定向
![2](http://ossweb-img.qq.com/upload/webplat/info/tgideas/20141205/1417746126325962.jpg)

> * 使用其他方式加载图片 ：css3绘制图片 、使用iconfont代替图片。


脚本的执行的优化：


> * 尽量避免DataURI: 生成的代码文件相对图片文件体积没有减少反而增大，而且浏览器在对这种base64解码过程中需要消耗内存和cpu，这个在移动端坏处特别明显。

> * 点击事件优化:适当使用touchstart，touchend，touch等事件代替延迟比较大的click事件。


渲染阶段的优化：


> * 动画优化: 尽量使用css3动画(不占js主线程，可开启硬件加速，浏览器可以对动画做优化，不支持中间状态的监听)、适当使用canvas动画(可规避渲染树的计算，渲染更快，开发成本高)、合理使用RAF--requestAnimationFrame(能解决脚本问题引起的丢帧，卡顿问题，支持中间状态监听)

> * 高频事件优化: 类似touchmove，scroll这类的事件可导致多次渲染,增加响应变化的时间间隔，减少重绘次数。


合成/绘制优化：


> * GPU加速，触发GPU加速的方式：CSS3 transitions、 CSS3 3D transforms(transform: translateZ(0) )、will-change

总结
![3](http://ossweb-img.qq.com/upload/webplat/info/tgideas/20141205/1417746129250017.jpg)