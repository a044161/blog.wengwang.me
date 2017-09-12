---
title: 记一次npm package插件开发
date: 2017-09-12 23:21:46
tags:
- JavaScript
categories: 
- Font-end
---

github地址：[https://github.com/a044161/pocket-tool](https://github.com/a044161/pocket-tool)

# 开篇闲扯

除了上次的五子棋开发的blog，好像很久没做一些思路的分享了，刚好也借这个机会做一次分享，理一理这次的开发思路。

<!-- more -->

# 为何要开发

在日常的开发中，其实自己经常会用到一些类型的判断，还有dom元素的操作，不想每次都复制来复制去，而且也是使用`ES6`来进行编写，为何不弄个公用的包呢，于是乎，在开发一个插件的过程中，我就停下来，改为开发一个自己的工具包了，取名为`pocket-tool`，也能借此机会熟悉一下`npm package`开发的流程。

# 目录结构

```
├─ core // 核心代码
├─ dist // 打包后的文件
├─ test // 单元测试
├─ .babelrc // babel的配置文件
├─ index.js // 入口文件
└─ rollup.config.js // rollup的配置文件
```
`core` 中的具体代码不展开说了，因为不是这次的重点。  
`dist` 是用来专门放置打包后的文件，在单元测试中我也是直接使用该目录下的文件，可能有人会在单元测试中使用`index.js`，我所想的是，一定要保证打包出来的文件也能够正常通过测试，所以之后可能会把两边同时引入，这样才能保证可靠性。  
`test`用来放置单元测试相关的内容，这部分会专门写一篇思路，这里不展开。  
`index.js`这个就是作为入口文件，里面只是引入了`core`的代码。  
`rollup.config.js`我这次选用了`rollup`作为构建工具，因为职责足够单一，足够轻量，也能帮我在打包过程中先移除无用的代码。

# 思路

`core`中根据业务需求对文件进行了拆分  
例如有个`utlis.js`的业务代码，内容如下
```
// core - utils.js 
 <some code>
 export default {
     ...
 }

```
最后将需要对外开放的内容通过`export`的方式导出，然后在`index.js`文件中进行引用

```
// core - index.js 
 import Utils from './utils';
 
 export default {
    Utils
 }
```
在最外层的入口文件`index.js`其实只是引入了`core`中的`index.js`，这样才能把相关的代码导入到入口文件中，如下
```
// index.js
import Core from './core/index.js';

export const Utils = Core.Utils;

export defualt Core;
```
按照这样的一种结构才能在构建工具中，将所有的文件打包到一个文件中，当用户`npm install`后，进行引用，其实也是引用`index.js`文件，最终调用的也是`core`中的方法。  
其实还是蛮简单的，不过还是要实践一下，没有最好，只有最适合的。

# 构建工具

其实在开发的过程中，会不由自主的想到，如果这时用户不是通过`import`的方式，或者说是不支持`ES6`的环境下呢，那就需要有一个最终的文件供用户调用。我也想过使用`webpack`，但是我这样的功能开发，不需要用到`webpack`那么多强大的功能，又要足够的简单，于是就引入的`rollup`。  
`rollup`的配置其实蛮简单的，而且我也只要满足几个需求就好  

* 支持`Es6`
* 打包为一个文件
* umd方式

配置如下
```

import babel from 'rollup-plugin-babel';

export default {
	input: './index.js', // 入口文件
	plugins: [ // 引入插件
		babel({
			exclude: 'node_modules/**',
			plugins: ['external-helpers'], // babel官方建议的一直方式
			externalHelpers: true
		})
	],
	output: {
		file: './dist/pocket-tool.js', // 输出文件
		name: 'pocket-tool', // 名称
		format: 'umd' // 用umd方式
	}
};

```
这里就足够我使用了，当然。。。因为这个`core`的功能是我之前就写好的，所以不存在开发的过程，所以，还漏了开发的方式，之后会进行补充。

# 单元测试

这里使用`mocha`+`should`  
详见：[https://wengwang.me/Font-end/Font-end/小试单元测试-mocha-should/](https://wengwang.me/Font-end/Font-end/小试单元测试-mocha-should/)

# 调用方式

在`package.json`中添加两个脚本
```
"scripts": {
	"build": "rollup --config rollup.config.js", // 打包
	"test": "npm run build && mocha" // 测试
}
```
到这里，一个`package`算是完成了，最后再提交到`npm`，通过命令`npm publish`，如果没有登录的话，还需要`npm login`，推送成功后就能在`npm`中搜索到啦。

# 一个好的习惯很重要

自测！自测！自测！  
在提交代码之前，一定要自测完全，一种方式是通过测试用例，一种是当做用户去使用，让每次提交都为最完美的一次提交。  
还有一个点就是，如果你每次要`npm publish`记得要修改`package.json`中的`version`，不然每次都要为了修改一个字段，而重新提交，我已经犯了好多次这样的错误。。。

# 结语

其实也蛮久没有折腾自己的东西了，每次自己瞎折腾总会发现一些有趣的内容，当然还是要总结一下折腾的过程，这样才算真正折腾了。