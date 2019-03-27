---
title: http参数格式转换
tags: 
- http
- 前端
categories: 
- 前端
---
---

# http请求参数格式化

http请求时，对参数格式有一定要求

get请求参数衔接在请求url后，使用问号(?)分隔并使用与符号(&)连接多个参数

post请求中，当请求方式为formdata时，也需要转换成key=value&key1=value1这样的形式

通常http库会对参数进行一定程度的封装，对get请求不需要特别处理，但post请求有时需要手动处理格式，如axios

此时我们在请求发出前的通用方法中加入如下参数转换方法，即可对参数格式进行转换。

```js
function (obj) {
     var str = [];
     for (var p in obj) {
         str.push(encodeURIComponent(p)"="encodeURIComponent(obj[p]));
     }
     return str.join("&");
 }
```
