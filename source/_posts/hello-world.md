---
title: Hexo(yilia)+Github构建个人博客错误收集
date: 2016-12-18 19:58:41
tags: [hexo,github]
---

### 1. 报错： `ERROR Plugin load failed: hexo-generator-json-content`

按照[这篇教程](http://www.cnblogs.com/liuxianan/p/build-blog-website-by-hexo-github.html) 一步步走，然后执行 hexo s，打开 http://localhost:4000/  可以看到主页了，但是 点击左下角的“所有文章”的时候，发现 文章列表里面出现提示  

> * 缺少模块， 需要在根目录下导入该包hexo-generator-json-content。
> * 配置根目录下的 _config.xml文件，配置代码有提示。

当导入完包后,重新执行 hexo g ，cmd上面报错了 `ERROR Plugin load failed: hexo-generator-json-content`,查找相关的文章资料，是由于node版过低造成的，解决办法是 把node升级到6.0版本及以上。

<!-- more -->

### 2. 报错：`ERROR Deployer not found: github`

解决办法：在根目录下 run  `npm install hexo-deployer-git --save`，将 _config.xml 中的 deploy 的type的值 改为 git 

```
deploy:
  type: git
  repository: git@github.com:xxx/xxx.github.io.git
  branch: master
```

### 3. 如何在多台电脑上更新博客

解决办法：

1、git clone https://github.com/xxx/xxx.github.io 建立本地仓库
2、


### 4. 报错：`Host key verification failed. fatal: Could not read from remote repository`

解决办法:

1、在当前目录下打开 git bash, 然后输入 ：ssh git@github.com ，回复 “yes”
2、界面出现 “Connection to github.com closed” 成功。