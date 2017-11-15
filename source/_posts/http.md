---
title: http
date: 2017-03-02 09:24:40
tags: [js,http]
---

#### 一、HTTP method

* GET 是最常用的方法，用于请求服务器发送某个资源
* HEAD和GET类似，但服务器在响应中只返回首部，不返回实体的主体部分
* POST 主要是用来向服务器提交表单数据
* PUT  让服务器用请求的主体部分来创建一个由所请求的URL命名的新文档，如果那个URL已经存在的话，就用这个主体代替他。（服务器创建文档）
* TRACE  用于验证请求是否如愿穿过了请求/响应链。目的服务器端发起一个环回请求诊断，最后一站的服务器会弹回一个TRACE响应并在响应体中携带它收到的原始请求报文。
* OPTIONS 请求服务器告知其支持的各种功能。查询服务器支持哪些方法。
* DELETE  请求服务器删除请求URL指定的资源。

<!-- more -->

#### 二、HTTP状态码及其含义

  1XX：信息状态码
    > * 100 Continue：客户端应当继续发送请求。这个临时相应是用来通知客户端它的部分请求已经被服务器接收，且仍未被拒绝。客户端应当继续发送请求的剩余部分，或者如果请求已经完成，忽略这个响应。服务器必须在请求万仇向客户端发送一个最终响应
    > * 101 Switching Protocols：服务器已经理解力客户端的请求，并将通过Upgrade消息头通知客户端采用不同的协议来完成这个请求。在发送完这个响应最后的空行后，服务器将会切换到Upgrade消息头中定义的那些协议。
  2XX：成功状态码
    * 200 OK：请求成功，请求所希望的响应头或数据体将随此响应返回
    * 201 Created：
    * 202 Accepted：
    * 203 Non-Authoritative Information：
    * 204 No Content：
    * 205 Reset Content：
    * 206 Partial Content：
  3XX：重定向
    * 300 Multiple Choices：
    * 301 Moved Permanently：
    * 302 Found：
    * 303 See Other：
    * 304 Not Modified：
    * 305 Use Proxy：
    * 306 （unused）：
    * 307 Temporary Redirect：
  4XX：客户端错误
    * 400 Bad Request:
    * 401 Unauthorized:
    * 402 Payment Required:
    * 403 Forbidden:
    * 404 Not Found:
    * 405 Method Not Allowed:
    * 406 Not Acceptable:
    * 407 Proxy Authentication Required:
    * 408 Request Timeout:
    * 409 Conflict:
    * 410 Gone:
    * 411 Length Required:
    * 412 Precondition Failed:
    * 413 Request Entity Too Large:
    * 414 Request-URI Too Long:
    * 415 Unsupported Media Type:
    * 416 Requested Range Not Satisfiable:
    * 417 Expectation Failed:
  5XX: 服务器错误
    * 500 Internal Server Error:
    * 501 Not Implemented:
    * 502 Bad Gateway:
    * 503 Service Unavailable:
    * 504 Gateway Timeout:
    * 505 HTTP Version Not Supported:


#### 三、HTTP request报文结构

>1.首行是Request-Line包括：请求方法，请求URI，协议版本，CRLF
>2.首行之后是若干行请求头，包括general-header，request-header或者entity-header，每个一行以CRLF结束
>3.请求头和消息实体之间有一个CRLF分隔
>4.根据实际请求需要可能包含一个消息实体 一个请求报文例子如下：

```http

GET /Protocols/rfc2616/rfc2616-sec5.html HTTP/1.1
Host: www.w3.org
Connection: keep-alive
Cache-Control: max-age=0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8
User-Agent: Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/35.0.1916.153 Safari/537.36
Referer: https://www.google.com.hk/
Accept-Encoding: gzip,deflate,sdch
Accept-Language: zh-CN,zh;q=0.8,en;q=0.6
Cookie: authorstyle=yes
If-None-Match: "2cc8-3e3073913b100"
If-Modified-Since: Wed, 01 Sep 2004 13:24:52 GMT

name=qiu&age=25

```

#### 四、HTTP response报文结构

>1.首行是状态行包括：HTTP版本，状态码，状态描述，后面跟一个CRLF
>2.首行之后是若干行响应头，包括：通用头部，响应头部，实体头部
>3.响应头部和响应实体之间用一个CRLF空行分隔
>最后是一个可能的消息实体 响应报文例子如下：

```http

HTTP/1.1 200 OK
Date: Tue, 08 Jul 2014 05:28:43 GMT
Server: Apache/2
Last-Modified: Wed, 01 Sep 2004 13:24:52 GMT
ETag: "40d7-3e3073913b100"
Accept-Ranges: bytes
Content-Length: 16599
Cache-Control: max-age=21600
Expires: Tue, 08 Jul 2014 11:28:43 GMT
P3P: policyref="http://www.w3.org/2001/05/P3P/p3p.xml"
Content-Type: text/html; charset=iso-8859-1

{"name": "qiu", "age": 25}

```

####  五、从浏览器地址栏输入url到显示页面的步骤

1.在浏览器地址栏输入URL

2.浏览器查看缓存，如果请求资源在缓存中并且新鲜，跳转到转码步骤
&emsp;&emsp;i.如果资源未缓存，发起新请求
&emsp;&emsp;ii.如果已缓存，检验是否足够新鲜，足够新鲜直接提供给客户端，否则与服务器进行验证。
&emsp;&emsp;iii.检验新鲜通常有两个HTTP头进行控制Expires和Cache-Control：
&emsp;&emsp;&emsp;&emsp;HTTP1.0提供Expires，值为一个绝对时间表示缓存新鲜日期
&emsp;&emsp;&emsp;&emsp;HTTP1.1增加了Cache-Control: max-age=,值为以秒为单位的最大新鲜时间

3.浏览器解析URL获取协议，主机，端口，path

4.浏览器组装一个HTTP（GET）请求报文

5.浏览器获取主机ip地址，过程如下：
&emsp;&emsp;i.浏览器缓存
&emsp;&emsp;ii.本机缓存
&emsp;&emsp;iii.hosts文件
&emsp;&emsp;iv.路由器缓存
&emsp;&emsp;v.ISP DNS缓存
&emsp;&emsp;vi.DNS递归查询（可能存在负载均衡导致每次IP不一样）

6.打开一个socket与目标IP地址，端口建立TCP链接，三次握手如下：

&emsp;&emsp;i.客户端发送一个TCP的SYN=1，Seq=X的包到服务器端口
&emsp;&emsp;ii.服务器发回SYN=1， ACK=X+1， Seq=Y的响应包
&emsp;&emsp;iii.客户端发送ACK=Y+1， Seq=Z

7.TCP链接建立后发送HTTP请求

8.服务器接受请求并解析，将请求转发到服务程序，如虚拟主机使用HTTP Host头部判断请求的服务程序

9.服务器检查HTTP请求头是否包含缓存验证信息如果验证缓存新鲜，返回304等对应状态码

10.处理程序读取完整请求并准备HTTP响应，可能需要查询数据库等操作

11.服务器将响应报文通过TCP连接发送回浏览器

12.浏览器接收HTTP响应，然后根据情况选择关闭TCP连接或者保留重用，关闭TCP连接的四次握手如下：
&emsp;&emsp;i.主动方发送Fin=1， Ack=Z， Seq= X报文
&emsp;&emsp;ii.被动方发送ACK=X+1， Seq=Z报文
&emsp;&emsp;iii.被动方发送Fin=1， ACK=X， Seq=Y报文
&emsp;&emsp;iv.主动方发送ACK=Y， Seq=X报文

13.浏览器检查响应状态吗：是否为1XX，3XX， 4XX， 5XX，这些情况处理与2XX不同

14.如果资源可缓存，进行缓存

15.对响应进行解码（例如gzip压缩）

16.根据资源类型决定如何处理（假设资源为HTML文档）

17.解析HTML文档，构件DOM树，下载资源，构造CSSOM树，执行js脚本，这些操作没有严格的先后顺序，以下分别解释

18.构建DOM树：
&emsp;&emsp;i.Tokenizing：根据HTML规范将字符流解析为标记
&emsp;&emsp;ii.Lexing：词法分析将标记转换为对象并定义属性和规则
&emsp;&emsp;iii.DOM construction：根据HTML标记关系将对象组成DOM树

19.解析过程中遇到图片、样式表、js文件，启动下载

20.构建CSSOM树：
&emsp;&emsp;i.Tokenizing：字符流转换为标记流
&emsp;&emsp;ii.Node：根据标记创建节点
&emsp;&emsp;iii.CSSOM：节点创建CSSOM树

21.根据DOM树和CSSOM树构建渲染树:

&emsp;&emsp;i.从DOM树的根节点遍历所有可见节点，不可见节点包括：1）script,meta这样本身不可见的标签。2)被css隐藏的节点，如display: none
&emsp;&emsp;ii.对每一个可见节点，找到恰当的CSSOM规则并应用
&emsp;&emsp;iii.发布可视节点的内容和计算样式

22.js解析如下：

&emsp;&emsp;i.浏览器创建Document对象并解析HTML，将解析到的元素和文本节点添加到文档中，此时document.readystate为loading

&emsp;&emsp;ii.HTML解析器遇到`没有` `async`和`defer`的script时，将他们添加到文档中，然后执行行内或外部脚本。`这些脚本会同步执行，并且在脚本下载和执行时解析器会暂停`。`这样就可以用document.write()
把文本插入到输入流中`。同步脚本经常简单定义函数和注册事件处理程序，他们可以遍历和操作script和他们之前的文档内容

&emsp;&emsp;iii.当解析器遇到设置了`async`属性的`script`时，开始下载脚本并继续解析文档。`脚本会在它下载完成后尽快执行，但是解析器不会停下来等它下载`。`异步脚本禁止使用document.write()`，它们可以访问自己script和之前的文档元素

&emsp;&emsp;iv.当文档完成解析，document.readState变成interactive

&emsp;&emsp;vi.浏览器在Document对象上触发DOMContentLoaded事件

&emsp;&emsp;vii.此时文档完全解析完成，浏览器可能还在等待如图片等内容加载，等这些内容完成载入并且所有异步脚本完成载入和执行，document.readState变为complete,window触发load事件

23.显示页面（HTML解析过程中会逐步显示页面）

            





#### 六、如何使用缓存


##### 1、降低浏览器向服务器发出请求的次数

如果页面已缓存，检验是否足够新鲜，足够新鲜直接提供给客户端，否则与服务器进行验证。

足够新鲜的Headers General：
>Status Code:200 OK (from memory cache)

```http

Request URL:http://www1.pconline.com.cn/images/blank.gif
Request Method:GET
Status Code:200 OK (from memory cache)  // Status:200,Size:from memory cache,Time:0
Remote Address:61.145.113.41:80
Referrer Policy:no-referrer-when-downgrade

```
---

检验新鲜通常有两个HTTP头进行控制`Expires`和`Cache-Control`：

HTTP1.0提供Expires，值为一个绝对时间表示缓存新鲜日期、(`缓存的载止时间`，允许客户端在这个时间之前不去检查)
HTTP1.1增加了Cache-Control: max-age=,值为以`秒`为单位的`最大新鲜时间`（资源在`本地缓存多少秒`）


> 如果max-age和Expires同时存在，则被Cache-Control的max-age覆盖。




##### 2、向服务器进行验证请求

###### 2.1、Last-Modified
在浏览器第一次请求某一个URL时，服务器端的返回状态会是200，内容是你请求的资源，同时有一个Last-Modified的属性标记(HttpReponse Header)此文件在服务期端最后被修改的时间。

Response Header格式如下：

Last-Modified:Tue, 24 Feb 2009 08:01:04 GMT

客户端第二次请求此URL时，根据HTTP协议的规定，浏览器会向服务器传送If-Modified-Since报头(HttpRequest Header)，询问该时间之后文件是否有被修改过。

Request Header格式如下：

If-Modified-Since:Tue, 24 Feb 2009 08:01:04 GMT

如果服务器端的资源没有变化，则自动返回HTTP304（NotChanged.）状态码，内容为空，这样就节省了传输数据量。当服务器端代码发生改变或者重启服务器时，则重新发出资源，返回和第一次请求时类似。从而保证不向客户端重复发出资源，也保证当服务器有变化时，客户端能够得到最新的资源。


###### 2.2、ETag

HTTP协议规格说明定义ETag为“被请求变量的实体标记”（参见14.19）。简单点即服务器响应时给请求URL标记，并在HTTP响应头中将其传送到客户端。

服务器端 response 返回的格式：

Etag:“5d8c72a5edda8d6a:3239″

浏览器的 request 格式是这样的：

If-None-Match:“5d8c72a5edda8d6a:3239″


如果ETag没改变，则返回状态304。

即:在客户端发出请求后，HttpReponse Header中包含Etag:“5d8c72a5edda8d6a:3239″标识，等于告诉Client端，你拿到的这个的资源有表示ID：5d8c72a5edda8d6a:3239。

当下次需要发Request索要同一个URI的时候，浏览器同时发出一个If-None-Match报头(Http RequestHeader)此时包头中信息包含上次访问得到的Etag:“5d8c72a5edda8d6a:3239″标识。
If-None-Match:“5d8c72a5edda8d6a:3239“

服务器首先产生ETag，服务器可在稍后使用它来判断页面是否已经被修改。
本质上，客户端通过将该记号传回服务器要求服务器验证其（客户端）缓存。
304是HTTP状态码，服务器用来标识这个文件没修改，不返回内容，浏览器在接收到个状态码后，会使用浏览器已缓存的文件。

###### 2.3、Last-Modified 和 Etag

RFC 规定，如果 ETag 和 Last-Modified 都有，则必须一次性都发给服务器，没有优先级。
如果服务器输出了 ETag，没有必要再输出 Last-Modified。

    




####  七、创建ajax过程

(1)创建XMLHttpRequest对象,也就是创建一个异步调用对象.

(2)创建一个新的HTTP请求,并指定该HTTP请求的方法、URL及验证信息.

(3)设置响应HTTP请求状态变化的函数.

(4)发送HTTP请求.

(5)获取异步调用返回的数据.

(6)使用JavaScript和DOM实现局部刷新.

```javascript

var xmlHttp = new XMLHttpRequest();

xmlHttp.open('GET','demo.php','true');
//第三个参数设置请求是否为异步模式。如果是TRUE，JavaScript函数将继续执行，而不等待服务器响应。当状态改变时会调用onreadystatechange属性指定的回调函数

xmlHttp.send()

xmlHttp.onreadystatechange = function(){

    if(xmlHttp.readyState === 4 & xmlHttp.status === 200){

    }

}

```


####  八、JS脚本动态加载

```javascript

function GetHttpRequest(){ 

    if ( window.XMLHttpRequest ) // Gecko 

        return new XMLHttpRequest() ; 

    else if ( window.ActiveXObject ) // IE 

        return new ActiveXObject("MsXml2.XmlHttp") ; 
} 


function AjaxPage(sId,url,type){ 

    var oXmlHttp = GetHttpRequest() ; 

    oXmlHttp.OnReadyStateChange = function() { 

        if ( oXmlHttp.readyState == 4 ) { 

            if ( oXmlHttp.status == 200 || oXmlHttp.status == 304 ) { 

                console.log(2222);

                type==="js" && IncludeJS( sId, url, oXmlHttp.responseText );

            }else{ 

                alert( 'XML request error: ' + oXmlHttp.statusText + ' (' + oXmlHttp.status + ')' ) ; 
            }
        } 
    } 

    oXmlHttp.open('GET', url, true); 

    oXmlHttp.send(null); 

} 

function IncludeJS(sId, fileUrl, source) { 

    if ( ( source != null ) && ( !document.getElementById( sId ) ) ){ 

        var oHead = document.getElementsByTagName('HEAD').item(0); 

        var oScript = document.createElement( "script" ); 

        oScript.language = "javascript"; 

        oScript.type = "text/javascript"; 

        oScript.id = sId; 

        oScript.defer = true; 

        oScript.text = source; 

        oHead.appendChild( oScript ); 
    } 

}

AjaxPage( "scrA", "b.js" ,"js");

```