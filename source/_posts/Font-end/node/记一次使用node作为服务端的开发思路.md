---
title: 记一次使用node作为服务端的开发思路
date: 2016-08-28 17:35:12
tags:
- NodeJs
categories: 
- Font-end
---

# 开篇闲扯
距离上一次的技术博文已经好久了，之前一直写的是关于`react`的（虽然才写了三篇），这次就写一写最近在项目中用到`node`来做服务端的开发思路，用`node`做服务端的机会不多，毕设用过一次，之后就没有用过了。先打个预防针，这次的开发思路完全是我自己瞎掰扯的，主要目的和之前写`react`一样，想要做一次小结，以及想要换取更多的交流，来弥补自身的不足。一直都觉得，写博文比写代码要难得多，不仅要回顾代码，还要理清思路，还要将这些思路清晰的表达出来，确实是一件不简单的事，自己写代码容易，但是要教会别人，或者告诉别人要难很多。  
好了，这次主要从以下几个方面来展开说一下：

* 目录结构  
* 数据库规划  
* 路由规划  
* 日志   
* 环境变量    
* 简单的token  
* 简单的插件编写  
* 目录结构  
```json
├─ README.md
├─ server.js // 入口文件
│  
├─app // 主文件夹
│  │  
│  ├─libs // 各种插件
│  │      
│  ├─mongodb // mongoose相关文件
│  │  │  
│  │  ├─documents
│  │  │      
│  │  └─schemas
│  │          
│  ├─route // 路由
│  │      
│  ├─static // 静态资源
│  │  │  
│  │  ├─fonts
│  │  │      
│  │  ├─images
│  │  │      
│  │  ├─libs
│  │  │      
│  │  ├─scripts
│  │  │      
│  │  └─styles
│  │          
│  └─views // 模板文件
│          
├─config // 环境变量的配置文件
│      
└─log // 日志文件
        
```
目录结构相对来说还是比较简单的，也是通过不断的调整和更改命名方式来定义目录结构，能够涵盖我当前项目的一些需求，可能还有更多关于服务端需要调用的功能和配置我这里没有列举出来，本身也是因为上次毕业设计结束后，时隔这么久再次使用了一次，个人觉得相对毕业设计的目录结构，这次的这个方案会更加直白和分离一些。欢迎各位同学能够一起分享一下自己的目录结构，能够一起探讨一下。
# 数据库规划
```json
├─mongodb // mongoose相关文件  
    ├─documents      
    └─schemas
```
数据库选用的是`mongodb`调用的第三方库为[mongoose](http://www.nodeclass.com/api/mongoose.html)，根据`mongoose`的官方定义，一共分为三个部分`schemas > models > documents`因此在目录的规划上将`schemas models`合在了一次，之前是有将他们分为三个不同的目录来存放，一定意义上能够做到分离，但是也让调用的过程稍微繁琐了一点（这次并大型应用，因此对于数据库的操作相对来说并不是非常复杂，`schemas models`中的一些中间件也较少用到），按照这样的规划，因此`schemas`中的写法大致是这样
```javascript
`use stricts`

const mongoose = require('mongoose');

const AdminScheams = new mongoose.Schema({
    name: String,
    password: String
})

const Admin = mongoose.Model('Admin', AdminSchemas);

module.exports = Admin;
```
`documents`中是为了对数据库进行增删查改的操作，根据实际的项目需求，对其进行相关的一些封装，这里可能要用到`Promise`，根据`mongoose`官方的建议是使用`mpormise`，在`documents`中调用之前定义好的`models`进行操作
```javascript
`use strict`

const mPromise = require('mpromise');

/* Models */
const Admin = require('../schemas/Admin');

exports.Get = (id) => {
    const promise = new mPromise();
    
    Admin
    .findById(id)
    .exec((err, admin) => {
        if(err){
            promise.resolve(err, {flag: false, data: {}, info: err});        
        }else{
            promise.resolve(err, {flag: true, data: admin, info:''}); 
        }
        
    });

    return promise;
}
```
通过引入`models`，`exports`一个函数，之后可以对其进行扩展，最简单的就是增删查改的需求，使用`promise`让最后返回的是一个`promise`对象，可以调用`xx.then()`根据不同的业务需求，可以先将一些常规的操作进行封装，这样可以保证在路由部分的调用能够是一个最简单的数据返回的效果。
```javascript
// route.js
'use strict'
const express = require('express');
const router = express.Router();


/* Documents */
const Admin = require('../mongodb/documents/admin');

/* Router */
router.get('/', (req, res) =>{
    const id = req.query.id;

    Admin
    .Get(id)
    .then( data => {
        if(data.flag){
            res.json({flag: true, data: data.data, info:""})
        }else{
            res.json({flag: false, data: {}, info: "无法查到相对应的管理员"})
        }
    })
})

module.exports = router;
```
在路由里面的调用方式就如上所示，还算是比较简便的一种方式，当然还有更好的方式，希望能够和大家沟通后，不断的进行改进。
# 路由规划
路由的规划还是蛮重要的，划分的足够精细，才能让后期能够有一个较好的扩展性。
```json
├─route // 路由
   ├─api.js
   ├─api
      ├─admin.js
```
我的划分习惯是逐级划分，如果api下面还有其它级的路由就建一个文件来存放其它的子路由，以此类推，如果api的子路由下面还有其它的子路由，就继续创建文件夹来存放其它的子路由。  
先来创建`app.js`下的子路由`admin.js`
```javascript
'use strict'

const express = require('express');
const router = express.Router();

router.get('/list', (req, res) => {
    res.json({flag: true})
});

module.exports = router
```
之后我们再来写`api.js`
```javascript
'use strict'

const express = require('express');
const router = express.Router();
const bodyParser = require('body-parser');

/* Use */
router.use(bodyParser.json());
router.use(bodyParser.urlencoded({
    extended: true
}));

/* Router */
const admin = require('api/admin');

/* Router Use */
router.use('/admin', admin);

module.exports = router;
```
看编写的方式应该就能够比较容易理解，`api.js`里面有子路由，可以通过`router.use`进行调用，也是这样逐级进行调用，最后入口文件`server.js`的话可以这样来使用
```javascript
'use strict'

const express = require('express');
const app = express();

/* Router */
const api = require('route/api');

app.use('/api', api)
```
路由划分的思路大致就是这样，应该还是比较好理解的，希望大家能够告诉我有更好的方式，或者我这样的方式有什么不妥的地方，希望也能够得到大家的指正。
# 日志
关于日志这一部分，我这次使用的是`log4js`，刚好看见[寸志](http://weibo.com/chromeappsstore?refer_flag=1001030101_&is_all=1)在[前端外刊](https://zhuanlan.zhihu.com/FrontendMagazine#!)发表了一篇[Node.js 之 log4js 完全讲解](https://zhuanlan.zhihu.com/p/22110802?refer=FrontendMagazine)的文章，就刚好开了一下进行使用了，具体的使用方法大家可以直接通过链接进行查看，使用的方式还是蛮容易上手的，我为了能够方便管理，我在前面的`libs`里面添加了一个`logger.js`方便进行维护，代码大致如下
```javascript
'use strict'

/* Logger */
const log4js = require('log4js');
log4js.configure({
	appenders: [
		{
			type: 'DateFile',
			filename: './log/api/access/api_access.log', // 输出的路径
			pattern: '-yyyy-MM-dd.log', // 命名方式
			alwaysIncludePattern: true,
			category: 'api_access'
		},
		
	],
	levels: {
		'api_access': log4js.levels.INFO
	}
})


module.exports = {
	apiAccess: log4js.connectLogger(log4js.getLogger('api_access'))
}
```
之后在上面创建的`api.js`中调用的方式就是
```javascript
'use strict'

const express = require('express');
const router = express.Router();
const bodyParser = require('body-parser');

/* Libs */
const logger = require('../libs/logger'); // 引入

/* Logger */
router.use(logger.apiAccess); // 调用

/* Use */
router.use(bodyParser.json());
router.use(bodyParser.urlencoded({
    extended: true
}));

/* Router */
const admin = require('api/admin');

/* Router Use */
router.use('/admin', admin);

module.exports = router;
```
感觉是一种比较简单粗暴的方式，不同的日志类型，也都可以在刚刚的`logger.js`中进行配置管理，在任意地方进行调用
# 环境变量
这个其实想要说的就是改变`NODE_ENV`的值，在不同的开发环境下可能调用的参数不同，例如参见的开发模式`development`和`production`两种，有可能在这些不同的环境下，服务调用的接口不同，数据库地址不同等等一系列的配置文件，这个可以通过使用[config](https://www.npmjs.com/package/config)这个第三方插件来使用，因此在目录结构那节里面有一个`config`的文件，下面就是用来存放config的配置文件，更多高级的使用方式可以查看相对应的[文档](https://www.npmjs.com/package/config)，我在`config`下创建了两个文件，分别是`development.json`和`production.json`，当然如果还有更多的环境可以创建多个配置文件，两个文件的写法相同，只是里面可能某些的参数不同，我就拿其中一个举例吧
```json
{
	"mongodb": {
		"path": "mongodb://127.0.0.1/test",
		"name": "test"
	},
	"server" : {
		"port": 3131
	}
}
```
这都是根据自己的需求进行定义，内容也就没什么好解释的了，其中只要注意一下要严格按照`json`的写法，要不然会导致无法解析。接下来我们再来看看该如何调用
```javascript
'use strict'

const config = require('config');

const mongodbConf = config.get('mongodb');
```
调用方式也是非常简单，先引入`config`，再通过`get`来获取你想要的字段，之后就和访问对象一样了。
# 简单的token
对应`token`的验证机制，大家可以自行去查找一下，资料还是非常多的，这里我简单的介绍一下相对应的用法是什么样的，`token`我使用的是[jwt-simple](https://www.npmjs.com/package/jwt-simple)，和名字一样，使用的方式也是比较简单的，下面还是用相对应的代码来展示一下
```javascript
'use strict'
const jwt = require('jwt-simple');
const moment = require('moment');

/* MongoDB Models */
const Admin = require('../mongodb/schemas/admin');

/* Set */
const jwtSecret =  'abcd1234' // token 秘钥

exports.SetToken = (value) => {
	const expires = moment().add(7, 'days').valueOf();
	const token = jwt.encode({
		iss: value, // 加密对象
		exp: expires // 时间戳
	}, jwtSecret) // 密钥

	return token;
}

exports.GetToken = (req, res, next) => {
	const reqToken = req.headers["token"];

	if(typeof reqToken !== 'undefined'){
		try{
			const decodedToken = jwt.decode(reqToken, jwtSecret);
			if(decodedToken.exp <= Date.now()){
				res.status(401).json({flag: false, data:{}, info:'token过期');
			}else{
				Admin
				.findOne({name: decodedToken.iss})
				.exec((err, admin) => {
					if(!admin){
	res.status(403).json({flag: false, data:{}, info:'无操作权限');
					}else{
						next()
					}
				})
			}
			
		}catch(e){
			res.status(403).json({flag: false, data:{}, info:'无操作权限');
		}
	}else{
		res.status(403).json({flag: false, data:{}, info:'无操作权限');
	}
}
```
先引入`jwt-simple`，再定义一个秘钥`jwtSecret`，我还使用了多定义了两个方法以便后面进行调用，一个是`SetToken`设置`token`还有一个就是`GetToken`获取`token`，其实相对应的就是`encode`和`decode`，首先是想对想要进行加密的部分进行`encode`当然`{iss: value, exp: expires}`这个是可以自定义的。之后再根据客户端发送的`token`进行`decode`，来获取相对应的信息，在上面的代码中我是先对时间进行验证，再根据之前加密的`name`进行数据库查询，如果有相对应的值，则允许进入下一个路由。  
我将这个验证方式写到了`libs`，命名为`token.js`，在需要进行验证的地方可以进行调用
```javascript
'use strict'

const express = require('express');
const router = express.Router();

/* Libs */
const token = require('../../libs/token');

router.get('/list', token, (req, res) => { // 中间件的方式使用token
    res.json({flag: true})
});

module.exports = router
```
在需要进行`token`验证的路由下加入就好。
# 简单的插件编写
在不断的开发过程中，会抽象出很多简单的小插件，主要目的就是为了方便后期重复代码的编写，以及解决一些数据上的处理，就像上面我抽象了两个出来，分别是日志的`logger.js`和`token`验证的`token.js`，其实后来发现，应该将`token.js`纳入`middleware`这个文件下。  
其实我还多加一个`response.js`用来处理各个路由的`response`
```javascript
'use strict';

exports.successRes = (res, data, info, status) =>{
	const _status = status ? status : 200;
	const _info = info ? info : '';

	const resData = {
		flag: true,
		data: data,
		info: _info,
		status: _status
	}

	res.status(_status).json(resData)
};

exports.badRes = (res, info, status, data) => {
	const _status = status ? status : 200;
	const _info = info ? info : '';
	const _data = data ? data : {};

	const resData = {
		flag: false,
		data: _data,
		info: _info,
		status: _status
	}

	res.status(_status).json(resData)
};
```
一个为成功时的操作，一个为失败时的操作，这样做的好处是能够统一返回的格式，而且也较好进行统一的管理。其实很多都是开发过程中不断的进行抽象，不断的累积出适合实际业务的一套方案。  
在最后就把入口文件`server.js`展示一下
```javascript
const fs = require('fs')
const path = require('path')
const express = require('express');
const app = express();
const mongoose = require('mongoose');
const config = require('config');

/* Config */
const mongodbConfig = config.get('mongodb');
const serverConfig = config.get('server');

process.on('uncaughtException', (err) => {
    console.log(err);
});

/* Set */
app.set('views', './app/views/'); // 页面路径
app.set('view engine', 'pug'); // 模板引擎

/* 静态资源 */
app.use(express.static(path.join(__dirname, './app/static'))); //定义静态资源目录

/* connect mongodb */
mongoose.connect(mongodbConfig.path);
const db = mongoose.createConnection('localhost', mongodbConfig.name);
db.on('error', console.error.bind(console, 'connection error:'));

/* Use */


/* Tmp */
const cors = require('cors'); // 解决跨域
app.use(cors())


/* Router */
const api = require('./app/route/api');

/* Router use */
app.use('/api', api); // api

app.listen(serverConfig.port);

```
# 结语
发现距离写上一篇博文已经间隔了4个月，这一段时间内也发生了不少的事情，恩，在这篇博文里还是不适宜提起这些事情，还是以技术为主吧。  
最主要的目的还是对这一次的使用过程做一次总结，想要和更多的人进行探讨，有任何问题或者任何想要交流的地方都可以和我联系，邮箱:wengwangjay@126.com，微博:@爱拍照的小胖纸。