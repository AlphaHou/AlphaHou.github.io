---
layout:     post
title:      koa-middleware-learning
date:       2018-02-20 20:00:00
author:     "Hou"
header-img: "img/post-bg-rwd.jpg"
tags:
    - node
    - koa
---
koa 学习
============

## router路由

>使用koa-router前需要在控制台npm install koa-router -S，之后通过require（'koa-router')的方式引入。引入的koa-router是个函数，所以需要声明变量接住返回的router。在定义好router方式后，通过app.use(router.route())将router注册进中间件。

kao-router提供有get(url,async fn),post(url,async fn)方法。

如果在主程序中不断定义router必定导致程序臃肿，难以维护，因此有必要想办法将路由地址和对应的方法模块化，这样不但能够复用代码，同时也使得主程序变得简洁，耦合低。

模块化的一个办法是，在最上层路径添加一个controller.js的控制器，以及一个controllers的文件夹，里面放置存有路径和方法的js文件。这些模块的输出标准为一个{‘METHOD /url': async fn}的对象。在controller.js中，通过 `<fs.readdirSync(__dirname+'/controllers/')>` 扫描文件夹内的文件，得到files后，filter帅选js文件。循环遍历得到的数组，通过 `<require(__dirname+'/controllers/'+f)>` 得到对象。循环判断对象中的键值，使用对应的方法。这一步骤结束后，我们就可以放心输出controller.js中的router.route()了。在主程序中引入controller.js，在合适位置调用app.use(controller())，大功告成。

以下是controller的代码：

```
var fs=require('fs')

function addMapping(router, mapping) {
    for (var url in mapping) {
        if (url.startsWith('GET ')) {
            var path = url.substring(4)
            router.get(path, mapping[url])
        } else if (url.startsWith('POST ')) {
            var path = url.substring(5)
            router.post(path, mapping[url])
        } else {
            console.log(`invalid URL: ${url}`);
        }
    }
}

function addControllers(router) {
    var files = fs.readdirSync(__dirname + '/controllers');
    var js_files = files.filter((f) => {
        return f.endsWith('.js')
    })

    for (var f of js_files) {
        let mapping = require(__dirname + '/controllers/' + f)
        addMapping(router, mapping)
    }
}

module.exports = function (dir) {
    let
        controllers_dir = dir || 'controllers', // 如果不传参数，扫描目录默认为'controllers'
        router = require('koa-router')()
    addControllers(router, controllers_dir)
    return router.routes()
}
```


##总结

在addController中，我们得到了对象数组，在addMapping中，我们遍历这些对象，在router中注册对应的请求和方法，实现模块化的路由。