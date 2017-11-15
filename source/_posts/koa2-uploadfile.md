---
title: koa2--通过koa2上传图片到php后台
date: 2017-09-18 15:18:59
tags: [node,koa2]
---

> 项目有个这样的需求，浏览器页面需要上传图片到后台服务器，前端的页面是通过koa2框架搭建起来的中间层，一开始是通过浏览器直接上传文件到后台，需要携带接口token值，token值一开始登陆之后就保存在页面的cookie上面，因此也暴露了token值，有安全隐患。再者因为页面要兼容ie7，找了很久的，最后选定了 ajaxfileupload，但是前端页面和后台页面是不同子域名，需要配置跨域，设置采用设置domain，本地调试起来各种坑... 因此需要寻找一种 通过koa2上传文件(调用上传文件接口)到后台服务器的可行办法。

<!-- more -->

###### 前端页面 js

```javascript

//document.domain = "xxx.com";
$.ajaxFileUpload({
    url:'/api/upload', //apiDomain + '/api/v1.user/photoupload'
    secureuri:false, //一般设置为false
    fileElementId: 'fileupload', //文件上传空间的id属性<input type="file" id="file" name="file" />
    dataType: 'json', //返回值类型 一般设置为json
    success: function (res,status)  //服务器成功响应处理函数
    {
         
        if(res.data.code==0){
            $("#fileuploadImg").attr("src", "http://" + res.data.imgurl).css({'display': 'inline-block'});
            $("#fileuploadImg").attr("src2", res.data.savename);
            $("#text1").hide();

            $(".field-companyLicense .tips").removeClass('error').addClass('success');
            page.fort();
        }else{
            alert(res.data.message);
        }
         
    },
    error: function (data,status, e)//服务器响应失败处理函数
    {
        console.log(data, status, e);
    }
});  

```
###### koa2的处理js

```javascript

const path = require('path')
const fs = require('fs')
const Busboy = require('busboy')
const request = require('request');
 
function getSuffix (filename) {
  return filename.split('.').pop()
}
 
// 重命名
function Rename (filename) {
  return Math.random().toString(16).substr(2) + '.' + getSuffix(filename)
}
// 删除文件
function removeTemImage (path) {
  fs.unlink(path, (err) => {
    if (err) {
      throw err
    }
  })
}
 
module.exports.post = async(ctx) => {
 
    let session = ctx.session;
    let apiDomain = ctx.state.apiDomain;
    let jump = ctx.request.query.jump;
 
    let busboy = new Busboy({ headers: ctx.request.headers });
    let req = ctx.req;
 
    let imgPath = null;
    let fileName=null;
    let contentType=null;
 
    // 上传到本地服务器
    function uploadFile (ctx) {
 
        return new Promise((resolve, reject) => {
 
            // 监听文件解析事件
            busboy.on('file', function(fieldname, file, filename, encoding, mimetype) {
 
                fileName = Rename(filename);
 
                imgPath = './uploadImages/' + fileName;
 
                contentType=mimetype;
 
                // 文件保存到制定路径
                file.pipe(fs.createWriteStream(imgPath));
 
                // 解析文件结束
                file.on('end', function() {
 
 
                })
 
            });
 
            // 监听请求中的字段
            //busboy.on('field', function(fieldname, val, fieldnameTruncated, valTruncated) {
                //console.log('fieldname:' + fieldname + ',value:' + inspect(val))
                //token = ctx.session.user.token;
            //})
 
            // 监听结束事件
            busboy.on('finish', function(val) {
 
                resolve({
                    imgPath:imgPath,
                    options:{
                        filenafilename: fileName,
                        contentType: contentType,
                    }
                });
 
            })
 
            req.pipe(busboy)
 
        })
    }
 
    // 上传到后台
    function upToPhp (result) {
 
        return new Promise(function(resolve, reject) {
 
            var formData = {
                token: ctx.session.user.token,
                image:{
                    value: fs.createReadStream(result.imgPath),
                    options:result.options,
                }
            };
 
            request.post({ url: apiDomain + '/api/v1.user/photoupload', formData: formData }, function optionalCallback(err, httpResponse, body) {
 
                if (err) {
                    return console.error('Upload failed:', err);
                }
                console.log('Upload successful! ', body);
 
                //删除临时文件
                fs.unlink(imgPath,(err) => {
                    if (err) {
                        throw err
                    }
                });
 
                resolve();
 
            });
 
        })
 
 
    }
 
 
    // 获取上存图片
    const result = await uploadFile(ctx);
 
    // 发送到后台
    const result2 = await upToPhp(result);
 
 
    ctx.body = {
        data: {
            code: result2.data.code,
            message: result2.data.message,
        }
    };
 
}

```

页面已经可以成功上传图片到后台了，但是发现了一个问题，就是如果上传的图片是png24格式的时候，upToPhp这个方法没有执行成功，一直提示 result.imgPath 路径不存在，具体也不知道是什么原因，png8和jpg格式的图片可以正常上传。