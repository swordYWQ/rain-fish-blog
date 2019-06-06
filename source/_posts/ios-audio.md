---
title: ios上无法自动播放audio音乐
date: 2018-09-21 12:34:14
tags: 
- 移动端
- 前端
categories: 
- 前端
---
---

# ios上无法自动播放audio音乐

在网页中加入音频作为背景音乐自动播放时，在ios设备中会出现无法自动播放的情况。

由于ios设备限制，用户未有页面交互时，无法自动播放音乐，因此需要手动监听touchstart事件触发播放。

如果音乐播放时手动触发的，不会出现这种情况。

在ios设备中做如下监听:

```
 document.addEventListener("DOMContentLoaded", function() {
      function audioAutoPlay() {
        document.getElementById('audio').play()
      }
      audioAutoPlay();
    });
    //--创建触摸监听，当浏览器打开页面时，触摸屏幕触发事件，进行音频播放
    document.addEventListener("touchstart", function() {
      function audioAutoPlay() {
        document.getElementById('audio').play()
      }
      audioAutoPlay();
    });
```
