---
title: 使用命令更新文件到服务器
date: 2019-04-01 12:34:14
tags: 
- html
- 前端
categories: 
- 前端
---
---

> 现在的前端开发大多会使用到模块化构建工具，每次打包然后手动上传，导致不必要的时间消耗，我们可以把常用的操作集成起来，通过一个命令搞定。

比如我们要将目录中的网站文件更新到服务器上，我们可以使用ftp-deploy库上传文件，也可以把它集成到webpack或者gulp自动化流程中，在打包完成后自动提交更新到服务器。

```js
let FtpDeploy = require('ftp-deploy')
let ftpDeploy = new FtpDeploy()
let config = {
  user: "", // 用户名
  password: "", // 密码
  host: "", // 服务器地址
  port: , // 端口
  deleteRemote: false, // 上传之前删除远端的所有文件
  localRoot: __dirname + '/dist', // 要部署的本地文件夹路径
  remoteRoot: '/public_html/', // 远程文件夹路径
  exclude: [], // 排除文件类型
  include: ['*', '**/*'] // 要上传的文件类型,这样写就是上传所有文件
}

ftpDeploy.deploy(config, function(err) {
  if (err) console.log(err)
  else console.log('finished')
})
```

这里注意一点，更新项目时，经常图片或者部分资源没有变化，此时我们不需要更新，所以可以在exclude中配置排除文件类型。