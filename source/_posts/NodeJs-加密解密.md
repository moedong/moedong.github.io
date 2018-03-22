---
title: NodeJs 加密解密
date: 2018-01-17 16:18:53
tags: [node,crypto]
---

> 常见的加密算法基本分为这几类，1 :线性散列算法、2：对称性加密算法、3、非对称性加密算法。
> nodejs是通集成在内核中的crypto模块来完成加密解密。

crypto的基本使用

```javascript
var crypto = require('crypto');
 
//加密
exports.cipher = function(algorithm, key, buf) {
    var encrypted = "";
    var cip = crypto.createCipher(algorithm, key);
    encrypted += cip.update(buf, 'binary', 'hex');
    encrypted += cip.final('hex');
    return encrypted
};
 
//解密
exports.decipher = function(algorithm, key, encrypted) {
    var decrypted = "";
    var decipher = crypto.createDecipher(algorithm, key);
    decrypted += decipher.update(encrypted, 'hex', 'binary');
    decrypted += decipher.final('binary');
    return decrypted
};
```




### 1、对称式加密

就是加密和解密使用同一个密钥，通常称之为“Session Key ”这种加密技术在当今被广泛采用，如美国政府所采用的DES加密标准就是一种典型的“对称式”加密法，它的Session Key长度为56bits。

<!--more-->

栗子：

1、加密模块的引用：

```javascript
var crypto=require('crypto');
var $=require('underscore'); //underscore中的reduce、reduceRight方法，进行加密和解密的算法执行
var DEFAULTS = {
    encoding: {
        input: 'utf8',
        output: 'hex'
    },
    algorithms: ['bf', 'blowfish', 'aes-128-cbc']
};
```

>默认加密算法配置项：
>- 数据输入格式为utf8，输出格式为hex;
>- 算法使用bf,blowfish,aes-128-abc三种加密算法;

2、配置项初始化：

```javascript
function MixCrypto(options) {
	if (typeof options == 'string')
		options = { key: options };
 
	options = $.extend({}, DEFAULTS, options);
	this.key = options.key;
	this.inputEncoding = options.encoding.input;
	this.outputEncoding = options.encoding.output;
	this.algorithms = options.algorithms;
}
```
>加密算法可以进行配置，通过配置option进行不同加密算法及编码的使用。

3、加密方法代码如下：

```javascript
MixCrypto.prototype.encrypt = function (plaintext) {
    return $.reduce(this.algorithms, function (memo, a) {
        var cipher = crypto.createCipher(a, this.key);
        return cipher.update(memo, this.inputEncoding, this.outputEncoding)
            + cipher.final(this.outputEncoding)
    }, plaintext, this);
};
```
>使用crypto进行数据的加密处理。

4、解密方法代码如下：

```javascript
MixCrypto.prototype.decrypt = function (crypted) {
	try {
		return $.reduceRight(this.algorithms, function (memo, a) {
			var decipher = crypto.createDecipher(a, this.key);
			return decipher.update(memo, this.outputEncoding, this.inputEncoding)
			+ decipher.final(this.inputEncoding);
		}, crypted, this);
	} catch (e) {
		return;
	}
};
```
>- 使用crypto进行数据的解密处理。
>- 通过underscore中的reduce、reduceRight方法，进行加密和解密的算法执行



### 2、非对称加密

非对称加密算法需要两个密钥：公开密钥(publickey)和私有密钥(privatekey)
公开密钥与私有密钥是一对，如果用公开密钥对数据进行加密，只有用对应的私有密钥才能解密,因为加密和解密使用的是两个不同的密钥，所以这种算法叫作非对称加密算法

>为私钥创建公钥

```
$ openssl req -key key.pem -new -x509 -out cert.pem
```

```javascript

var crypto = require('crypto');
var fs = require('fs');

var buffer = new Buffer('hello');

//使用公钥对数据进行加密
var secret = crypto.publicEncrypt(fs.readFileSync('cert.pem').toString(), buffer);

//使用私钥对数据进行解密
var result = crypto.privateDecrypt(fs.readFileSync('key.pem').toString(), secret);

console.log(result.toString());

```

[参考文章](http://www.jsdaxue.com/archives/136.html)