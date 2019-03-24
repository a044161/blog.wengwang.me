---
title: 浅入React。 再次
date: 2015-12-11 02:10:20
tags:
- React
categories: 
- Front-end
---

# 开篇闲扯
距离上一篇的React已经过去快10天啦，文章的更新速度还是稍慢了一点，一直不知道该按一个什么样的顺序写下来，其实更多的作用只是为了能够学着总结一些知识点，能够有一次好的沉淀机会。好了，闲话不多说。项目在今天也正是上线测试了，这是我在入公司以来接手的第二个项目，虽然之前也有一些类似维护的工作，但是项目才是一个真正补缺补漏，学习新知识的最好方式。先感谢W哥能够给我这次练手React的机会，还有H哥在项目期间对我的各种帮助包括webpack的搭建、思路上的帮助、样式的修复，当然还有两位合作的同学，可爱的交互妹子QY同学、全栈大牛BJ同学，以及我的好组员XX同学、XQ同学，谢谢你们对我这位新人的帮助~希望没有在项目中给你们添麻烦啦~哦哦哦，好像又说多了。。。本来就是为了感谢这些同学，然后顺便写一下这篇分享博文~  
这次要从以下几点进行分享：  

<!-- more -->

* 找到真实的自己ref  
* 我要给你生猴子map  
* style也能这样  
* 合体mixins  
* 风情万种的classSet  

# 找到真实的自己ref
React操作的DOM都为虚拟DOM，肯定避免不了需要操作真实的DOM，这是就要依靠ref啦  
#### ReactDOM.findDOMNode()
在[官方实例](http://facebook.github.io/react/docs/more-about-refs.html)中使用的是  
```
var Component = React.createClass({
    handleClick: function(){
        this.myTextInput.focus();
    },
    render: function(){
        return(
            <div>
                <input type="text" ref={(ref) =>   this.myTextInput = ref} />
                <input
                     type="button"
                     value="Focus the text input"
                     onClick={this.handleClick}
                />
            </div>
        )
    }
})
```
其中用到箭头函数`=>`这个为==ES6==的函数，由于我也还未正式开始学习==ES6==所以就不展开，但是，React配上==ES6==逼格瞬间就上来了，因此接下来也会加紧学习一下==ES6==。所以我在项目中使用的是官方并不是很推荐的用法`ReactDOM.findDOMNode()`
```
var Component = React.createClass({
    handleClick: function(){
        var node = React.findDOMNode(this.refs.realInputDom);
        console.log(node.value);
    },
    render: function(){
        return(
            <div>
                <input type="text" ref="realInputDom"/>
                <button type="button" onClick={this.handleClick}>Click Me</button>
            </div>
        )
    }
})
```
在==HTML==标签中加入`ref`属性，这里有一个点要注意一下，`ref`的值需要是唯一的，也就是说，如果你定义了多个相同的`ref`只会取到最后一个`ref`的值，和`id`有些类似。通过这个方法获取到DOM为真实的DOM，也就是说通过这个操作，会产生一些更大的开销，但是有的时候又需要通过该方法操作DOM，比如一些简单的操作，不需要添加额外的`state`，来改变节点的`innerText`值等。当然能够通过虚拟DOM的操作，还是使用虚拟DOM的操作比较好。接下来会结合map，对需要动态生成的==HTML==标签来添加`ref`。
# 我要给你生猴子map
在遍历`[]`或者`{}`时通过使用`map`能够很轻松的将想要的数据遍历出来，再进行渲染。
```
var Component = React.create({
    getInitialState: function(){
        return {
            data:[1,2,3,4,5]
        }
    },
    render: function(){
        var list = this.state.data.map(function(item, key){
             return(
                 <li key={key}>{item}</li>
             )
        });
        return(
           <ul>{list}</ul>
        )
    }
})
```
先定义一个变量`list`再对你需要遍历的数据使用`map`，对象为一个函数，其中第一个参数为数据的每个子项，第二个参数`key`是react建议传入的参数，用来标识每一个子项，不传入也是可以的，只是会有烦人的`warning`，最后在`render`中使用`{list}`，对的，就跟使用正常的变量一样。有兴趣的同学可以`console.log(list)`会发现里面是有每个`<li>`。
接下来结合`map`来为每个子项添加`ref`
```
var Component = React.create({
    getInitialState: function(){
        return {
            data:[1,2,3,4,5]
        }
    },
    render: function(){
        var list = this.state.data.map(function(item, key){
             return(
                 <li key={key} ref={'li' + key}>{item}</li>
             )
        });
        return(
           <ul>{list}</ul>
        )
    }
})
```
其实很简单的一个思路，加上一个特定的字符串比如我这里添加的`li`然后再加上`key`，就能够形成唯一的`ref`而且还能够轻松的找到顺序，其实`key`就是每个`li`的`index`，在做操作的时候
```
ReactDOM.findDOMNode(this.refs['li'+1])
```
更多的可以结合数据使用，比如数组的index其实是和`key`一一对应的，这样就很容易找到想要的DOM
```
var data = [1,2,3,4,5];

for(var i in data.length){
    ReactDOM.findDOMNode(this.refs['li'+i])
}
```
`ref`个人觉得非必要的情况下还是不要使用比较好，但是在这次的项目中还是有一些地方用到了，可能是因为前期对React的不熟悉，有些地方更是用了Jquery和js来获取DOM节点。。。。之后要好好修改一下代码。大家别像我这样哈~
# style也能这样 
对于React中`style`的定义方式也是让我觉得比较爽得地方，尤其是某些标签在特定条件下的样式变化，又不需要通过改变`className`。
比如，判断用户是否为管理员时显示或隐藏某个标签
```
var Component = React.createClass({
    getInitialState: function(){
        return{
            isAdmin: false
        }
    },
    render: function(){
        return(
            <div style={{'display': this.state.isAdmin?'block' : 'none'}}>who are you</div>
        )
    }
})
```
通过简单的三元运算符`?:`来操作一些简单的样式变化，获取可以传入值，根据state的变化，渲染不同的界面，小小的用处还是很不错，不用获取DOM，然后再修改`className`或通过DOM修改`style`。
# 合体mixins
想复用一些函数，或者state。可以通过使用mixins将一些常用的函数，或者state结合到任何一个组件中，先定义一个mixins
```
var Tools = {
    getInitialState: function(){
        return{
            data: [1,2,3,4,5]
        }
    },
    createID: function(){
        var date = new Date();
        return date;
    }
}
```
再调用mixins
```
var Component = React.create({
    mixins:[Tools],
    handleClick: function(){
        var _id = this.createID();
        console.log(_id);
        console.log(this.state.data);
    },
    render: function(){
        return(
            <button type="button" onClick={this.handleClick}>Click Me</button>
        )
    }
})
```
定义一个mixins和定义一个普通的对象是一样的，`getInitialState`、`componentDidMount`这些方法也是同样适用的，我比较常用除了定义一些工具函数外，还用来管理ajax。对了，每个mixins的作用域都只在该组件中，是不会跨组件的哟，也就是说，你在某个组件中应用了mixins，然后修改了其中的一个state，在另外一个组件中引用了相同的mixins，这时的state就是默认值了哟。
#风情万种的classSet 
个人觉得用来操作复杂的`className`的神器啊~记得在使用前引入==react-addons==
```
var ReactAddons = require('react-addons');

var Component = React.createClass({
    getInitialState: function(){
        return{
            data:[1,2,3,4,5]
        }
    },
    render: function(){
        var cx = ReactAddons.classSet;
        var list = this.state.data.map(function(item, key){
            var isFive = (function(){ if(item == 5) {return true} else{ return false }})();
            var className = cx({
                    'five': isFive
                });
            return(
                <li className={className}>{item}</li>
            )
            
        });
      return(
          <ul>{list}</ul>
      )
    }
}) 
```
用法其实很简单，可以先给`ReactAddons.classSet`赋变量名，之后传入`className`比如这里的`'five'`，同时我定义了一个方法来判断何时使用该`className`，也就是当`=5`时，`isFive`返回`true`，当传入的`className`为`true`时，最后才会被添加到标签中。用来管理比较复杂的`className`是个不二选择。我在项目中主要用来管理日历上日期的不同标识。
# 结语
感觉这篇写得没有[浅入React。 一次](wengwang.me/qian-ru-react-ci-2/)那篇有感觉。因为研究的还不够深入，所有不知道该分享些什么比较有价值的内容，于是就写了一些在这次项目中用的比较有感觉的方法，接下来会有两个番外篇，一个为日历、一个为滑动块，会尽快将这两个组件抽象出来，达到一个较高的复用，同时也会放上github，还有好多高级技法都没使用到，一个是项目开始的比较突然，对React没有一个深入的了解，会在接下来的代码重构中试着学习一些高级技巧，来完善代码。   
最后还是要感谢先感谢W哥、H哥、QY同学、BJ同学，XX同学、XQ同学在这次项目中对我的帮助。