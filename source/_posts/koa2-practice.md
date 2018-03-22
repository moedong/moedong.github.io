---
title: koa2-生成环境部署注意事项
date: 2017-12-13 14:39:23
tags: [node,koa2,http]
---

>koa2应用部署到生产环境,需要做一些安全配置。

###### 1、安全性相关的HTTP头

以下是一些安全性相关的HTTP头，你的站点应该设置它们：

> * Strict-Transport-Security：强制使用安全连接（SSL/TLS之上的HTTPS）来连接到服务器。
> * X-Frame-Options：提供对于“点击劫持”的保护。
> * X-XSS-Protection：开启大多现代浏览器内建的对于跨站脚本攻击（XSS）的过滤功能。
> * X-Content-Type-Options： 防止浏览器使用MIME-sniffing来确定响应的类型，转而使用明确的content-type来确定。
> * Content-Security-Policy：防止受到跨站脚本攻击以及其他跨站注入攻击。

<!-- more -->

在Node.js中，这些都可以通过使用Helmet模块轻松设置完毕：

````javascript
var koa = require('koa');  
var helmet = require('koa-helmet');

var app = express();

app.use(helmet());  

````

###### 2、设置 cookie 安全性选项

设置以下 cookie 选项来增强安全性：

> * secure - 确保浏览器只通过 HTTPS 发送 cookie。
> * httpOnly - 确保 cookie 只通过 HTTP(S)（而不是客户机 JavaScript）发送，这有助于防御跨站点脚本编制攻击。
> * domain - 表示 cookie 的域；用于和请求 URL 的服务器的域进行比较。如果匹配，那么接下来检查路径属性。
> * path - 表示 cookie 的路径；用于和请求路径进行比较。如果路径和域都匹配，那么在请求中发送 cookie。
> * expires - 用于为持久性 cookie 设置到期日期。

###### 3、命令注入

攻击者使用命令注入来在远程web服务器中运行系统命令。通过命令注入，攻击者甚至可以取得系统的密码。

实践中，如果你有一个URL：

`https://example.com/downloads?file=user1.txt`

它可以变成：

`https://example.com/downloads?file=%3Bcat%20/etc/passwd `

在这个例子中，%3B会变成一个分号。所以将会运行多条系统命令。

为了预防这类攻击，请确保总是检查过滤了用户的输入内容。

我们也可以以Node.js的角度来说：
```
child_process.exec('ls', function (err, data) {  
    console.log(data);
});
```
在child_process.exec的底层，它调用了/bin/sh，所以它是一个bash解释器，而不仅仅是只能执行用户程序。

当用户的输入是一个反引号或$()时，将它们传入这个方法就很危险了。

可以通过使用[child_process.execFile](http://javascript.ruanyifeng.com/nodejs/child-process.html)来解决上面这个问题。

###### 4、跨站请求伪造（CSRF）

跨站请求伪造（CSRF）是一种迫使用户在他们已登录的web应用中，执行一个并非他们原意的操作的攻击手段。这种攻击常常用于那些会改变用户的状态的请求，通常它们并不窃取数据，因为攻击者并不能看到响应的内容。

在Node.js中，你可以使用csrf模块来缓和这种攻击。它同样是非常底层的，你可能更喜欢使用如csurf这样的[koa-csrf](https://www.npmjs.com/package/koa-csrf)中间件。

在路由层，可以会有如下代码：

```javascript

import Koa from 'koa';
import bodyParser from 'koa-bodyparser';
import session from 'koa-generic-session';
import convert from 'koa-convert';
 
const app = new Koa();
 
// set the session keys 
app.keys = [ 'a', 'b' ];
 
// add session support 
app.use(convert(session()));
 
// add body parsing 
app.use(bodyParser());
 
// add the CSRF middleware 
app.use(new CSRF({
  invalidSessionSecretMessage: 'Invalid session secret',
  invalidSessionSecretStatusCode: 403,
  invalidTokenMessage: 'Invalid CSRF token',
  invalidTokenStatusCode: 403,
  excludedMethods: [ 'GET', 'HEAD', 'OPTIONS' ],
  disableQuery: false
}));
 
// your middleware here (e.g. parse a form submit) 
app.use((ctx, next) => {
 
  if (![ 'GET', 'POST' ].includes(ctx.method))
    return next();
 
  if (ctx.method === 'GET') {
    ctx.body = ctx.csrf;
    return;
  }
 
  ctx.body = 'OK';
 
});
 
app.listen();

```

在展示层，EJS Template：

```html
<form action="/register" method="POST">
  <input type="hidden" name="_csrf" value="<%= csrf %>" />
  <input type="email" name="email" placeholder="Email" />
  <input type="password" name="password" placeholder="Password" />
  <button type="submit">Register</button>
</form>

```


###### 5、正则表达式

这类攻击主要是由于一些正则表达式，在极端情况下，会变得性能及其糟糕。这些正则被称为恶魔正则（Evil Regexes）：

对于重复文本进行分组
在重复的分组内又有重复内容

([a-zA-Z]+)*， (a+)+ 或 (a|a?)+在如aaaaaaaaaaaaaaaaaaaaaaaa! 这样的输入面前，都是脆弱的。这会引起大量的计算。更多详情可以参考ReDos。

可以使用Node.js工具 [safe-regex](https://www.npmjs.com/package/safe-regex) 这检测你的正则：

```
$ node safe.js '(beep|boop)*'
true  
$ node safe.js '(a+){10}'
false  
```
###### 6、确保依赖项的安全

在管理应用程序的依赖项方面，npm 功能非常强大，而且使用方便。但是，您使用的软件包可能包含严重的安全漏洞，也可能会对应用程序产生影响。应用程序的安全取决于依赖项中“最弱”的一环。

可以使用以下的任一或全部两种工具，帮助确保所使用的第三方软件包的安全性：nsp 和 requireSafe。这两种工具的功能大体相同。

nsp 是一种命令行工具，用于检查 Node 安全项目漏洞数据库，确定应用程序是否使用具有已知漏洞的软件包。可通过以下命令安装该工具：

```
$ npm i nsp -g
```

使用以下命令将 npm-shrinkwrap.json 文件提交至 nodesecurity.io 以进行验证：

```
$ nsp audit-shrinkwrap
```

使用以下命令将 package.json 文件提交至 nodesecurity.io 以进行验证：

```
$ nsp audit-package
```

以下示例说明如何使用 requireSafe 来审计 Node 模块：

```
$ npm install -g requiresafe
$ cd your-app
$ requiresafe check
```