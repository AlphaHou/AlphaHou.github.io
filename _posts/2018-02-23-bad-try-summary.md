---
layout:     post
title:      bad-try-record
date:       2018-02-23 19:00:00
author:     "Hou"
header-img: "img/post-bg-node.png"
tags:
    - record
---
失败的尝试
============


>今天原本的计划是跟着廖雪峰老师的js教程，把websocket做出来。但各种原因，是个失败的尝试。为了让今天显得不那么失败，还是记录一下，吸取教训。



## koa-vue-bootstrap

廖雪峰老师的websocket通过koa-vue-nunjucks-bootstrap的技术栈，以MVC的思想实现。但既然使用了vue，为什么不干脆直接mvvm呢？不但数据双向绑定直接就有现成的（成功免去使用nunjucks的麻烦），使用webpack可以直接打包一个bundle.js减少请求次数，甚至模块热替换使得开发异常爽快。

那么是遇到什么问题导致任务搁浅呢。使用webpack+vue需要监听8080端口并自动创建一个服务器，但是怎么让这个服务器实现成为websocketserver呢。。可能可以，但我不会，于是直接带来的问题是，需要同时监听两个端口。这在不了解websocket使用send方法传递消息情况下极大增加了开发难度。同时，查了很久没有找到vue如何接受post传来的信息，以及取出cookie数据。头痛

简单总结一下。mvvmc是可行的，但是需要想办法解决以上问题。通过vue+webpack+bootstrap，我们很容易做出需要的前端页面，但如何与后端联动是个大问题。目前能够想到的解决方案是，将打包好的页面放在后端，加上controller控制，用假数据模拟情景。