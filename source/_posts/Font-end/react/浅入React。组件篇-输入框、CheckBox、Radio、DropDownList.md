---
title: 浅入React。组件篇-输入框、CheckBox、Radio、DropDownList
date: 2016-05-07 00:14:45
tags:
- React
categories: 
- Font-end
---

# 开篇闲扯
好久没有发过关于React的文章了，也好久没有动过React了，趁着这次毕设的机会再来使用我最爱的React，话说看过一些大牛对react的用法、看法，以及一些其它开发者写的react代码，觉得自己的水平真的是相差甚远，还是要一步步熟悉一下。这次是用到了一点点的ES6的写法，也是第一次接触ES6，各种不熟悉以及各种不习惯。这次主要是想要分享一下关于输入框、CheckBox、Radio、DropDownList的组件编写。其实每个的原理都是相同的，都只负责ui的简单交互，其它的数据通过props传递。不知道能不能算的上是组件，分享的目的就是为了不断的进步，不断的交流，不断的探索。btw，wengwangjay@126.com、@爱拍照的小胖纸,分别是我的邮箱和微博账号，欢迎随时的交流。最后再啰嗦一句，请忽略丑爆的ui。哈哈哈。  

<!-- more -->

# 简单说说大体构建思路

* 先对所有组件进行重构  
重构时也是根据组件类型来进行区分，也为方便后期移入react，将所有的css都合并到一个`commom.css`。也构建了最开始的一个引导页，使用sass来编写样式，方便模块化的导入。
* 再移入react进行编写 
 
为了方便，通过yo生成了一套脚手架，基本能够满足需求。然后将各个组件命名为单个的文件夹，这是为了方便后期的扩展（目前只是满足了基本功能、数据传递），后面还会引入对样式的控制、一些验证机制等。最后使用一个Main.js来当做入口文件，引入这些组件。组件内只关系到不同的状态变化，所有的数据都由父组件进行传递。整体的思路还是比较简单的。
# 输入框
因为输入框这次没有加入验证的选项所以整体的思路也是比较简单的。
先看一下`input`的dom结构
```html
<div className="info-item ui-input">
	<label className="ui-label">
        <span className="label-sign">*</span>
	    <span className="label-text">{options.label}</span>
    </label>
	<input className="input-body" placeholder={options.placeholder} value={this.state.defaultValue} onChange={this.props.onChange}/>
</div>
```
我多加了一个`label`原本后面的几个组件也都有加上该标签，后来还是先删除掉了，想要之后能够进行自定义的配置，让`label`变为一个可选的配置。对于`input`的`placeholder value onChange`都是由用户传入的信息进行配置的。  
现在来看一下父组件  
```javascript
class AppComponent extends React.Component{
    constructor(props){
        super(props);
        this.state={
            inputOption:{
                input_text:{
                    label:'这是一个输入框',
                    placeholder: '请输入...',
                    value: ''
                }
            }
        }
    }
    handleChangeInputText(e){
        let _value = e.target.value;
        let _inputOption = this.state.inputOption;
        
        _inputOption.input_text.value = _value;
        this.setState({ inputOption: _inputOption });
    }
 
    render(){
        return(
            <div>
                <InputText options={this.state.inputOption.input_text} onChange={this.handleChangeInputText.bind(this)}>
            </div>
        )
    }
}
```
从代码可以看出这也是一段比较简单的引用，先定义一个`state`来存放想要配置的`InputText`这里我定义了一个`inputOption`，里面的每个key对应一个`input`，当然定义方式都是随意的，只要其中包含`label placeholder value`就好。随后又定义了在`input`输入时触发的`onChange`事件，这里大概就是当用户发送输入时，将该值传入对应的`state`，由父组件的`state`来触发更新。  
最后将`InputText`组件的代码贴出来
```javascript
class InputTextComponent extends React.Component{
    constructor(props){
        super(props);
    }

    render(){
        let options = this.props.options;
        return(
            <div className="info-item ui-input">
	            <label className="ui-label">
                    <span className="label-sign">*</span>
	                <span className="label-text">{options.label}</span>
                </label>
	            <input className="input-body" placeholder={options.placeholder} value={this.state.defaultValue} onChange={this.props.onChange}/>
            </div>
        )
    }
}

InputTextComponent.defaultProps = {
    onChange(){};
    options:{
        placeholder: '',
        value: '',
        label: ''
    }
}
```
整体思路也是比较简单的，因为还没将其它个性化的配置添加进去，只需要注意使用的是`value`就好，最后也定义了默认的props。是一个比较简单的组件实现思路，当初也是为了简单的为了满足一下毕业设计时里面的使用进行编写的。陆续也会添加上其它的一些配置选项。
# CheckBox
CheckBox因为涉及到一些样式的变化还有默认值的问题，所以会比单纯的`input`的复杂一点，但是整体思路也是很简单的。先看下dom结构吧
```html
<div class="ui-checkbox on">
	<input class="input-hidden" type="checkbox"></input>
	<span class="checkbox-body"></span>
	<span class="checkbox-text">这是多选框</span>
</div>
```
将原生的`input`隐藏，控制其样式，让它点击文字的时候也能够触发。接来下看看父组件的配置方式
```javascript
class AppComponent extends React.Component{
    constructor(props){
        super(props);
        this.state={
            checkboxOption:[
                {
                    text:'多选框-1',
                    value: 'checkbox-1',
                    checked: true
                },
                {
                    text:'多选框-2',
                    value: 'checkbox-2',
                    checked: false
                },
                {
                    text:'多选框-3',
                    value: 'checkbox-3',
                    checked: false
                }
            ]
        }
    }
    handleChangeCheckBox(index){
        let _checkboxOption = this.state.checkboxOption;

		if(_checkboxOption[index].checked){
			_checkboxOption[index].checked = false;
		}else{
			_checkboxOption[index].checked = true;
		}

		this.setState({checkboxOption: _checkboxOption})
    }
 
    render(){
        return(
            <div>
               <CheckBox options={this.state.checkboxOption} onChange={this.handleChangeCheckBox.bind(this)}/>
            </div>
        )
    }
}
```
通过`checkboxOption`来定义你想要使用的checkbox，一个元素对应一个checkbox。其中`text`对应的是多选框旁边的文字；`value`对应的是该checkbox对应的值；`checked`若为`true`则被选中，若为`false`则不被选中。再定义一个发生变化时的方法`handleChangeCheckBox`参数为点击的checkbox的索引值，通过其改变`state`中对应的checkbox对象。传入参数的方式和`input`组件是类似的。最后来看看组件的源码
```javascript
class CheckBoxComponent extends React.Component{
	componentDidMount() {
		const _options = this.props.options;
	    for(let i=0; i<_options.length; i++){
	    	if(_options[i].checked){
	    		ReactDOM.findDOMNode(this.refs['checkbox-'+ i]).className = 'ui-checkbox on';
	    	}else{
	    		ReactDOM.findDOMNode(this.refs['checkbox-'+ i]).className = 'ui-checkbox';
	    	}
	    }
	}
	handleChecked(index){
		const _this = ReactDOM.findDOMNode(this.refs['checkbox-'+index]);
		if(_this.className == 'ui-checkbox'){
			_this.className = 'ui-checkbox on';
		}else{
			_this.className = 'ui-checkbox';
		}
	}
	render(){
		return(
			<div className="del-item">
				<label className="ui-label">
					<span className="label-sign">*</span>
					<span className="label-text">{this.props.label}</span>
				</label>
				{this.props.options.map((item,index) => {
					return (
						<div key={index} ref={'checkbox-'+index} className="ui-checkbox" onClick={this.handleChecked.bind(this,index)}>
							<input className="input-hidden" type="checkbox" onChange={this.props.onChange.bind(this,index)} defaultChecked={item.checked}/>
							<span className="checkbox-body"></span>
							<span className="checkbox-text">{item.text}</span>
						</div>
					)
				})}
			</div>
		)
	}
}
```
先通过`componentDidMount`来初始化那些被选中或没被选中的checkbox，是在`map`时通过`ref`加上`index`来进行标识，再使用`ReactDOM.findDomNode`进行样式的操作。然后定义一个当点击时进行样式切换的方法`handleChecked`将该事件注册在组件最外层的`div`的`onClick`事件上，改变样式的方式和初始化类似，这里就不做过多的解释了。而之前父组件定义的`handleChangeCheckBox`则注册在`input`的`onChange`事件上，但点击事件时，改变`state`。主要还是通过`map`的`index`来进行配合使用，后续也会去参考一下其它开发人员写的方式。
# Radio
radio其实和checkbox差不多，唯一的区别就是，radio有组的概念，只能有一个被选中，还是先看看radio的dom结构吧
```html
<div class="ui-radio on" data-radio="group">
	<input class="input-hidden" type="radio" name="group" checked></input>
	<span class="radio-body"></span>
	<span class="radio-text">这是单选框</span>
</div>
```
结构其实和checkbox一样，对input进行隐藏，多了`name`。接下来看看父组件的配置方式
```javascript
class AppComponent extends React.Component{
    constructor(props){
        super(props);
        this.state={
            radioOption:[
                {
                    text:'单选框-1',
                    value: 'radio-1',
                    name: 'radio-name',
                    checked: true
                },
                {
                    text:'单选框-2',
                    value: 'radio-2',
                    name: 'radio-name',
                    checked: false
                },
            ]
        }
    }
    handleChangeRadio(index){
        const _radioOption = this.state.radioOption;

		for(let r=0; r < _radioOption.length; r++){
			_radioOption[r].checked = false
		}

		_radioOption[index].checked = true;
		this.setState({radioOption: _radioOption})
    }
 
    render(){
        return(
            <div>
               <Radio options={this.state.radioOption} onChange={this.handleChangeRadio.bind(this)}/>
            </div>
        )
    }
}
```
通过`radioOption`来定义你想要使用的radio，一个元素对应一个radio。其中`text`对应的是单选框旁边的文字；`value`对应的是该radio对应的值；`name`为radio的组，对应input中的name；`checked`若为`true`则被选中，若为`false`则不被选中。再定义一个发生变化时的方法`handleChangeRadio`参数为点击的radio的索引值，先将所有的`checked`置为`false`，然后再把选中的那个radio的`checked`置为`true`，传入参数的方式都是类似的。最后来看看组件的源码
```javascript
class RadioComponent extends React.Component{
	componentDidMount() {
	    const _options = this.props.options;
	    for(let i=0; i<_options.length; i++){
	    	if(_options[i].checked){
	    		ReactDOM.findDOMNode(this.refs['radio-'+ i]).className = 'ui-radio on';
	    	}else{
	    		ReactDOM.findDOMNode(this.refs['radio-'+ i]).className = 'ui-radio';
	    	}
	    }
	}
	componentWillReceiveProps(nextProps) {
	    const _options = nextProps.options;
	    for(let i=0; i<_options.length; i++){
	    	if(_options[i].checked){
	    		ReactDOM.findDOMNode(this.refs['radio-'+ i]).className = 'ui-radio on';
	    	}else{
	    		ReactDOM.findDOMNode(this.refs['radio-'+ i]).className = 'ui-radio';
	    	}
	    }
	}
	handleChecked(index){
		for(let i in this.refs){
			ReactDOM.findDOMNode(this.refs[i]).className = 'ui-radio';
		}

		const _this = ReactDOM.findDOMNode(this.refs['radio-'+ index]);
		_this.className = 'ui-radio on';
	}

	render(){
		return(
			<div  className="option-item">
				{this.props.options.map((item, index) => {
					return (
						<div key={index} ref={'radio-'+ index} className="ui-radio" data-radio={item.name} onClick={this.handleChecked.bind(this,index)}>
							<input className="input-hidden" type="radio" name={item.name} value={item.value} defaultChecked={item.checked} onChange={this.props.onChange.bind(this,index)}/>
							<span className="radio-body"></span>
							<span className="radio-text">{item.text}</span>
						</div>
					)
				})}
			</div>
		)
	}
}

RadioComponent.defaultProps = {
	onChange(){},
}
```
思路和checkbox是一样的，同样也是初始化，然后定义改变样式的方法，改变样式的思路和checkbox一致，也和父组件定义的方法一样，只不过把对应的元素换成了dom节点。
# DropDownList
模拟一个ddl，这里还算是一种比较简单的方式，没有过多复杂的交互，下拉框也没出现空隙的状况。还是一样的，先看看dom结构
```html
<div class="ui-dropdown open">
		<span class="dropdown-text">
			请选择....
			<span class="icon"></span>
		</span>
		<ul class="dropdown-body">
			<li class="dropdown-item">1111111</li>
			<li class="dropdown-item">2222222</li>
			<li class="dropdown-item">3333333</li>
		</ul>
	</div>
```
很简单的一种结构，上面为显示框，下面为列表。再看看父组件是如何进行配置的
```javascript
class AppComponent extends React.Component{
    constructor(props){
        super(props);
        this.state={
            dropdownOption:[
                {
                    value: '下拉列表-1',
                    id: '1111'
                },
                {
                    value: '下拉列表-2',
                    id: '2222'
                },
                {
                    value: '下拉列表-3',
                    id: '3333'
                },
                {
                    value: '下拉列表-4',
                    id: '4'
                }
            ]
        }
    }
    handleChangeDropDownList(index){ // 下拉列表选项变化时触发

	}
 
    render(){
        return(
            <div>
               <DropDownList defaultValue="请选择一个..." options={this.state.dropdownOption} onSelect={this.handleChangeDropDownList.bind(this)}/>
            </div>
        )
    }
}
```
和之前的配置方式一样,`value`对应的是显示的那个值，`id`对应的是它本身的值。再定义一个当选中某一项时的方法，返回`index`，通过`index`判断选择的是哪一项。最后看看组件的源码
```javascript
class DropDownListComponent extends React.Component{
	constructor(props){
		super(props);
		this.state = {
			ddlDefalut: ''
		}
	}
	componentDidMount() {
	    this.setState({ddlDefalut: this.props.defaultValue})  
	}
	handleSelectd(){
		const _this = ReactDOM.findDOMNode(this.refs['dropdown']);

		if(_this.className == 'ui-dropdown'){
			_this.className = 'ui-dropdown open';
		}else{
			_this.className = 'ui-dropdown';
		}
	}
	handleChoseItem(index){
		const data = this.props.options;
		this.props.onSelect(index)
		this.setState({ddlDefalut: data[index].value})
	}
	render(){
		return(
			<div className="ui-dropdown" ref='dropdown' onClick={this.handleSelectd.bind(this)}>
                <span className="dropdown-text">
					{this.state.ddlDefalut}
					<span className="icon"></span>
                </span>
                <ul className="dropdown-body">
                	{this.props.options.map((item, index) => {
                		return(
                			<li key={index} className="dropdown-item" onClick={this.handleChoseItem.bind(this, index)}>{item.value}</li>
                		)
                	})}
                </ul>
            </div>
		)
	}
}

DropDownListComponent.defaultProps = {
	defaultValue: 'select one...',
	onSelect(){}
}
```
定义的`ddlDefault`用来初始化显示框的内容，以及后面选择后显示的内容。同样也是通过`componentDidMount`进行初始化。`handleSelectd`可以看出是用来控制下拉框的展开或者收起，这里也只是简单的交互效果。`handleChoseItem`是用来选中之后改变显示的内容，以及使用父组件定义的'onSelect'来传递数据。整体的思路也是比较简单的。
# 结语
还有好多的不足之处，很多地方还没有考虑到，也会去参考更多的组件化编写方式，后续也会对其进行不断的完善，还是由于时间比较赶，想要先将博文发出来，不想再一拖再拖，顺便对用es6写的react进行熟悉一下，虽然用到的es6的地方并不多，还是先感受一下~一样的，这些也全部都托管在[github](https://github.com/a044161/react-component)上。  
还是觉得很不满意，包括如何使用的文档也没编写，还有很多想要补充完整的地方，后面也会不断跟上，对博文也会再进行补充。有任何建议或者意见也都希望可以和我一起交流。