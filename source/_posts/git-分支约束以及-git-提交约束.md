---
title: git 分支约束以及 git 提交约束
date: 2018-06-24 17:39:47
photos:
tags:
categories:
- Others
---

# 分支约束

可以分为3种类型的分支：主分支、开发分支和临时性分支

<!-- more -->

## master 分支 - 主分支

必须有且仅有一个的分支，是正式版本或者真正提供给用户使用的正式版本，都在该分支上。

## develop 分支 - 开发分支

日常的开发都在 develop 分支上进行。  
当开发完成，需要发布到正式版本的时候需要和 master 进行合并。

## 临时性分支

分为3种类型：feature 分支 - 功能分支；release  分支 - 预发布分支；fixbug 分支 - 修补 bug 分支。  
上述的分支都为临时性的，使用建议做合并、删除操作，尽量保证仓库只保留 master 分支和 develop 分支。

### feature 分支 - 功能分支

在 develop 分支上进行开发的时候，有时需要临时增加新的功能，但是又不想影响当前的 develop 分支，因此，可以选择基于 develop 分支重新拉取一条 feature 分支进行开发，开发完成后再并入 develop 分支。  
命名方式 `feature-*`

### release 分支 - 预发布分支

在将要发布到正式版本之前，可以选择基于 develop 分支拉取 release 分支进行预发布，对功能进行走查，测试等，但 release 分支验证通过后，可合并到 matser 分支进行发布。如果这个时候发现问题，建议统一在 develop 分支上进行修复，然后再合并到 release 分支上。  
命名方式 `release-*`

### fixbug 分支 - 修补 bug 分支

发布到正式版本后会出现 bug，这时需要基于 master 分支拉取一个 fixbug 分支用来修补 bug，修补 bug 完毕后再合并 master 和 develop 分支。  
命名方式 `fixbug-*`

## tag - 打标签

一般是用在发布后，再对应的提交上打上标签，用于记录该提交是针对 `xx` 版本发布。  

`git tag 1.0.0`

> [Git 分支管理策略](http://www.ruanyifeng.com/blog/2012/07/git.html)  
> [创建标签](https://git-scm.com/book/zh/v1/Git-%E5%9F%BA%E7%A1%80-%E6%89%93%E6%A0%87%E7%AD%BE)  

# 提交约束

拥有好的 commit message 和 change log 对追溯是很有帮助的

## commit message 编写

可以使用 angular 开发团队的编写规范，将 commit message 分为三个部分：Header、Body 和 Footer

```
<type>(<scope>): <subject>
// 空一行
<body>
// 空一行
<footer>
```

### Header

- type：说明 commit 的类别，包含下面几个标识

```
feat：新功能（feature）
fix：修补bug
docs：文档（documentation）
style： 格式（不影响代码运行的变动）
refactor：重构（即不是新增功能，也不是修改bug的代码变动）
test：增加测试
chore：构建过程或辅助工具的变动
```
- scope：影响范围
- subject：简短藐视，不超过50个字符

### Body

对本次提交的详细描述

### Footer

- 不兼容变动
- 关闭 Issue


可以使用 [commitizen](https://github.com/commitizen/cz-cli) 编写 commit log 以及 [validate-commit-msg](validate-commit-msg) 校验是否符合格式

> [Commit message 和 Change log 编写指南](http://www.ruanyifeng.com/blog/2016/01/commit_message_change_log.html)  
> [Commit Message & Change Log](https://www.jianshu.com/p/00c9ec4e552e)


其实定义一些规范，可能刚开始的时候会比较麻烦，但是在多人协作中是很有必要的，可以避免各种各样的情况发生。