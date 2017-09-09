---
title: 浅入React。 一次
date: 2015-12-02 17:29:38
tags:
---

# 开篇闲扯
对的，都不知道要用什么来作为开头，就是简单的代入一下~看标题，浅入React 一次，对的，把Blog的第一次就贡献给React吧~当然还会 入React 两次、三次、等等。。。第一次正正经经的做一次分享，不好的地方请猛烈拍砖，来者便是客，欢迎留言交流，留者比粉啊~哈哈哈~这次会先说一下几点：  

* 一笔带过JSX  
* 保底的React.createClass & ReactDOM.render  
* 状态之源 getInitialState  
* 缠绵的 state & props  
* 牢记Component的生命  

或许文中有不正确，不合理的地方，随便批评，随便撕B，我接受你的批评，回应你的撕B。这样才有讨论的气氛，进步的可能~ ^.^

# 一笔带过JSX
JSX是React特有的一种写法，一开始发现React的这种写法，我是头也不回的把页面关掉了。  
对于JSX，我的了解并不深入，当然有兴趣的同学，可以查看React官方关于[JSX](http://facebook.github.io/react/docs/displaying-data.html)的文档，在这里我就简单的提一下

#### 与原HTML写法打架的属性  
```
class -> ClassName   
for -> htmlFor 
style -> style={{'color':'red'}}
```
我目前碰到的、最常出错的就是这三个。  
对于`<img>`、`<input>`一定要加上`/`，正确的姿势`<img/>`、`<input/>`

#### 传递各种值
`<div>{value}</div>`  
使用`{}`来传递值，可以传递各种各样的，包括三元运算符`<div>{value ? true : false}</div>`、方法（在组件中定义的一些方法、会用在事件绑定上）`<div onClick={this.handleClick}>click me</div>`、布尔值，undefined,null需要包裹在`{}`，例如`{true}`
#### 各种“姿势”
`<div onClick={this.handleClick}>click me</div>`  
* 点击事件 -> `onClick`  
* `<input/>`监听事件 -> `onChange`  
* 输入事件 -> `onInput`  
* 鼠标事件 -> `onMouseOver`、`onMouseUp`、`onMouseDown`  
这里就举例我常用到的几个，类型的事件其实和js基本类似，有兴趣的同学可以查看官方的[事件系统](http://facebook.github.io/react/docs/events.html)文档
# 保底的React.createClass & ReactDOM.render  
#### React.creatClass
React.createClass 是用来创建一个Component  
```
var Component = React.createClass({
    render: function(){
        return(
            <div>I'm from Component</div>
        )
    }
})
```  
其中 React.createClass 传递的参数为一个`{}`集合，同样最基本的，也是不可缺少的属性为==render==，该属性的作用为渲染==HTML==。最简单的一个Component就是这样创建出来的。最终调用的形式为`<Componet/>`
#### ReactDOM.render
自从0.14版本后，就变为使用==react-dom==渲染最后的组件，调用格式为
```
var Component = React.createClass({
    render: function(){
        return(
            <div>I'm from Component</div>
        )
    }
})

ReactDOM.render(
    <Component/>,
    document.getElementById('id');
)
```  

传递两个参数，第一个为最终要渲染到界面的Component，第二个为HTML元素；最终该Component会插入到HTML元素内。
# 状态之源 getInitialState
这是一个非常重要的方法，重中之重啊  
```javascript
var Component = React.createClass({
    getInitialState: function(){
        return{
            stateValue: 'hehe'
        }
    },
    handleChangeState: function(){
        this.setState({stateValue: 'o'})
    },
    render: function(){
        return(
            <div>{this.state.stateValue}</div>
        )
    }
})

ReactDOM.render(
    <Component propsData={false}/>,
    document.getElementById('id');
)
```
`getInitialState`定义时返回的是一个对象`{}`，里面包含你想要定义的初始状态，可以为字符串、布尔值、函数返回值等等...  
想要修改state必须唯一只能使用`setState({})`，参数为你想要修改的属性。  
React也是依据state的状态变化作为重新渲染的依据。
# 缠绵的 state & props
`state`和`props`可为一对好基友，惺惺相惜
#### 插播一条组件嵌套
忽然发现忘记提一下组件嵌套了。。。
```
var Parent = React.createClass({
    render: function(){
        return(
            <div>
                <Children fromParent={}/>
            </div>
        )
    }
})

var Children = React.creatClass({
    render: function(){
        return(
            <div>i'm from Children</div>
        )
    }
})
```
对的，想要在父组件中嵌套子组件，就和引用HTML标签一样，`<>`+组件名 = `<组件名/>`
有一点需要注意，`render`中`return`的HTML标签都必须有个最外层的标签包裹，特别需要注意的是子组件必须包裹在一个HTML标签内，下面是一个错误的例子
```
var Parent = React.createClass({
    render: function(){
        return(
           <Children fromParent={}/>
        )
    }
})

var Children = React.creatClass({
    render: function(){
        return(
            <div>Oops</div>
            <div>i'm from Children</div>
        )
    }
})
```
在`Parent`中，子组件没有包裹在一个HTML标签内。  
在`Children`中，两个`<div>`没有包裹在一个HTML标签内。
#### props
父组件想要和子组件通信就要通过props，子组件通过`this.props.xxx`获取父组件传递给子组件的值
```
var Parent = React.createClass({
    getInitialState: function(){
        return{
            data: 'parent'
        }
    },
    render: function(){
        return(
            <div>
                <Children fromParent={this.state.data}/>
            </div>
        )
    }
})

var Children = React.creatClass({
    render: function(){
        return(
            <div>{this.props.fromParent}</div>
        )
    }
})
```
父组件通过在标签内`fromParent={this.state.data}`定义想要传给子组件的属性、当然属性的类型也是各种各样，包括函数。  
子组件通过`this.props.fromParent`获取值。  
对了，props是不能修改的，props只会根据父组件传递的值改变而改变，修改props并不会影响父组件的值。
#### 以下犯上
子组件想要修改父组件的state呢？好办！  
```
var Parent = React.createClass({
    getInitialState: function(){
        return{
            data: 'parent'
        }
    },
    storeData: function(new_data){
        this.setState({data: new_data})
    },
    render: function(){
        return(
            <div>
                <Children fromParent={this.state.data} storeData={this.storeData}/>
            </div>
        )
    }
})

var Children = React.creatClass({
    handleClick: function(){
        var data = 'new data!!!';
        this.props.storeData(data);
    },
    render: function(){
        return(
            <div onClick={this.handleClick}>{this.props.fromParent}</div>
        )
    }
})
```
现在父组件定义一个方法`storeData`该方法的参数为你想要传递的数据，然后使用`this.setState()`进行修改，再将该方法传递给子组件，子组件调用的时候就能够修改父组件的state，但本质还是父组件自己修改自己的state。
# 牢记Component的命运 
#### Component的成长史
`componentWillMount` -> `componentDidMount` -> `componentWillReceiveProps` -> `shouldComponentUpdate` -> `componentWillUpdate` -> `componentDidUpdate` -> `componentWillUnmount`  
从字面的翻译上应该是很好理解的吧？我就不多BB了，要记住的一点是上面给出的顺序是第一次渲染Component时，Component的生命周期，但数据发生变化时，生命周期就发生了变化，`componentWillReceiveProps` -> `shouldComponentUpdate` -> `componentWillUpdate` -> `componentDidUpdate`  
我从我最常接触到的来讲，  `componentDidMount`、`componentWillReceiveProps`
##### componentDidMount
这是在Component挂在后的阶段，也就是DOM都已经生成，可以做一些UI上初始化的行为，例如根据返回的数据渲染到HTML，同时也是ajax比较常见的放置点，这是组件第一次渲染时最后一次修改state的机会，可以触发state变化，从而重新渲染界面。
```
var Component = React.createClass({
    getInitialState: function(){
        return(
            isUpdate: false
        )
    },
    componentDidMount: functon(){
        $.get(url,params, function(res){
            this.setState({isUpdate: res})
        })
    },
    render: function(){
        var text = this.state.isUpdate? 'Update!' : 'NO~!'
        return(
            <div>{text}</div>
        )
    }
})

ReactDOM.render(
    <Component/>,
    document.getElementById('id');
)
```
先定义一个`state`，当第一次渲染时`isUpdate`为`false`，当DOM生成后进入`componentDidMount`，于是获取到新的值，此时`state`发生变化，便触发组件的更新。自此`componentDidMount`的生命就走到了尽头，接下来的路就交给了`componentWillReceiveProps`
##### componentWillReceiveProps
顾名思义该方法在将要接收到props时触发，注意是==**将要**==，==**将要**==，==**将要**==收到==**props**==，==**props**==，==**props**==时触发。参数为下一个的props。最常用的就是在跟之前的props进行对比，如果不同触发相应的行为，如果相同触发相应的行为。或者更新下一个的props到state。根据各种需求而使用吧
```
var Component = React.createClass({
    getInitialState: function(){
        return{
            isUpdate: false
        }
    },
    componentWillReceiveProps: functon(nextprops){
        var data = nextprops.propsData;
        this.setstate({isUpdate: data})
    },
    render: function(){
        var text = this.state.isUpdate? 'Update!' : 'NO~!'
        return(
            <div>{text}</div>
        )
    }
})

ReactDOM.render(
    <Component propsData={false}/>,
    document.getElementById('id');
)
```
==nextprops==是下一个的props，是一个包含有进行传递的props对象。
关于Component的生命周期还有的几种比较少用到，尤其是Update的，曾经用的时候，`console.log()`直接崩溃了，同样还是参考官方文档，[组件的生命周期](http://facebook.github.io/react/docs/component-specs.html)
# 结语
先分享一些简单的内容，可能会有蛮多遗漏的地方，希望大家能够帮忙提出来；也希望能够对想要了解React的同学一些小小的帮组，接触React的时间并不长，手上的项目也还未完成，等项目结束，会结合实际中遇到的问题一起分享出来，不断完善 *浅入React* 。  
为什么没有在开头介绍React的[环境配置](http://facebook.github.io/react/docs/getting-started.html)呢？个人觉得，一搜一大把，就不必在这写出来了，只为分享一些最简单易懂的内容。接下来的计划也会按照原来[开篇文章](http://www.wengwang.me/2015/11/29/blogzheng-shi-kai-qi/)写得那样进行。  
最后还是希望喜欢的人能够分享，能够评论，能够交流，一起跳入React的大坑。