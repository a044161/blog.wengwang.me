---
title: 浅入React。组件篇-日历
date: 2016-01-22 01:55:03
tags:
- React
categories: 
- Font-end
---

# 开篇闲扯
感觉拖延症各种泛滥，好久没更新了。。。而且也在上一篇的结尾提到要写一次日历组件的分享，恩，今天就写一下~一开始想把之前写的日历代码重构一下，后来发现。。还是重新写一个日历吧~哈哈哈。因为之前写的都是为了满足当时的需求，各种东拼西凑，然后今天就打算重新写一下，功能没有之前那么多，现在是先把简单的日历实现，之后会预留一些配置选项，其中包括每天的状态对应不同的样式、日期的选择操作（单日、多日）、星期的排列方式（一到日、日到六）等一些我能够想到的小功能。这次主要提一下我的整体实现思路。其实我的最终目的是想要创建一个属于自己的react组件库，希望能够从这日历开始，不断完善这个组件库。

<!-- more -->

# 环境
gulp+webpack  
关于这两个的配置我也用的不是很好，其中使用gulp起webpack-dev-server，还想趁这次用一下es6最后还是考虑之后的事情，并没有使用。具体的配置可以看[github](https://github.com/a044161/react-component)，gulp和webpack的配置文件也写的很渣~还请大家轻喷~哈哈~没有去完善这一部分，后续也会捣腾一下gulp和webpack~`gulp server`就能起服务了，然后访问`127.0.0.1:7777`
# 目录结构
（实在不懂怎么在markdown上话目录结构树，简单的用文字说明一下吧）
包含两个文件夹  
`static`用来存放静态页，也就是页面重构的目录，先从这边重构页面，再迁移到react  
`react`用来存放编写react的文件，也就是多了`jsx`，其中`jsx`就是用来存放react代码文件，又将安装不同的组件分了不同的文件夹，现在只有一个`calendar`  
在写该组件时，我又将其分为了`_storeCalendar.jsx`用来管理日历的全局state，`Calendar.jsx`想要让它成为日历的数据组件，但是还没想到好的方式把ui部分剥离出来，`CalendarBody.jsx`就是日历的主体部分，其中不涉及任何数据的操作，只有使用props的数据，生成相应的view
# 整体实现思路
我最早是先重构日历，找了个还比较喜欢的日历ui，然后重构。  
然后开始想需要哪些数据作为全局的state，基于之前做的日历，我定义了这几个全局的state：`toDay:{yyyy: '', mm: '', dd: '', ymd: '', md: ''}`用来存放当前的设置的日期、`monthEn: ['January ', 'February', 'March', 'April', 'May', 'June', 'July', 'August', 'September', 'Octorber', 'November', 'December']`这个就自己决定啦，对应不同月份的英文而已、`weeksArray:[]`用来存放当前月份下每周的具体数据（这个比较重要）  
定义了几个方法:`getNowDate`获取当前时间、`createMonthArray`生成当前年份月份下的具体日期，就是`weeksArray`的来源、`handlePreMonth,handleNextMonth`这个就是前一个月和后一个月的事件函数了。  
重点说一下`createMonthArray`。一开始一直在想要怎么生成日历，日历的日期和星期该如何对应。之后想到了数组，也就是一周对应一个数组，让月成为一个二维数组，将周push到月。每周有7天，对应数组里面的七个项。顺着这个思路下去就可以将日历的日期和星期的对应关系理清了，也就是一行为一周、一周为一个数组。下面是具体的代码实现，采用了`moment.js`来管理日期。
```javascript
		var weekArray = []; // 存放一周的天数
		var weeksArray = []; // 存放一整个月的天数，一个子数组为一周

		var _moment = new moment(); // new moment 才能保证设置年、月等属性时是有效的
		var toDay = this.state.toDay;

		var _year = year? year : toDay.yyyy;
		var _month = month? month : toDay.mm;

		_moment.set({'year': _year, 'month': _month}); // 设置年、月

		var days = _moment.daysInMonth(); // 获取当前月份下的天数

		var dayInfo = {}; // 初始一个每天的详情对象

		for(var d=1; d<days+1; d++){ // 根据当前月份的天数生成，日期对应星期的数组

			_moment.set('date', d);  // 设置日期

			var day = _moment.day(); // 获取星期

			dayInfo.yyyy = _year; // 年
			dayInfo.mm = _month; // 月
			dayInfo.dd = d; // 日
			dayInfo.day = day; // 星期
			dayInfo.ymd = _moment.format('YYYY-MM-DD'); // 年-月-日

			if(day == 6){ // 判断是否为星期六 为周六则需要结束该子数组
				weekArray[day] = dayInfo;
				weeksArray.push(weekArray.concat());
				weekArray = [];
			}else if(d==days){ // 判断是否为最后一天 最后一天也需要结束该子数组
				weekArray[day] = dayInfo;
				weeksArray.push(weekArray.concat());
			}else{
				weekArray[day] = dayInfo;
			}

			dayInfo = {}

		}

		for(var w in weeksArray){ // 将为空的部分填充上空
			for(var d=0; d<7; d++){
				if(typeof(weeksArray[w][d]) == 'undefined'){
					weeksArray[w][d] = '';
				}
			}
		}

		this.setState({weeksArray: weeksArray})
```  
当`weeksArray`生成之后就将其传入`CalendarBody`，在`CalendarBody`分为两步走，第一是先渲染一周里的每天、然后再渲染每一周，具体代码如下
```javascript
renderDay: function(day, key){ // 渲染每一天
		var toDay = this.props.calendarState.toDay;

		var cx = ReactAddons.classSet;

		var isToday = function(){
			if(toDay.ymd == day.ymd){
				return true;
			}else{
				return false;
			}
		}

		var dayClass = cx({
			'day-item': true,
			'now': isToday()
		})

		return(
			<li className={dayClass} key={key} data-yyyy={day.yyyy} data-mm={day.mm} data-dd={day.dd} data-day={day.day} data-ymd={day.ymd}>{day.dd}</li>
		)
	},
	renderWeek: function(week, key){ // 渲染每一周
		var day = week.map(this.renderDay)
		return(
			<ul key={key} className="day-list">
				{day}
			</ul>
		)
	},
	render: function(){
		var weeksArray = this.props.calendarState.weeksArray;
		var week = weeksArray.map(this.renderWeek)
		return(
			<div>
				{week}
			</div>
		)
	}
```  
至此，一个简单的日历就搞定啦~
# 结语
希望不要再犯拖延症，早日能够完善组件库，还有好多地方没有剥离出来，包括文件的结构，会不断的完善，最后是[github](https://github.com/a044161/react-component)地址。欢迎随时交流~最后一句~祝我顺利完成毕业设计吧~~