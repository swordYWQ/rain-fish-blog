---
title: 常用的跨域解决方案
date: 2018-09-11 12:34:14
tags: 
- http
- 前端
categories: 
- 前端
---
---

# 什么是跨域

跨域是指一个域下的文档或脚本试图去请求另一个域下的资源。

# 如何跨域

在项目开发中，时常遇见浏览器接口请求需要跨域访问的情况，此时，我们该如何访问呢？

下面是几种常用的跨域解决方案:

## 通过JSONP跨域

接口通过jsonp格式请求，请求中需带上callback字段，传递回调方法名，服务端返回结果需要以回调参数的形式返回。

在页面上我们可以引入不同域上的js脚本文件，但是无法通过ajax访问接口，jsonp的原理是通过一个js脚本回调达到跨域请求的效果。

## 服务端设置响应头

服务端设置Access-Control-Allow-Origin响应头为 *，表示允许所有请求跨域访问，也可以设置为具体域名或IP，达到允许制定地址访问。

```

response.setHeader("Access-Control-Allow-Origin", "*");

```

此时需要加上前端header头 'Content-Type': 'application/x-www-form-urlencoded'

如果跨域请求需要携带cookie信息:

此时服务端需要设置响应头

```
response.setHeader("Access-Control-Allow-Credentials", "true");
response.setHeader("Access-Control-Allow-Origin", request.getHeader("Origin"));
```

而前端代码ajax请求需要加上withCredentials = true属性，允许携带cookie，

## 代理服务器

如果是前后端分离的项目，前端开发阶段可以通过webpack或者gulp构建本地服务器并设置转发。

如webpack配置

```
devServer: {
     historyApiFallback: true,
     noInfo: true,
     proxy: {
         '/menu': {
             target: 'https://other-server.example.com',
             secure: false
         }
     }
```

也可以手写一个node express代理转发,代码如下

```
const express = require('express');
const path = require('path');
const timeout = require('connect-timeout');
const proxy = require('http-proxy-middleware');
const app = express();

// 这里从环境变量读取配置，方便命令行启动
// HOST 指目标地址
// PORT 服务端口
// const { HOST = 'http://api.zhuishushenqi.com', PORT = '3300' } = process.env;

// 超时时间
const TIME_OUT = 30 * 1e3;

// 设置端口
app.set('port', 1337);

// 设置超时 返回超时响应
app.use(timeout(TIME_OUT));
app.use((req, res, next) => {
  if (!req.timedout) next();
});

// 静态页面
// 这里一般设置你的静态资源路径
app.use('/', express.static(__dirname));

// 反向代理（这里把需要进行反代的路径配置到这里即可）
// eg:将/api/test 代理到 ${HOST}/api/test
app.use('/api1', proxy({
  target: 'http://api.zhuishushenqi.com', pathRewrite: {
    '^/api1': '/'
  },
  changeOrigin: true
}));

app.use('/api2', proxy({
  target: 'http://chapterup.zhuishushenqi.com', pathRewrite: {
    '^/api2': '/'
  },
  changeOrigin: true
}))

// 监听端口
app.listen(app.get('port'), () => {
  console.log(`server running @${app.get('port')}`);
});
```

把这个js文件放在根目录，用node启动，在本地进行ajax请求时，加上特定的url标识，就能正常转发了。

如果使用nginx作为服务器，可以添加配置

```

upstream my_server {                                                         
    server 10.0.0.2:8080;                                                
    keepalive 2000;
}
server {
    listen       80;                                                         
    server_name  10.0.0.1;                                               
    client_max_body_size 1024M;

    location /my/ {
        proxy_pass http://my_server;
        proxy_set_header Host $host:$server_port;
    }
}

```

在这里我只针对了常用的几种跨域方式进行了介绍,还有不同类型的其他跨域方式，如同源跨域，设置domain，或者postMessage，或者window.name，这里就不一一介绍了。