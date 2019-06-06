---
title: 常用的网站性能优化方法整理
date: 2019-04-01 12:34:14
tags: 
- html
- 前端
categories: 
- 前端
---
---

## 页面

### 压缩文本

通过gulp或webpack插件压缩html,js,css文本中的空格，尽可能减少文本体积。

### 内联关键css或js

通过内联关键css或js减少页面请求，同时加快展现。

在gulp中使用gulp-embed进行内联。

## 图片

### 压缩图片

使用工具tinypng或者智图软件对页面中使用的图片进行压缩，减小图片体积。

### 使用jpg图片

对于不需要透明的png图片，转换成jpg可以拥有更小的体积。

### 使用webp图片

google的新型图片格式webp拥有相同的展现以及更小的体积。

https://developers.google.com/speed/webp/docs/using

<!--在gulp中可使用gulp-imageisux插件进行压缩和生成webp文件。(gulp-webp批量压缩时报错)-->
在scss中引用的图片，通常使用scss或less等minix进行处理：
```scss
@mixin webp($url) {
  background-image: url($url);
  .webpa & {
    background-image: url($url + '.webp');
  }
}

.bg {
  @include webp('./img/bg.jpg')
}
```

在less中，大同小异：
```less
.webpbg(@url) {
  background-image: url(@url);
  .webpa & {
    background-image: url('@{url}.webp');
  }
}

.bg {
  webpbg('./img/bg.jpg');
}
```

在html中使用的图片兼容:




### 小图base64

对小于10KB的图片，可以通过gulp插件或webpack插件将其转换为base64文本，减少请求。

注意： 过大的图片转成base64得不偿失。

### 尽可能用css实现

对于纯色的或基础渐变等效果，如果能使用css实现，尽可能使用，以减小图片大小。

### 图片懒加载

对于不需要再首屏展现的图片，使用图片懒加载，当用户滚动屏幕时，通过js修改img的src或者设置style的background-image(可以使用lazyload插件处理),减少首屏加载的图片数量。

## js

### 尽可能使用css动画

css动画的性能比js动画要高，如果可以用css实现，尽可能这样。

### setInterval或requestAnimationFrame

在需要多次执行方法时，用setInterval而不用setTimeout。当然，使用requestAnimationFrame更好。

### 防抖和节流

在一个监听的频繁事件中，比如滚动的回调事件中，由于事件触发的频率过快，而实际上我们不需要重复那么多次，可以使用防抖和节流。

## 字体

### 压缩字体文件

通过字体文件都会比较庞大，但我们大部分时候都用不上所有字体，所以可以使用font-spider等工具，根据html页面中使用该字体的文字，将字体文件进行压缩处理，以减小体积。

## 服务端

### gzip压缩

使用gulp或webpack gzip压缩插件，对项目中的js文件，等资源进行gzip压缩，并在服务端上配置gzip响应头支持,能大幅减小项目文件大小。

## 进阶

### 服务端缓存

开启静态资源缓存，对不常变动的静态资源进行缓存，保证在页面二次加载时能快速响应。(nginx,node)

### mainfest缓存

在mainfest文件中配置缓存文件，将文件缓存到本地，第二次访问时直接从缓存中读取。

### serviceworker

在支持serviceworker的浏览器中，使用渐进式增强处理，对页面中的资源文件或者api请求进行缓存或更新，以加快请求速度。

## 工具

### pageSpeed

前往pageSpeed网页，输入你的网站外网地址，对页面进行性能检测，它会给出你的网站性能评分和一些页面针对性的建议。

## 拓展

### 缓存一切不需要变动的(参考百度)

除了将各类静态资源进行缓存处理，将页面中的dom也进行缓存，访问html页面时优先从本地获取dom缓存，否则向服务端请求。