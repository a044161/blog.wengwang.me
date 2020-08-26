---
title: 简历
---

# 个人信息

姓名：翁旺

岗位：前端开发工程师

联系方式：

* 电话：18606933816
* github：[a044161](https://github.com/a044161)
* 个人博客：[https://blog.wengwang.me/](https://blog.wengwang.me/)
* 邮箱：[wengwangjay@126.com](mailto:wengwangjay@126.com)

学历：本科/网络工程/2016 年 6 月  

现居住地： 福建福州

工作经历：

* 网龙：2015 年 10 月 - 2018 年 09 月
* 中电（福建）健康医疗大数据运营服务有限公司：2018 年 09 月 - 至今

# 一些介绍

## 前端脚手架

> @cecdataFE/create-tpl
> @cecdataFE/webpack-script

统一前端开发的整体目录结构以及技术栈，规定前端开发技术栈（react、scss），规定目录结构、开发模式、构建产物、部署方式，开发了`create-tpl`作为构建脚手架的命令行工具，目前包含了react、koa、electron、dataVision（可视化大屏相关）的脚手架，开发了`webpack-script`命令行工具用来统一前端项目的构建方式。最终统一了公司前端的整体工程，避免了出现技术统一的情况，简化了开发人员新建项目和开发新项目的成本

## 前端自动化 

> @cecdataFE/ci

创建项目太麻烦！发布项目太麻烦！我想要一中更加优雅的方式-自动化的方式完成创建项目、发布项目。使用nodejs作为主要技术栈，在创建项目的时候使用`create-tpl`进行脚手架的生成，调用gitlab的api进行项目以及分支（master、test、develop）的创建，将项目推送到gitlab上，nginx创建对应的配置文件（生产、测试、开发），将nginx文件推送到对应的服务器上，以此完成项目的创建。在发布项目的时候，根据gitlab上对应的项目id进行拉取，然后进行构建，再将构建的产物推送到对应环境的文件夹下，以此完成发布项目，所有环境的代码都与gitlab上对应的分支保持一致。最后让项目的创建、发布变得更加简单，开发人员只需根据进行简单的操作就能完成繁琐的工作，并且可以提供接口给其他方进行调用，来自行构建前端项目，并获取构建产物

## 前端开发桌面应用 

> @cecdataFE/all-in-one

有了上面的CI，但是缺少一个入口。于是需要将所有的内容都进行集合，使用桌面应用赋能前端开发人员。使用electron作为主要技术栈，将create-tpl，ci，物料库、组件库进行整合，通过ci提供的接口进行项目的创建、发布、下载。物料库也可以直接在页面中进行下载，指定目录，生成对应的业务代码。通过iframe的方式浏览组件库，会更加的方便，省去了打开浏览器的方式。我们通过一个桌面应用，赋能前端开发人员，简化工作

![https://oss.wengwang.me/images/20191221131727.png](https://oss.wengwang.me/images/20191223173702.png)
![https://oss.wengwang.me/images/20191221131912.png](https://oss.wengwang.me/images/20191221131912.png)

## 前端“微服务”

目前公司的业务系统更像是整个体系内的子系统，需要定义统一的入口，需要将所有的系统进行整合。得益于一开始对所有前端开发技术栈的统一，再结合`ci`，开发一个统一入口的壳，将所有项目的业务代码进行拉取，重新生成路由文件，合并各个项目的环境配置信息，再对组件使用懒加载，这样子就将所有前端项目进行了整合。这也像另外一种形式的前端“微服务”，各业务系统保持独立的存在以及开发，最后再对系统进行代码整合，在异地或者内部部署的时候只需要部署一个前端项目。


## 前端团队管理

刚入司时前端只有我一个人，从0到1，一点点的搭建前端基础部分以及前期的业务系统开发，目前一共有前端7人（实习生1名），业务范围涵盖前端基础建设、业务系统开发、数据可视化大屏，保持两周一次没有规则的分享，让每个人都能根据自己的喜好做自己喜欢的事情，也有小英语老师。尽可能的让大家能够找到自身的价值，高效的工作。

## 项目管理

负责公司运营平台项目管理，主要涵盖大数据服务平台、数据可视化大屏、兄弟公司部分前端系统开发。比较有意思的项目在数据可视化大屏项目，我们接到了数据可视化大屏以及决策系统的相关任务，前期也是各种手写代码，非常麻烦，而且内容枯燥物料且繁琐，后续参照了竞品datav、帆软等可视化编辑器，我们自研了一个“太初”，用于开发数据可视化大屏以及决策系统，目前已投入到实际的项目中使用，解放了大部分的前端人力成本，将这些成本移到了UI。目前该项目也要进入立项阶段。


# 技能栈

* HTML、CSS、JavaScript
* ReactJS
* NodeJS