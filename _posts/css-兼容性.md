---
layout: post
title: "css在浏览器中的兼容性"
description: ""
category: "knowledge"
tags: [css]
group: post
---

#### a标签点击事件不响应
html结构
<div class="container">
	<img src="" alt="">
	<a href=""></a>
</div>

img是背景图片，position:relative，a标签指向图片上的某个区域，position:absolute,display:block。

在ie7中(ie6没有测试)中a标签的click事件不会响应。

解决方法:

1.	给a标签添加样式background:url(about:blank)`推荐`
2.	给a标签添加背景色或背景图片，并将透明度设置为0，即opacity:0;filter:alpha(opacity=0);

#### ie7/ie8不支持background-size
解决方法:
padding-top:177.78%;
background-image:url(images/bg.png);
-webkit-background-size: 100% auto;
background-size: 100% auto;
position:relative;
filter: progid:DXImageTransform.Microsoft.AlphaImageLoader(src='images/bg.jpg',sizingMethod='scale');
-ms-filter: "progid:DXImageTransform.Microsoft.AlphaImageLoader(src='images/bg.jgp',sizingMethod='scale')";


