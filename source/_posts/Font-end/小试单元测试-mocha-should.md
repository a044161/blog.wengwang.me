---
title: 小试单元测试-mocha+should
date: 2017-09-12 23:21:24
tags:
- JavaScript
- Unit Test
categories: 
- Font-end
---

github地址：[https://github.com/a044161/pocket-tool](https://github.com/a044161/pocket-tool)  

基于[记一次npm package插件开发](https://wengwang.me/Font-end/Font-end/记一次npm-package插件开发/)写的。

# 开篇闲扯

很早就想把玩一下单元测试了，可是一直没找到机会使用，这次刚好兴致来了就简单的使用了，做一下小结，文章不会具体提到该如何使用，而是主要用来描述整个思路，因为各种`API`官方文档直接就能查到，所以没有啥意义。

<!-- more -->

# 为何要进行单元测试

每个人想要进行测试的目的都不同，可能大部分是为了想要验证某个函数最后的输出值是否正确，拥有较高的覆盖能够保证代码在后期的迭代过程中，增加或者删减某些功能时，可以通过之前写的测试用例进行验证，避免意外情况的发生。还有一种是先写好了结果，然后再进行开发，来保证代码符合预期。当然这都是我个人的一些理解。  
回到我这次想要进行单元测试的目的，一种是可以了解单元测试，还有就是对我代码的可靠性进行验证。  
选用的是`mocha`和`should`，为什么选择这两种呢，因为`mocha`足够上手快、功能全。`should`是因为这次测试中只需要用到`should`的就足够了。一直坚持的就是，不一定要选择大而全，而是要选择最适合的。

# 目录结构

```
├─ core 
├─ dist
├─ test // 单元测试
│   │ 
│   ├─ utils // 因为core中有一个模块为utlis，因此这里存放的是utils相关的测试用例
│   ├─ mocha.opts // mocha的配置文件
│   └─ utils.test.js // utils的测试代码
├─ .babelrc
├─ index.js
└─ rollup.config.js
```

这里展示的目录只要`test`下的。  
我是根据实际项目中，按照模块进行了拆分，这里只对`utils`模块进行了用例的编写。  
`utlis`下有根据想要进行单元测试的函数进行了拆分，以函数名进行命名，里面用来存放测试用例。  
`mocha.opts`是用来写`mocha`命令行的参数，我只写了一个 `--recursive` 表示执行当前文件夹下所有的测试，即文件名为`xx.test.js`的文件。  
`utils.test.js`则是用来写测试的脚本。

# 思路

先来看一下`utils.test.js`

```
const should = require('should'); // 引入should
const Utils = require('../dist/pocket-tool').Utils; // 引入自己写的函数

const utilsCase = require('./utils'); // 引入测试用例

// 这里做了一个队结果为boolean类型判断的封装，
// target为值，expect为期望的结果
function booleanFn(target, expect) {
	if (expect) {
		target.should.be.true(); // should的语法
	} else {
		target.should.be.false();
	}
}

// 这里是对运行测试用例做了一个封装
// type 是函数的名称，itemCb，是将每个用例返回到it中
function runTestFn(type, itemCb) {
	let testItemObj = utilsCase[type];
	testItemObj.map(item => {
		itemCb(item);
	});
}


// it的第一个参数为名称，第二个参数是回调函数，测试的执行
describe('undefined判断', () => {
	runTestFn('isUndefined', item => {
		it(`${item.name}${item.expect ? '为' : '不为'}undefined`, () => {
			booleanFn(Utils.isUndefined(item.value), item.expect);
		});
	});
});

describe('null判断', () => {
	runTestFn('isNull', item => {
		it(`${item.name}${item.expect ? '为' : '不为'}null`, () => {
			booleanFn(Utils.isNull(item.value), item.expect);
		});
	});
});

...

describe('合并对象', () => {
	runTestFn('merge', item => {
		it(item.name, () => {
			let mergeResult = Utils.merge(...item.value);
			mergeResult.should.be.eql(item.expect);
		});
	});
});
```

一开始我在写用例的时候发现很麻烦，如果有多个用例的话，那岂不是要写好多个`it`，就要重复多次，于是就有了上面提到的`utils`文件夹，里面用来存放测试用例的，现在就以`utils/isNull.js`和`utils/merge.js`为例

```
// utils/isNull.js

module.exports = [
	{
		value: null,
		name: 'null',
		expect: true
	},
	{
		value: undefined,
		name: 'Undefined',
		expect: false
	},
	{
		value: 1,
		name: 'Number',
		expect: false
	},
	...
	,
	{
		value: NaN,
		name: 'NaN',
		expect: false
	}
];
```
该文件是用来存放判断是不是`Null`类型的用例，文件名称对应的就是函数名，其实就是一个数组，里面有多个对象，每个对象就是一个测试用例，其中`value`就是传入函数的值，`name`就是`it`的第一个参数，`expect`就是最终的期望结果。可以和`utils.test.js`对比来看

```
describe('null判断', () => {
	runTestFn('isNull', item => {
		it(`${item.name}${item.expect ? '为' : '不为'}null`, () => {
			booleanFn(Utils.isNull(item.value), item.expect);
		});
	});
});
```
参数是一一对应的，这样子我只要专注写用例就好，而且又易维护。

# 调用方式

在`package.json`中增加一段脚本
```
"scripts": {
	"build": "rollup --config rollup.config.js",
	"test": "npm run build && mocha"
},
```
直接`npm run test`就能看到结果了。

# 结语

我可能才用到了一点点的功能，也许不是最佳的方式，但是对于一次的使用来说，添加用例的方式我还是用的比较舒服的，对于单元测试的各种实践，或者其中有说的不对的地方欢迎指出，可直接在`github`中进行交流。