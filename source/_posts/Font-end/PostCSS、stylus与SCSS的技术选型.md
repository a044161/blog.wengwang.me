---
title: PostCSS、stylus与SCSS的技术选型
date: 2017-07-09 23:55:00
tags:
- CSS
categories: 
- Font-end
---

# 开篇闲扯

最近因为在做自己的side project，又想回到了想以前那样，每做一些事情就会引发一些思考，个人觉得这篇文章写的有点打酱油，之后是希望自己能够应用起来之后，再做完整的一次分享。

# PostCSS
#### 简介  
PostCSS 其实和其它两个有所不同，它是作为一个平台，开发者可以开发任何自己需要的插件。官方写的是 “A tool for transforming CSS with JavaScript”，一个用JavaScript转换css的工具。
#### 优点

* 扩展性强
  
* 兼容性强  

* 功能全面

#### 缺点

* 配置繁琐

* 学习曲线相对较陡

# stylus

#### 简介  

与SCSS类似，在写法上与原生的css还是有很大的不同，写法很像在写js，又很像在写python。

#### 优点

* 更加健壮

* 写法更接近于写js

* 功能强大

#### 缺点

* 写法需要一定的适应时间
* 学习曲线相对较陡

# SCSS

####简介

预处理框架，提供丰富的语法，上手较快。

#### 优点

* 功能强大

* 上手较快

* 语法接近于原生css

* 语法简洁

#### 缺点

* 依赖ruby

* node下安装麻烦

# 三者对比
三者对比的话，我觉得如果从一些上手程度或者配置的过程进行对比的话，一定是SCSS和stylus要优于PostCSS。
stylus和SCSS能不能说在一定程度上他们是类似的，解决的也都是类似的问题，而PostCSS就有所不同，PostCSS提供的是一个平台，而SCSS和stylus提供的是一种新的语法。  
stylus和SCSS也许只能使用他们所提供的能力来做相对应的事情，但是PostCSS是可以给你机会来做更多你想做的事情，我想这就是他们三者最大的区别吧。

# 最终选择  
经过这几天的一个思考，我没有写相对应的demo，但是我一直在想，PostCSS既然是一个平台，那么它的可能性是更大的，学习曲线有可能是由陡到缓。  
我一开始一直在犹豫PostCSS的时候是在纠结，我用了PostCSS，为什么我还要引入相对应的SCSS的插件呢，于是就开始看有没有其它的代替方案，也在不断的看css-next，后来发现我这样的想法其实是愚蠢的，因为我一直忽略了一点，PostCSS是一个平台，平台所提供的可能性是更大的，我不能将PostCSS的某些功能和SCSS、stylus进行对比，我可以依旧在PostCSS上面使用SCSS或者stylus的语法，但是我还能使用其它更加强大的功能，形成一个我的工具包，甚至找不到合适的插件，我可以自己编写插件，这是SCSS或者stylus所无法做到的。因此我最终的选择会是PostCSS，当然，我一直秉承的思想就是要选择适合自己的，适合自己的才是最好的。  

# DEMO
简单的以PostCSS和gulp的使用做了一个demo，不过既然是对比，之后还会再继续加上SCSS和stylus的demo。  
https://github.com/a044161/demo-or-test/tree/master/gulp

# 结语
其实这篇文章是7月4号时创建的，后来因为工作上比较忙，也就没有继续完成下去，到了周末就想在家好好休息，然后也在思考这几个问题，最后还是倾向于PostCSS。  

[![](https://badge.juejin.im/entry/5966de11f265da6c332419e2/likes.svg?style=flat-square)](https://juejin.im/entry/5966de11f265da6c332419e2/detail)