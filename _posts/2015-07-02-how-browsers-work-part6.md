---
layout: post
title: "(译)浏览器是如何工作之part6——paint"
---

paint阶段，遍历render tree，执行每个renderer上的paint方法，然后将内容显示到屏幕上。paint过程使用了UI结构组件。

###整体paint和增量paint
和layout一样，paint也分为整体和增量两种，整体就是绘制整个render tree，当只有部分renderer发生变化且不影响整体的时候只需要进行增量paint。发生变化的render在屏幕上的矩形区域会失效。操作系统视这个区域为dirty区域，并且触发一个绘制事件。操作系统是很聪明的，它会将多个区域合并为一个。在Chrome中这个过程会更加复杂，因为renderer和主进程不在同一个进程。一定程度上Chrome模拟了操作系统的行为。表现层监听了这些事件，然后把信息传给render root。然后遍历render tree直到找到相关的renderer，进行paint。

###paint的顺序
[CSS2的规范](http://www.w3.org/TR/CSS21/zindex.html)定义了这个顺序。通常也是元素在stacking context中的顺序。在栈中绘制从后向前进行。一个块状renderer的栈顺序如下：

1. 背景颜色(backgrount color)
2. 背景图片(background image)
3. 边框(border)
4. 后代元素(children)
5. outline

###FF的显示列表
FF会遍历render tree，为绘制矩形构建一个显示列表。它包含绘制矩形相关的renderer，按照正确的绘制顺序。（**不通顺！！**）。这样每一次重绘都只需要遍历一次renderer tree，--一次绘制所有的背景、然后图片、然后所有的边框等依次类推。

FF不会增加隐藏的元素，包括在不透明元素下面隐藏的元素，这样优化了绘制过程。

###webkit的rectangle storage
绘制后，webkit将上一次的矩形已位图的形式保存下来，重绘的时候就只需要绘制新矩形和旧矩形之间的差异部分。

###动态变化
针对每一个改动，浏览器试图去做最小程度的动作去响应。遵循这一原则，元素颜色的变化只会引起该元素的重绘，元素位置的变化会同时带来该元素、后代元素、相邻元素的layout和重绘，增加节点会引起该节点的layout和重绘。重要的变化如增加html元素的字体大小会导致缓存失效及整体的重排和重绘。

###渲染引擎的线程
渲染引擎是单线程运行的，几乎除了网络操作以外的所有操作都是在一个线程上进行。在FF和safari中这就是浏览器的主线程。在chrome中，这是每个标签的主线程。

###事件轮询
浏览器的主线程使用的是事件轮询机制。它会进行无线循环以保持处理处于活动状态。当有事件发生如layout和paint的时候，线程就进行处理。FF的主要的事件轮询代码如下：

    while(!mExiting)
        NS_ProcessNextEvent(thread);
