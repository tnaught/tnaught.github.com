---
layout: post
title: "web视图以及尺度相关属性"
description: ""
category: "knowledge"
tags: [css]
group: post
---

### Screen视图属性
尺度相关属性有：

*	availWidth/availHeight,显示器可用区域的大小，不包括任务栏
*	width/height,显示器屏幕的大小，包括任务栏，相当于屏幕分辨率的大小
*	colorDepth
*	pixelDepth

### Window视图窗口
浏览器窗口的可视区域，浏览器resize以及控制台都会影响这个视图的大小。尺度相关属性有

*	innerWidth/innerHeight,窗体的内部宽度，包括滚动条的大小，不包括用户界面元素，比如菜单栏,
*	outerWidth/outerHeight,整个浏览器窗口的大小，包括菜单栏和状态栏等。可能还有几个像素的边框
*	pageXOffset/pageYOffset,页面滚动的大小
*	screenX/screenY,窗口在显示器中的位置

### 文档视图
相关API有：

*	document.elementFromPoint(x,y),返回给定坐标所在的元素，(x,y)相对于文档视图(可视区域)左上角
*	element.getBoundingClientRect(),返回元素的margin edge矩形相对于文档视图(可视区域)左上角的坐标{left,top,right,bottom}
*	element.getClentRects(),获取所有的line box模型的坐标
*	element.scrollIntoView()

### 元素视图属性

*	clientLeft/clientTop,content edge相对于border edge的偏移量
*	clientWidth/clientHeight，padding border的大小，不包括边框和滚动条
*	offsetLeft/offsetTop，相对于最近的祖先定位元素的偏移量
*	offsetParent，最近的祖先定位元素，
*	offsetWidth/offsetHeight，整个元素的尺寸，包括边框
*	scrollLeft/scrollTop,元素滚动的值，前提是元素的overflow为scroll，可读可写
*	scrollWidth/scrollHeight，表示整个内容区域加上padding的宽高，包括隐藏的部分

### 鼠标位置
		
*	clientX/clientY,鼠标相对于window窗口左上角（可视区域，不包括滚动区域）的偏移
*	offsetX/offsetY,相对于当前被点击元素padding box左上角的偏移量(body的background会覆盖margin区域)
*	pageX/pageY,鼠标相对于document的坐标,包括滚动区域
*	screenX/screenY，相对显示器屏幕的偏移
*	x/y相当于clientX/clientY


### 总结
本文没有详细讨论这些属性的兼容性，只是希望在需要计算坐标的时候有一个大概的概念，很多属性和方法容易混淆，需要进一步理解，同时要结合实际项目进行记忆。

<hr>
<div class="post-reference">
	<h4 class="">参考资料</h4>
	<a class="reference-item" href="http://www.zhangxinxu.com/wordpress/2011/09/cssom%E8%A7%86%E5%9B%BE%E6%A8%A1%E5%BC%8Fcssom-view-module%E7%9B%B8%E5%85%B3%E6%95%B4%E7%90%86%E4%B8%8E%E4%BB%8B%E7%BB%8D/">1.http://www.zhangxinxu.com/wordpress/2011/09/cssom%E8%A7%86%E5%9B%BE%E6%A8%A1%E5%BC%8Fcssom-view-module%E7%9B%B8%E5%85%B3%E6%95%B4%E7%90%86%E4%B8%8E%E4%BB%8B%E7%BB%8D/</a>
</div>
<hr>