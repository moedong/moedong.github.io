---
title: pm2管理koa2
date: 2017-12-20 16:39:48
tags: [pm2,node,koa2]
---

##### 1、部署

package.json 

```json
"scripts": {
    "release": "gulp release",
    "reduce": "gulp reduce",
    "dev": "gulp dev",
    "test": "pm2 start ./config/ecosystem.config.js --env test",
    "server": "pm2 start ./config/ecosystem.config.js --env production"
  }
```
<!-- more -->

ecosystem.config.js
```javascript

module.exports = {
    apps: [{
        name: "yjl",
        script: "./app.js",
        watch: true,
        env: {
            NODE_ENV: "development",
            PORT: 443,
            API_URL:"https://backend.xxx",
            SOURCE_PATH:"./src"
        },
        env_test: {
            NODE_ENV: "test",
            PORT: 443,
            API_URL:"https://backend.xxx",
            SOURCE_PATH:"./dist"
        },
        env_production: {
            NODE_ENV: "production",
            PORT: 4000,
            API_URL:"http://backend.xxx",
            SOURCE_PATH:"./dist"
        },
        autorestart: true,
        log_date_format: "YYYY-MM-DD HH:mm Z"
    }]
}

```

app.js

```javascript


const port = process.env.PORT || 443;
const env = process.env.NODE_ENV || 'development';
const src = process.env.SOURCE_PATH || './src';
const apiDomain = process.env.API_URL || 'https://backend.xxx';

const app = new Koa();

// 配置session中间件
app.use(session({
    key: "SESSIONID", //default "koa:sess"
    maxAge: 2 * 60 * 60 * 1000 //设置session超时时间,2小时
}))

//设置全局变量
app.use(async(ctx, next) => {
    ctx.state.session = ctx.session;
    ctx.state.apiDomain = apiDomain;
    await next();
})

```

##### 2、使用pm2

1、保存脚本

```
pm2 save
```
2、创建开机启动脚本

```
pm2 startup
```

3、设置开机自动启动(centos 7+)
```
systemctl enable pm2-root.service
```

4、重启koa2
```
pm2 restart yjl
```
5、重新搭建启动koa2
```
pm2 delete yjl
npm run server
```
6、查看错误日志
```
pm2 show yjl  #查看log的输出目录

cd /root/.pm2/logs/ && cat yjl-error-0.log

```