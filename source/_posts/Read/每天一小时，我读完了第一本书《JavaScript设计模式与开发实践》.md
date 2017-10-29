---
title: 每天一小时，我读完了第一本书《JavaScript设计模式与开发实践》
date: 2017-10-29 15:49:42
photos:
- https://oss.wengwang.me/images/wengwang_me/hexo/Read/%E3%80%8AJavaScript%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F%E4%B8%8E%E5%BC%80%E5%8F%91%E5%AE%9E%E8%B7%B5%E3%80%8B.jpg
tags:
- 每天一小时
categories:
- Read
---

[![](https://badge.juejin.im/entry/59f58a636fb9a0450808ea46/likes.svg?style=flat-square)](https://juejin.im/entry/59f58a636fb9a0450808ea46/detail)

# 开篇闲扯
从国庆结束后，我就给自己定下来新的作息时间。每天6:30起床，7:00开始阅读，8:00吃早饭，9:00出门走路上班，21:00点下班回家，22:30睡觉。我也将这些加入了手机的提醒项，每天到时时间就提醒我。  
虽然不是100%按照这个时间进行，但是都有保证每天一小时的阅读，以及最迟不超过23:30的入睡时间。直到今天（10月29日）阅读完第一本书，我已经坚持了20天。  
<!-- more -->
这是我选择的第一本书《JavaScript设计模式与开发实践》，内容不错的第一本书，喜欢的同学可以去我做的笔记上面进行简单的查阅，当然想要阅读更多内容的同学，一定要支持作者，购买正版书籍，是非常值得入手的一本书。  

在线笔记：[http://book.wengwang.me/read_1/](http://book.wengwang.me/read_1/)  
购买地址：[京东](https://item.jd.com/11686375.html) [亚马逊](https://www.amazon.cn/JavaScript%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F%E4%B8%8E%E5%BC%80%E5%8F%91%E5%AE%9E%E8%B7%B5-%E6%9B%BE%E6%8E%A2/dp/B00XJ2AU3S/ref=sr_1_1?ie=UTF8&qid=1509261304&sr=8-1&keywords=javascript%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F%E4%B8%8E%E5%BC%80%E5%8F%91%E5%AE%9E%E8%B7%B5)

# 概要

### 作者介绍
曾探。2007年毕业于吉林大学软件学院，目前就职于腾讯Alloy Team前端团队，高级工程师。

### 内容介绍
一共三大部分：

- 讲解JavaScript面向对象和函数式编程方面的知识。
- 讲解16个设计模式
- 面向对象设计原则在设计模式的体现，还介绍了常见的编程技巧和代码重构。

其实我是比较建议有开发经验的同学阅读，可以从中获得很多灵感，或者技巧，会在项目的开发中进行思考，当然我也是比较赞成作者的观点，不要堆砌设计模式，而是要合理利用设计模式，设计模式也许是为了能够写出更好的代码，提高系统的稳定性。但是也不能滥用，无形中也会增加时间成本以及阅读代码的难度，还有代码的复杂度。

# 浓缩一下
本书一共介绍了16个设计模式，看到某些设计模式的时候，你会“哦，原来这个也是设计模式”，确实，写代码的过程会加入自己的一些思考，写出一些代码，也许你懂得有这样的写作技巧，但是缺少了系统的学习，也有不少人建议我要多看书，书中的知识才比较系统。也是因为这个原因，我才开始这这个每天一小时的计划。  
接下来的代码实例基本上来自书中。

## 单例模式
保证一个类仅有一个实例，并提供一个访问它的全局访问点

这里展示一个惰性单例，其实这个是经常使用的，用一个标记来表示是否创建过某个对象，是则返回。

```javascript
// 创建通用的惰性单例
var getSingleton = function(fn){
    var result;
    
    return function(){
        return result || (result = fn.apply(this, arguments));
    }
}

var createLoginLayer = function(){
    var div;
    
    return function(){
        if(!div){
            div = document.createElement('div');
            div.innerText = '这是弹窗';
            div.style.display = 'none';
            document.body.appendChild(div);
        }
        
        return div;
    }
});

var singletonCreateLoginLayer = getSingleton(createLoginLayer);

document.getElementById('loginBtn').onclick = function(){
    var layer = singletonCreateLoginLayer();
    layer.style.display = 'block';
}
```

## 策略模式
定义一系列的算法，把它们一个个的封装起来，并且使它们可以相互替换。目的就是算法的使用和算法的实现分离出来。  
由两部分组成：
- 策略类：封装了具体的算法，并负责具体的计算过程
- 环境类：接受客户的请求，随后把请求委托给某一个策略类

```javascript
var Strategy = {
    S: function(salary){
        return salary * 4;
    },
    A: function(salary){
        return salary * 3;
    },
    B: function(salary){
        return salary * 2;
    }
};

var getBouns = function(strategy, salary){
    return Strategy[strategy](salary);
}

getBouns('B', 1000); // 2000
```

## 代理模式
为一个对象提供一个代用品或占位符，以便控制对它的访问。当客户不方便直接访问一个对象或者不满足需要的时候提供一个替身对象来控制对这个对象的访问，客户实际上访问的是替身对象。

## 迭代器模式
是指提供一种方法顺序访问一个聚合对象中的各个元素，而又不需要暴露该对象的内部表示。

```javascript
var each = function(arr, callback){
    for(var i=0, l=arr.length, i < l, i++){
        callback(i, arr[i]);
    }
};

each(['a','b','c'], function(i, val){
    console.log(val);
});

// a, b, c
```

## 发布-订阅模式
又称为观察者模式，它定义对象间的一种一对多的依赖关系，当一个对象的状态发生
改变的时，所有依赖于它的对象都将得到通知。一为时间上解耦，二为对象之间的解
耦。

```javascript
var Event = (function(){ 
    var clientList = [],
        listen,
        trigger,
        remove;
    listen = function(key, fn){ // 订阅 
        if(!clientList[key]){
            clientList[key] = {};
        }
        clientList[key].push(fn);
    };
    trigger = function(){ // 发布
        var key = Array.prototype.shift.call(arguments),
        fns = clientList[key];
        if(!fns || fns.length === 0){
            return false;
        }
        for(var i=0; fn; fn = fns[i++]){
            fn.apply(this, arguments);
        }
    };
    remove = function(key, fn){
        var fns = clientList[key];
        if(!fns || fns.length === 0){
            return false;
        }
        if(!fn){
            fns && (fns.lengthss = 0); // 如果没有传 具的回调函数，表示需要取消key对应消息的所有订阅 
        }else{
            for(var l = fns.length - 1; l >=0; l--){ // 反向遍历 
            var _fn = fns[l];
  
            if(_fn === fn){
                fns.splice(l, 1); // 删除订阅者的回调函数
            } 
    };
    return {
        listen: listen,
        trigger: trigger,
        remove: remove
    }
})();
Event.listen('event1');
Event.trigger('event1');
Event.remove('event1');
```

## 命令模式
一个执行某些特定事情的指令。最常见的场景是：有时候需要向某些对象发送请求，但是并不知道请求的接收者是谁，也不知道被请求的操作是什么。命令模式在JavaScript语言中是一种隐形的模式。

```javascript
// 创建行为
var MenuBar = {
    refresh: function(){
        console.log('刷新');
    }
}

// 创建命令
var RefreshMenuBarCommand = function(receiver){
    return {
        execute: function(){
            receiver.refresh();
        }
    }
};

// 设置命令
var setCommand = function(button, command){
    button.onclick = function(){
        command.execute();
    }
};

var refreshMenuBarCommand = RefreshMenuBarCommand(MenuBar);

setCommand(button1, refreshMenuBarCommand);
```

## 组合模式
组合模式是将对象组合成树形结构，以表示“部分-整体”的层次结构。  
在大多数情况下，我们都可以忽略掉组合对象和单个对象之间的差别，从而用一致的方式来处理它们。
- 表示树形结构：提供了一种遍历树形结构的方案，通过调用组合对象的execute方法，程序会递归调用组合对象下面的叶对象的execute方法。组合模式可以非常方便地描述对象部分-整体层次结构。
- 利用对象多态性统一对待组合对象和单个对象

```javascript
var MacroCommand = function(){
    return {
        commandList: [],
        add: function(command){
            this.commandsList.push(command);
        },
        execute: function(){
            for(var i=0, command; command = this.commandList[i++];){
                command.execute();
            }
        }
    }
};

var openAcCommand = {
    execute: function(){
        ...
    }
};

// Tv、Sound 是相连的
var openTvCommand = {
    execute: function(){
        ...
    }
};
var openSoundCommand = {
    execute: function(){
        ...
    }
};

var macroCommand1 = MacroCommand();
macroCommand1.add(openTvCommand);
macroCommand1.add(openSoundCommand);

// closeDoor、openPC、QQ 是相连的
var closeDoorCommand = {
    execute: function(){
        ...
    }
};

var openPCCommand = {
    execute: function(){
        ...
    }
};

var openQQCommand = {
    execute: function(){
        ...
    }
};

var macroCommand2 = MacroCommand();
macroCommand2.add(closeDoorCommand);
macroCommand2.add(openPCCommand);
macroCommand1.add(openQQCommand);

var macroCommand = MacroCommand();
macroCommand.add(openAcCommand);
macroCommand.add(macroCommand1);
macroCommand.add(macroCommand2);

macroCommand.execute();
```

## 模板方法模式
模板方法模式是一种只需使用继承就可以实现的非常简单的模式。  
模板方法模式由两部分构成：

- 抽象父类：封装了子类的算法框架，包括实现一些公共方法以及封装子类中所有方法的执行顺序。
- 具体实现的子类：通过基础抽象父类，也继承了整个算法结构，可以选择重写父类的方法。

假如一些平行的子类，各个子类直接有一些相同的行为，也有一些不同的行为。相同的行为可能会重复出现在各个子类之间，那这些相同的行为可以被搬到另一个单一的地方，模板方法模式就是为了解决这个问题而生的。

```javascript
var Beverage = function(param){
    var boilWater = function(){...};
    
    var brew = param.brew || function(){...};
    
    var pourInCup = param.pourInCup || function(){...};
    
    var F = function(){};
    
    F.prototype.init = function(){
        boilWater();
        brew();
        pourInCup();
    }
    
    return F;
};

// 创建子类

var Coffee = Beverage({
    brew: function(){...},// 重写
    pourInCup: function(){...}
});

var coffee = new Coffee();
coffee.init();
```

## 享元模式
享元（flyweight）模式是一种用于性能优化的模式，“fly”在这里是苍蝇的意思，意为蝇量级。享元模式的核心是运用共享技术来有效支持大量细粒度的对象。  
享元模式是为解决性能而生的模式，在一个存在大量相似对象的系统中，享元模式可以很好地解决大量对象带来的性能问题。

```javascript
// uploadType作为内部状态，再抽离外部状态
var Upload = function(uploadType){
    this.uploadType = uploadType;
};

// 定义删除文件的方法
Upload.prototype.delFile = function(id){
    uploadManager.setExternalState(id, this); // 设置外部状态
    
    if(this.fileSize < 3000){
        return this.dom.parentNode.removeChild(this.dom);
    }
    
    if(window.confirm('确定要删除文件吗？'+ file.fileName)){
        return this.dom.parentNode.removeChild(this.dom);
    }
};

// 工厂进行对象实例化
var UploadFactory = (function(){
    var createFlyWeightObjs = {};
    return {
        create: function(uploadType){
            if(createFlyWeightObjs[uploadType]){
                return createFlyWeightObjs[uploadType];
            }
            
            return createFlyWeightObjs[uploadType] = new Upload(uploadType);
        }
    }
})();

// 管理器封装外部状态
var uploadManager = (function(){
    var uploadDataBase = {}; // 存储外部状态
    
    return {
        add: function(id, uploadType, fileName, fileSize){
            var flyWeightObj = UploadFactory.create(uploadType);
            
            var dom = document.createElement('div');
            dom.innerHTML = '...';
            document.body.appendChild(dom);
            
            uploadDataBase[id] = { // 添加外部状态
                fileName: fileName,
                fileSize: fileSize,
                dom: dom
            };
            
            return flyWeightObj;
        },
        setExternalState: function(id, flyWeightObj){ // 设置外部状态
            var uploadData = uploadDataBase[id];
            for(var i in uploadData){
                flyWeightObj[i] = uploadData[i];
            }
        }
    }
})();

var id = 0;

window.startUpload = function(uploadType, files){
    for(var i = 0, file; file = files[i++];){
        var uploadObj = uploadManager.add(++id, uploadType, file.fileName, file.fileSize);
    }
};

startUpload('plugin', [
    {
        fileName: '1.txt',
        fileSize: 1000
    },
    ...
]);
```

## 职责链模式
使多个对象都有机会处理请求，从而避免请求的发送者和接受者之间的耦合关系，将这些对象连成一条链，并沿着这条链传递该请求，直到有一个对象处理它为止，这些对象称为链中的节点。

```javascript
var order500 = function(orderType, pay, stock){
    if(orderType === 1 && pay){
        console.log('500元定金，得到100优惠券');
    }else{
        return 'nextSuccessor';
    }
};

var order200 = function(orderType, pay, stock){
    if(orderType === 2 && pay){
        console.log('200元定金，得到50优惠券');
    }else{
        return 'nextSuccessor';
    }
};

var orderNormal= function(orderType, pay, stock){
    if(stock > 0){
        console.log('普通购买，无优惠券');
    }else{
        console.log('手机库存不足');
    }
};

// 需要把函数包装进职责链节点，定义一个构造函数`Chain`，在`new Chain`的时候传递的参数即为需要被包装的函数，同时还拥有一个示例属性`this.successor`，表示在链中的下一个节点

var Chain = function(fn){
    this.fn = fn;
    this.successor = null;
};

Chain.prototype.setNextSuccessor = function(successor){ // 指定在链中的下一个节点
    return this.successor = successor;
};

Chain.prototype.passRequest = function(){ // 传递请求给某个节点
    var ret = this.fn.apply(this, arguments);
    
    if(ret === 'nextSuccessor'){
        return this.successor && this.successor.passRequest.apply(this.successor, arguments);
    }
    
    return ret;
};

// 封装成职责链节点
var chainOrder500 = new Chain(order500);
var chainOrder200 = new Chain(order200);
var chainOrderNormal = new Chain(orderNormal);

// 设置节点在职责链中的顺序
chainOrder500.setNextSuccessor(chainOrder200);
chainOrder200.setNextSuccessor(chainOrderNormal);

// 传递给第一个节点
chainOrder500.passRequest(1, true, 500);
chainOrder500.passRequest(2, true, 500);
chainOrder500.passRequest(3, true, 0);
```

## 中介者模式
解除对象与对象之间的紧耦合关系。增加一个中介者对象后，所有的相关对象都通过中介者对象来通信，而不是互相引用，所有当一个对象发生改变时，只需要通知中介者对象即可。中介者使各对象之间耦合松散，而且可以独立地改变它们之间的交互。中介者模式使网状的多对多关系变成了相对简单的一对多关系。

## 装饰者模式
给对象动态地增加职责的方式称为装饰者模式。  
装饰者模式能够在不改变对象自身的基础上，在程序运行期间给对象动态地添加职责。跟继承相比，装饰者是一种更轻便灵活的做法，这是一种“即用即付”的方式。

```javascript
var plan = {
    fire: function(){
        console.log('普通子弹');
    }
};

var missilDecorator = function(){
    console.log('导弹');
};

var atomDecorator = function(){
    console.log('原子弹');  
};

var fire1 = plane.fire;

plan.fire = function(){
    fire1();
    missilDecorator();
};

var fire2 = plan.fire;

plan.fire = function(){
    fire2();
    atomDecorator();
};

plan.fire();
```

## 状态模式
状态模式的关键是区分事物内部的状态，事物内部状态的改变往往会带来事物的行为改变。  

首先定义了`Ligth`类，`Light`类在这里也被称为上下文(`Context`)。随后在`Light`的构造函数中，我们要创建每一个状态类的实例对象，`Context`将持有这些状态对象的引用，以便把请求委托给状态对象。
```javascript
// Light 类
var Light = function(){
    this.offLightState = new OffLightState(this);
    this.weekLightState = new WeekLightState(this);
    this.strongLightState = new StrongLightState(this);
    this.button = null;
};

Light.prototype.init = function(){
    var button = document.createElement('button');
    var self = this;
    
    this.button = document.body.appendChild(button);
    this.button.innerHTML = '开关';
    
    this.currState = this.offLightState;
    
    this.button.onclick = function(){
        self.currState.buttonWasPressed();
    };
};
```
最后再编写各种状态类，`ligth`对象被传入状态类的构造函数，状态对象也需要持有`ligth`对象的引用，以便调用`ligth`中的方法或者直接操作`light`对象
```javascript
// offLightState
var OffLightState = function(light){
    this.light = light;
};

OffLightState.prototype.buttonWasPressed = function(){
    console.log('弱光') // offLightState 对应的行为
    this.light.setState(this.light.weekLightState); // 切换状态到 weekLightState
};
```

## 适配器模式
将原接口转换为客户希望的另一个接口，客户只需要和适配器打交道

```javascript
var googleMap = {
    show: function(){
        console.log('开始渲染地图');
    }
};

var baiduMap = {
    display: function(){
        console.log('开始渲染地图');
    }
};

var baiduAdapter = { // 增加 baiduAdapter 来解决问题
    show: function(){
        return baiduMap.display();
    }
}

var renderMap = function(map){
    if(map.show instanceof Function){
        map.show();
    }
};

renderMap(googleMap);
renderMap(baiduAdapter);
```

# 结语
可能只会也会不断的翻阅这些记录或者再次阅读这本书，会在开发过程中引入更多的思考，最后还要再声明一下，大部分内容并非原创，而是对书中内容的整理，有需要的小伙伴可以有两种方式：  

在线笔记：[http://book.wengwang.me/read_1/](http://book.wengwang.me/read_1/)  
购买地址：[京东](https://item.jd.com/11686375.html) [亚马逊](https://www.amazon.cn/JavaScript%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F%E4%B8%8E%E5%BC%80%E5%8F%91%E5%AE%9E%E8%B7%B5-%E6%9B%BE%E6%8E%A2/dp/B00XJ2AU3S/ref=sr_1_1?ie=UTF8&qid=1509261304&sr=8-1&keywords=javascript%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F%E4%B8%8E%E5%BC%80%E5%8F%91%E5%AE%9E%E8%B7%B5)

强烈建议大家购买原版书籍，值得入手。  
希望自己能够继续坚持下次，每天一小时。