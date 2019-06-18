---
title: vue-router权限管理
date: 2019-06-06 12:34:14
comments: true
tags: 
- vue
- 前端
categories: 
- 前端
---

> 项目中我们经常要对页面做权限管理，在这里，我完成使用vue-router的addRoutes方法对页面做权限管理。

## 将权限保存到本地

> 在登录或获取权限的回掉方法中将权限信息保存到本地，方便刷新后继续使用。

```js
(res)=>{
    localStorage.permissionArr = res.permission
}
```

## 基本路由配置

我们的路由主要分为两种，如登陆页、错误页、404页、无权限页等路由，属于不需要用户权限也能访问到的；

另一种是后台传递过来的用户权限，我们需要做权限管理的页面，这一部分我们使用addRoutes动态添加。

在路由初始化前，通过判断本地路由是否存在，初始化不同的路由

> 权限判断通过每个路由对象的meta对象中设置的属性permission

```js
// 路由配置
var baseRoutes =[/*基础路由(无需权限 如登录)*/]
var routes = [{
    path: 'path',
    name: 'pathName'
    component: ...,
    children: [{
        path: 'path1',
        name: 'path1',
        meta: {
            permission: 'permissionString'
        },
        compoenent: ...
    }]
}]

export const createRouter = r => new VueRouter({
    model: 'history',
    routes: r
})

var router = null
if (localStorage.permissionArr) {
    let newPermissionRoutes = getPermissionRoutes(); //路由权限
    router = createRouter([...newPermissionRoutes,...baseRoutes])
} else {
    router = createRouter([...baseRoutes])
}

export default router
```

## 递归获取有权限的树

> 在这里使用一个递归函数去匹配路由树,将没有权限的路由过滤掉。

```js
// 获取有权限的路由(meta不存在permission属性的或者permission属性存在并有权限的)
const getPermissionRoutes = function (config = routes) {
    let route = []
    let permissionArr = localStorage.permissionArr ? JSON.parse(localStorage.permissionArr) : {} // 权限
    config.forEach((item) => {
        let obj = {}
        // 存在meta和permission属性
        if (item.meta && item.meta.hasOwnProperty('permission')) {
            // 有权限
            if (permissionArr[item.meta.permission]) {
                obj = Object.assign({}, item)
                if (item.children && item.children.length > 0) {
                    obj.children = Object.assign([], getPermissionRoutes(item.children))
                }
                // 路由为重定向路由且子集长度大于0时，修改重定向路由为第一个子路由
                if (obj.redirect && obj.children.length > 0) {
                    obj.redirect = obj.children[0].path.indexOf(':') != -1 ? obj.redirect : obj.children[0]
                }
            }
        } else {
            obj = Object.assign({}, item)
            if (item.children && item.children.length > 0) {
                obj.children = Object.assign([], getPermissionRoutes(item.children))
            }
            // 路由为重定向路由且子集长度大于0时，修改重定向路由为第一个子路由
            if (obj.redirect && obj.children.length > 0) {
                obj.redirect = obj.children[0].path.indexOf(':') != -1 ? obj.redirect : obj.children[0]
            }
        }
        if (Object.keys(obj).length > 0) {
            route.push(obj)
        }
    })
    return route
}
```

## 路由重定向

> 通常我们会在无具体页面的父级路由重定向到子集第一个路由，但由于权限不确定性，要重定向的页面也是不确定的，在这里可以在递归方法中修改redirect的值为第一个有权限的子路由

如果子路由是动态路由时不做修改。

```js
if (obj.redirect && obj.children.length > 0) {
    obj.redirect = obj.children[0].path.indexOf(':') != -1 obj.redirect : obj.children[0]
}
```

## 路由重复添加

> 注销重新登录时路由重复添加,可创建一个新的路由对象,将其matcher赋值给当前路由，通过重新创建一个路由对象，将路由对象的matcher赋值给当前路由达到重置路由的效果

```js
   let newPermissionRoutes = getPermissionRoutes(); //路由权限
   self.$router.matcher = createRouter([...baseRoutes]).matcher;
   self.$router.addRoutes(newPermissionRoutes); // 添加路由
```

## 路由守卫判断权限

```js
// 所有支持的路由，用作路由匹配确认是否存在，不用做页面跳转,由于baseRoutes存在*全局匹配，所以把它放在最后
const usefulRouter = createRouter([...routes, ...baseRoutes])

router.beforeEach((to, from, next) => {
    let token = localStorage.token
    // 无需权限即可访问的路由
    if (to.name == 'login' || to.name == 'noright' || to.name == 'preview') {
        next()
    } else {
        if (token) {
            // 匹配到路由且匹配数大于1（因为baseRoutes中有一个*全匹配）
            if (to.matched.length > 1) {
                next()
            } else{
                // 与默认有所有权限的路由匹配，判断路由是不存在还是无权限
                const matched = usefulRouter.match(to).matched
                if (matched.length > 1) {
                    next('/noright')
                } else {
                    next()
                }
            }
        } else {
            next('/login')
        }
    }
})
```