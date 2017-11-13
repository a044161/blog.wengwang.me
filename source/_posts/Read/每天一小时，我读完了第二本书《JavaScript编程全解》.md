---
title: 每天一小时，我读完了第二本书《JavaScript编程全解》
date: 2017-11-13 20:37:42
photos:
- https://oss.wengwang.me/images/wengwang_me/hexo/Read/%E3%80%8AJavaScript%E7%BC%96%E7%A8%8B%E5%85%A8%E8%A7%A3%E3%80%8B.jpg
tags:
- 每天一小时
categories:
- Read
---

[![](https://badge.juejin.im/entry/5a09ab34f265da430d57844c/likes.svg?style=flat-square)](https://juejin.im/entry/5a09ab34f265da430d57844c/detail)

# 开篇闲扯
距离上次（2017年10月29日）阅读完第一本书，再到现在的第二本书我已经坚持了34天。这段时间工作上的事情比较多，到了周末有时就比较偷懒，早上就多睡了一会，这周日也没有继续阅读，因为还在加班，实在太累了，想要把事情快点做完这样才会对之后的 API 对接、接口联调、QA测试预留足够多的时间。所以才让自己牺牲掉周末的时间。好像有点扯远了。不过每天一小时的计划还有继续进行，虽然有那么一两天时间在偷懒，让自己睡个好觉。不过还是坚持读完了这本《JavaScript编程全解》。  
个人觉得这本书有点啰嗦，比较适合刚刚入门的同学。  
<!-- more -->

在线笔记：[http://book.wengwang.me/read_2/](http://book.wengwang.me/read_2/)  
购买地址：[京东](https://item.jd.com/11361718.html?dist=jd) [亚马逊](https://www.amazon.cn/%E5%9B%BE%E7%81%B5%E7%A8%8B%E5%BA%8F%E8%AE%BE%E8%AE%A1%E4%B8%9B%E4%B9%A6-JavaScript%E7%BC%96%E7%A8%8B%E5%85%A8%E8%A7%A3-%E4%BA%95%E4%B8%8A%E8%AF%9A%E4%B8%80%E9%83%8E/dp/B00GWAVZ7O/ref=sr_1_1?ie=UTF8&qid=1510577627&sr=8-1&keywords=JavaScript%E7%BC%96%E7%A8%8B%E5%85%A8%E8%A7%A3)

# 概要

### 作者介绍

- 井上诚一郎：Ariel NetWork 股份公司 CTO，出过多本书
- 土江拓郎：额。。。是井上诚一郎的同事
- 滨边将太：2009年加入雅虎公司
- 陈莜烟【译者】：长期从事对日软件外包工作，使用 Java 与 JavaScript 的开发人员

### 内容介绍

涵盖了 JavaScript 开发中各个方面的主题。本书的一大特色就是对 JavaScript 语言的语法规则进行了细致的说明（这个确实很细致，但也有点啰嗦），适合 JavaScript 开发初学者系统入门、有经验的 JavaScript 开发者深入理解语言本质。  

这本书也写着“日本 JavaScript 圣经级教材”，嗯，确实是个教材，我个人觉得还是比较适合初学者入门使用，有开发经验的同学可以用来巩固，但我觉得红宝书更适合有经验的开发人员，等我二刷一下红宝书再来分享。  

所以这本书很多内容我都是跳着看，还有就是有选择性的记录，可能一开始会记录的很详细，但是看了几章之后我就开始选择性的记录了，可能会发现有的部分内容很少，有的部分内容比较多。

# 浓缩一下

## JavaScript 概要
大致就介绍了一些 JavaScript 的背景啥的，我也没怎么看，可以自行跳过

## JavaScript 的语言基础
这部分对 JavaScript 的语言各方各面进行了很详细的分析，包括数据类型、语句、表达式、运算符、变量、闭包等。介绍的很全面，我是选择其中一些对我来说比较陌生或者比较重要的内容来看，但是我觉得很重要的闭包的内容，讲解的就不是很详细。感觉可以直接总结成一句话“闭包的前提条件是需要在函数声明的内部声明另一个函数（即嵌套的函数声明）。”  
这部分就是对 JavaScript 的基础进行了很全面的结束，各种语句的用法，运算符的用法啥的。也存在一些平时比较少用到的点。比如实现不可变对象的方法，通过`Object.seal` 和 `Object.freeze`，还有各类型的隐形转换也做了很全面的结束。  
是值得细细学习的一个部分，但还是觉得红宝书的更好，哈哈哈哈。

## 客户端 JavaScript 
这部分主要是介绍了 JavaScript 的 DOM、事件还有库的使用。其中 `console` 用来进行性能分析的方法也不错。还有 DOM 标注的定义，还有一个印象比较深的 `XPath` 虽然说没怎么用上，但是感觉是蛮新奇的一种方式，还有就是如何避免过多的浏览器重绘，是通过 ` document.createDocumentFragment` 来进行优化。  
库的部分我就没看了，主要是介绍 `jQuery` 的用法。

## HTML5
这部分主要是介绍了 HTML5 中新增的接口，我只稍微过了一下，主要看了一下 `WebSocket` 和 `Web Workers`，`WebSocket` 已经在毕业设计中使用过了，`Web Workers` 还没用过，也能够用来实现窗口间的通信。所以这个部分我只重点关注了这两个部分。

## Web API、服务器端 JavaScript
这部分我就是彻底放弃了，哈哈哈哈，因为对于我来说吸引力不大，也做过 NodeJs 的开发，所以也没啥必要去了解啦。

# 结语
这部书，推荐度不是很高。但是对于刚入门的同学我还是比较推荐的。最后还是希望大家能够支持正版。  

在线笔记：[http://book.wengwang.me/read_2/](http://book.wengwang.me/read_2/)  
购买地址：[京东](https://item.jd.com/11361718.html?dist=jd) [亚马逊](https://www.amazon.cn/%E5%9B%BE%E7%81%B5%E7%A8%8B%E5%BA%8F%E8%AE%BE%E8%AE%A1%E4%B8%9B%E4%B9%A6-JavaScript%E7%BC%96%E7%A8%8B%E5%85%A8%E8%A7%A3-%E4%BA%95%E4%B8%8A%E8%AF%9A%E4%B8%80%E9%83%8E/dp/B00GWAVZ7O/ref=sr_1_1?ie=UTF8&qid=1510577627&sr=8-1&keywords=JavaScript%E7%BC%96%E7%A8%8B%E5%85%A8%E8%A7%A3)

下一本书我已经选好了，是我的薄弱点，那就是《啊哈！算法》。希望自己能够坚持下去，每天一小时。