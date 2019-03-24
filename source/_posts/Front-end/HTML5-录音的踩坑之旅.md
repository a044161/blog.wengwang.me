---
title: HTML5 录音的踩坑之旅
date: 2017-12-17 19:37:32
photos:
- https://oss.wengwang.me/images/wengwang_me/hexo/Front-end/%E5%BD%95%E9%9F%B3-%E5%8D%A1%E9%80%9A.jpg
tags:
- HTML5
categories:
- Front-end
---

# 开篇闲扯

前一段时间的一个案子是开发一个有声课件，大致就是通过导入文档、图片等资源后，页面变为类似 PPT 的布局，然后选中一张图片，可以插入音频，有单页编辑和全局编辑两种模式。其中音频的导入方式有两种，一种是从资源库中导入，还有一种就是要提到的录音。  
说实话，一开始都没接触过 HTML5 的 Audio API，而且要基于在我们接手前的代码中进行优化。当然其中也踩了不少坑，这次也会围绕这几个坑来说说感受（会省略一些基本对象的初始化和获取，因为这些内容不是这次的重点，有兴趣的同学可以自行查找 MDN 上的文档）：

* 调用 Audio API 的兼容性写法
* 获取录音声音的大小（应该是频率）
* 暂停录音的兼容性写法
* 获取当前录音时间

<!-- more -->

# 录音前的准备

开始录音前，要先获取当前设备是否支持 Audio API。早期的方法 `navigator.getUserMedia` 已经被 `navigator.mediaDevices.getUserMedia` 所代替。正常来说现在大部分的现代浏览器都已经支持`navigator.mediaDevices.getUserMedia` 的用法了，当然`MDN`上也给出了兼容性的写法

```javascript
const promisifiedOldGUM = function(constraints) {
	// First get ahold of getUserMedia, if present
	const getUserMedia =
		navigator.getUserMedia ||
		navigator.webkitGetUserMedia ||
		navigator.mozGetUserMedia;

	// Some browsers just don't implement it - return a rejected promise with an error
	// to keep a consistent interface
	if (!getUserMedia) {
		return Promise.reject(
			new Error('getUserMedia is not implemented in this browser')
		);
	}

	// Otherwise, wrap the call to the old navigator.getUserMedia with a Promise
	return new Promise(function(resolve, reject) {
		getUserMedia.call(navigator, constraints, resolve, reject);
	});
};

// Older browsers might not implement mediaDevices at all, so we set an empty object first
if (navigator.mediaDevices === undefined) {
	navigator.mediaDevices = {};
}

// Some browsers partially implement mediaDevices. We can't just assign an object
// with getUserMedia as it would overwrite existing properties.
// Here, we will just add the getUserMedia property if it's missing.
if (navigator.mediaDevices.getUserMedia === undefined) {
	navigator.mediaDevices.getUserMedia = promisifiedOldGUM;
}
```

因为这个方法是异步的，所以我们可以对无法兼容的设备进行友好的提示

```javascript
navigator.mediaDevices.getUserMedia(constraints).then(
	function(mediaStream) {
		// 成功
	},
	function(error) {
		// 失败
		const { name } = error;
		let errorMessage;
		switch (name) {
			// 用户拒绝
			case 'NotAllowedError':
			case 'PermissionDeniedError':
				errorMessage = '用户已禁止网页调用录音设备';
				break;
			// 没接入录音设备
			case 'NotFoundError':
			case 'DevicesNotFoundError':
				errorMessage = '录音设备未找到';
				break;
			// 其它错误
			case 'NotSupportedError':
				errorMessage = '不支持录音功能';
				break;
			default:
				errorMessage = '录音调用错误';
				window.console.log(error);
		}
		return errorMessage;
	}
);
```

一切顺利的话，我们就可以进入下一步了。  
（这里有对获取上下文的方法进行了省略，因为这不是这次的重点）

# 开始录音、暂停录音

这里有个比较特别的点，就是需要添加一个中间变量来标识是否当前是否在录音。因为在火狐浏览器上，我们发现一个问题，录音的流程都是正常的，但是点击暂停时却发现怎么也暂停不了，我们当时是使用 `disconnect` 方法。这种方式是不行的，这种方法是需要断开所有的连接才可以。后来发现，应该增加一个中间变量 `this.isRecording` 来判断当前是否正在录音，当点击开始时，将其设置为`true`，暂停时将其设置为`false`。  
当我们开始录音时，会有一个录音监听的事件 `onaudioprocess` ，如果返回 `true` 则会将流写入，如果返回 `false` 则不会将其写入。因此判断`this.isRecording`，如果为 `false` 则直接 `return`

```javascript
// 一些初始化
const audioContext = new AudioContext();
const sourceNode = audioContext.createMediaStreamSource(mediaStream);
const scriptNode = audioContext.createScriptProcessor(
	BUFFER_SIZE,
	INPUT_CHANNELS_NUM,
	OUPUT_CHANNELS_NUM
);
sourceNode.connect(this.scriptNode);
scriptNode.connect(this.audioContext.destination);
// 监听录音的过程
scriptNode.onaudioprocess = event => {
	if (!this.isRecording) return; // 判断是否正则录音
	this.buffers.push(event.inputBuffer.getChannelData(0)); // 获取当前频道的数据，并写入数组
};
```

当然这里也会有个坑，就是无法再使用，自带获取当前录音时长的方法了，因为实际上并不是真正的暂停，而是没有将流写入罢了。于是我们还需要获取一下当前录音的时长，需要通过一个公式进行获取

```
const getDuration = () => {
    return (4096 * this.buffers.length) / this.audioContext.sampleRate // 4096为一个流的长度，sampleRate 为采样率
}
```

这样就能够获取正确的录音时长了。

# 结束录音

结束录音的方式，我采用的是先暂停，之后需要试听或者其它的操作先执行，然后再将存储流的数组长度置为 0。

# 获取频率

```javascript
getVoiceSize = analyser => {
	const dataArray = new Uint8Array(analyser.frequencyBinCount);
	analyser.getByteFrequencyData(dataArray);
	const data = dataArray.slice(100, 1000);
	const sum = data.reduce((a, b) => a + b);
	return sum;
};
```

具体可以参考[https://developer.mozilla.org/zh-CN/docs/Web/API/AnalyserNode/frequencyBinCount](https://developer.mozilla.org/zh-CN/docs/Web/API/AnalyserNode/frequencyBinCount)

# 其它

* HTTPS：在 chrome 下需要全站有 `HTTPS` 才允许使用
* 微信：在微信内置的浏览器需要调用 JSSDK 才能使用
* 音频格式转换：音频格式的方式也有很多了，能查到的大部分资料，大家基本上是互相 copy，当然还有一个音频质量的问题，这里就不赘述了。

# 结语

这次遇到的大部分问题都是兼容性的问题，因此在上面踩了不少坑，尤其是移动端的问题，一开始还有出现因为获取录音时长写法错误的问题，导致直接卡死的情况。这次的经历也弥补了 HTML5 API 上的一些空白，当然最重要的还是要提醒一下大家，这种原生的 API 文档还是直接查看 MDN 来的简单粗暴！
