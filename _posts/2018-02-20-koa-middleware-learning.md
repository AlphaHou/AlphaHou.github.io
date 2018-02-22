---
layout:     post
title:      koa-middleware-learning
date:       2018-02-20 18:00:00
author:     "Hou"
header-img: "img/post-bg-node.png"
tags:
    - node
    - koa
---
koa 学习
============
>每次调用app.use()会在app对象中的middleware数组中添加一个函数。然后在listen方法中调用在application模块中注册的回调函数。该回调函数使用compose函数处理middleware。在compose函数中，返回一个能够依次调用中间件的函数，此函数会在listen函数中，作为构造server的参数。


## middleware中间件



以下是compose函数源码:

```
function compose(middleware) {
  // 错误处理
  if (!Array.isArray(middleware)) throw new TypeError('Middleware stack must be an array!')
  for (const fn of middleware) {
    if (typeof fn !== 'function') throw new TypeError('Middleware must be composed of functions!')
  }
  return function(context, next) {
    // last called middleware #
    let index = -1
    return dispatch(0)
    function dispatch(i) {
      if (i <= index) return Promise.reject(new Error('next() called multiple times'))
      // 当前执行第 i 个中间件
      index = i
      let fn = middleware[i]
      // 所有的中间件执行完毕
      if (i === middleware.length) fn = next
      if (!fn) return Promise.resolve()
      try {
        // 执行当前的中间件
        // 这里的fn也就是app.use(fn)中的fn
        return Promise.resolve(fn(context, function next() {
          return dispatch(i + 1)
        }))
      } catch (err) {
        return Promise.reject(err)
      }
    }
  }
}
```
