---
title: '使用gitbook编写api文档'
date: 2018-02-26 16:57:54
tags: [gitbook,pdf]
---

### 1、安装gitbook

使用npm来安装，在命令行中输入：

```
npm install gitbook-cli -g

```

安装完成之后，你可以使用下面的命令来检验是否安装成功

```
gitbook -V
```

### 2、创建gitbook目录结构

**README.md** 这个文件相对于是一本Gitbook的简介，比如我们这本书的README.md :

```
# Gitbook 使用入门
> GitBook 是一个基于 Node.js 的命令行工具，可使用 Github/Git 和 Markdown 来制作精美的电子书。
本书将简单介绍如何安装、编写、生成、发布一本在线图书。
```
<!-- more -->

**SUMMARY.md** 这个文件相对于是一本书的目录结构。比如我们这本书的SUMMARY.md :

```
> # Summary

* [Introduction](README.md)
* [基本安装](howtouse/README.md)
   * [Node.js安装](howtouse/nodejsinstall.md)
   * [Gitbook安装](howtouse/gitbookinstall.md)
* [目录结构](book/README.md)
   * [README.md 与 SUMMARY编写](book/file.md)
   * [目录初始化](book/prjinit.md)
* [输出](output/README.md)
   * [输出为静态网站](output/outfile.md)
   * [输出PDF](output/pdfandebook.md)
* [发布](publish/README.md)
   * [发布到Github Pages](publish/gitpages.md)
* [结束](end/README.md)

```

#### 2.1 使用Gitbook的命令行工具将这个目目录结构生成相应地目录及文件

```
$ gitbook init

```

> 如果一开始时，您的目录下未添加 **README.md** 和 **SUMMARY.md** 这两个文件时，命令会自动生成这两个文件，然后在编辑这两个文件即可

### 3、输出静态网站和PDF

#### 3.1 本地预览

当你编辑好gitbook文档之后，你可以使用gitbook的命令来进行本地预览。

```
gitbook serve 

```
gitbook会启动一个4000端口用于预览。



#### 3.2  生成静态网页

```
gitbook build
```

生成的静态页面会在更目录的 **_book**目录下

#### 3.3 输出pdf

gitbook编写完成后，为了方便打开查看，我们将其转化成pdf, 需要先安装gitbook pdf:

```
npm install gitbook-pdf -g

```

然后，用下面的命令就可以生成PDF文件了。
```
gitbook pdf {book_name}
```
如果，你已经在编写的gitbook当前目录，也可以使用相对路径。
```
gitbook pdf .
```
正常情况的话，你就会发现，你的目录中多了一个名为book.pdf的文件。

但是在执行  gitbook pdf . 的时候，报错了：

```
EbookError: Error during ebook generation: 'ebook-convert' .....

```
> 网上查了一下，大概是说 'ebook-convert' 应该只是nodejs适配包，并不包含真正的执行程序,需要添加到环境变量中
> 安装calibre  http://www.calibre-ebook.com/download_windows
> 安装成功后，打开calibre文件位置 可以看到 `ebook-convert.exe` 和 `ebook-device.exe`，点击执行就可以了


### 4、参考文章

[Gitbook的安装教程1](https://tonydeng.github.io/gitbook-zh/gitbook-howtouse/book/file.html)
[Gitbook的安装教程2](http://yangjh.oschina.io/gitbook/faq/Number.html)
[Mac环境安装Gitbook，并导出PDF教程](https://www.jianshu.com/p/4824d216ad10)
[http://ju.outofmemory.cn/entry/212632](http://ju.outofmemory.cn/entry/212632)