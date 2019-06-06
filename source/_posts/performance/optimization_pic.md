---
title: 将图片以base64形式嵌入html中提升渲染速度
date: 2019-03-15 12:34:14
tags: 
- html
- 前端
categories: 
- 前端
---
---

# 页面图片优化

通常引入图片的时候，会触发多个http请求图片资源，使得页面渲染效果会变差，图片可能会出现空白或层次渲染的情况，体验效果会很差。

# 将图片转换为base64编码

把图片以base64格式后嵌入html，页面渲染速度会更快，图片是直接展现，使得展现效果会更好。

# 缺点

当然，转化为base64格式的图片虽然能提高渲染体验，但也存在一些缺点，由于图片转化成了字符串编码嵌入html中，简介提升了html文件的体积。如果图片较大或较多时，加载html会花费更长的时间，反而会降低初次加载的速度。

# 解决方案

对小于10kb的图片，我们使用base64字符串编码，对大于10kb的图片，我们正常加载或者针对其他方面做优化。

# 使用方法

在使用webpack的项目中，可以通过配置url-loader对图片资源做出处理，小于limit的图片将会转化为base64格式，limit可以根据需要酌情修改。

```js
{
        test: /\.(png|jpe?g|gif|svg)(\?.*)?$/,
        loader: 'url-loader',
        options: {
          limit: 100000,
          name: utils.assetsPath('img/[name].[hash:7].[ext]')
        }
 }
```

引用时:

```html
<img src="a.png" alt=""/>
```

编译后:

```
<img src="data:image/png;base64:iVBORw0KGgoAAAANSUhEUgAAADg" alt=""/>
```

能明显看到src的内容被修改为base64格式。