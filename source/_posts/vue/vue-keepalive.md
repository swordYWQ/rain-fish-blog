---
title: Vue性能优化之keep-alive
date: 2018-12-13 12:34:14
comments: true
tags: 
- vue
- 前端
categories: 
- 前端
---
在web项目中我们经常使用各种缓存在达到提高访问速度的效果，那么，在vue项目中如何使用组件缓存呢。

## keep-alive
keep-alive 是vue官方提供的缓存组件。它是一个抽象组件，在页面中使用keep-alive组件缓存,能有效提高页面响应速度，组件被卸载后不会被destroy掉，而是变成不活跃状态存在内存中，当再次访问组件时直接从内存中获取缓存渲染，而不是重新请求。

### 场景
一般使用keep-alive的场景为router-view路由组件或者component动态组件。

### 使用方式
1 只用单独的keep-alive包裹的话，会缓存router-view 渲染的所有组件
2 使用include绑定一个数组，只会缓存数组中name相同的组件
3 使用exclude绑定一个数组，会缓存除了数组中name相同的其他所有渲染的组件

``` bash
<keep-alive :include="cachePages" :exclude="excludePages">
  <router-view></router-view>
</keep-alive>
```

### 缺陷

能够实现打开后的组件进行缓存，但关闭标签页后，重新打开组件，组件仍是缓存后的组件，无法实现从内存中移除已缓存的组件。

### 解决方案

经过项目中的测试，发现如果需要比较正常开发多标签页的网站应用，比较适合的方法是使用component组件，组件由component渲染，使用一个数组对组件名进行保存，通过循环生成组件。这种方式能比较方便地实现度标签项目，但缺点是组件打开过多时会造成内存占用过多，因此最好对页面数量进行限制。
