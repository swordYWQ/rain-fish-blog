---
title: vue路由懒加载
date: 2019-02-05 12:34:14
comments: true
tags: 
- vue
- 前端
categories: 
- 前端
---

## webpack打包后体积过大

我们知道，vue-cli项目默认使用webpack进行打包，打包的时候，会把所有的css,js,vue文件打包成js，页面比较多的时候，最后会生成一个比较大的js文件。

这样的话，在网站进入的时候，需要等待这个大文件下载完成才能进入页面。

## 解决方法

这时我们就可以考虑路由懒加载了，路由懒加载是通过异步加载路由组件，打包后会为每一个组件生成一个js文件，切换路由的时候才会加载该js，减少公用js体积，加快首页渲染速度。

## 使用方法

在配置路由组件的时候，把默认的配置修改一下

```js
import Login from '@/login.vue'
{
  path:'/login',
  name:'login',
  component: Login
}
```
修改为
```js
{
  path:'/login',
  name:'login',
  component:(resolve) => require('***/login.vue',resolve)
}
```

