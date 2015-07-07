---
layout: post
title: "(译)浏览器是如何工作之part5——layout"
---

当renderer创建然后被加到render tree后，它并没有位置和大小信息。计算位置和大小的过程为'layout'或者'reflow'。   
HTML使用了一个已'layout model'为基础的'flow'，即大部分时候它都可以在一个'pass'上进行几何计算。在这个'flow '上后面的元素不会影响前面元素的几何计算，所有'layout'可以由文档从左至右、从上自下的进行。当然也有例外的情况，例如HTML的表格可能需要多个'pass'。

坐标系统以'root frame'为参照，原点为左上角，从左向右、从上自下递增。

'Layout'是一个迭代的过程。它从'root renderer '开始，对应的是'HTML document '元素。然后继续按层级遍历部分或所有的frame，为需要的renderer计算集合信息。

root renderer的坐标是0,0，它的大小是整个viewport空间，即浏览器窗口的可视区域。

所有的renderer都有一个layout或reflow方法，每个renderer会触发它的子renderer（需要layout）的layout方法。

###Dirty bit system
为了不要因为一个小的改动就进行整体的layout，浏览器使用了dirty bit系统。发生变动或者新增的renderer会将自身及其后代进行dirty标识，表明需要layout处理。

有两种标记——dirty和后代是dirty。后代是dirty意味着该renderer至少有一个后代需要layout。

###整体layout和增量layout
整体layout就是整个render tree都触发layout。触发整体layout的条件有：

*   一个影响所有renderer的样式发生了改变，如body的font size发生变化。
*   屏幕的大小发生改变

增量Layout是指只有具有dirty标记的renderer会发生layout，它的触发是异步的（**如何理解？**）。例如从网络获取内容后新的节点追加到DOM tree后render tree就会产生新的renderer。

###异步layout和同步layout
增量layout是异步完成的。在FF中，增量layout有一个reflow commands的队列，会有一个调度程序去触发这些commands的批量处理。Webkit也会定时去执行一次增量layout--遍历render tree，对dirty render执行layout。

脚本请求样式信息，如offsightHeight的时候也同时会触发增加layout。
整体layout一般都是同步触发的。
有时候layout会作为初始layout的一个回调被触发，例如滚动条的位置发生变化。

###layout优化
resize或者renderer位置发生变化（大小没有变）所引起的layout中renderer的大小会直接从缓存获取，而不需要重新计算。

在某些情况下，仅仅只有sub tree发生改变的时候，layout不需要从root开始。这通常都发生在局部改变不会影响全局的情况，例如在text域中插入文本。

###layout的过程
通常包括以下几个步骤：

1. renderer确定自身宽度。
2. 遍历子节点：

    1. 为子renderer定位。
    2. 调用子renderer的layout（前提是他们具有dirty标记或者整体需要layout）--获取子renderer的高度。
    
3. 通过所有子render的高度及margin和padding的高度来计算该renderer的高度。--它的parent需要用到这个高度。
4. 将该renderer的dirty设置为false。

###宽度的计算
renderer宽度的计算需要使用container block的宽度、renderer样式的width属性、margins和borders。
以下面这个div为例：

    <div style="width:30%" />
    
在webkit中会这样计算：

* container的宽度是container的availableWidth和0的最大值。availableWidth在这里可以看作是contentWidth，计算如下：clientWidth() - paddingLeft()-paddingRight()
clientWidth和clientHeight不包括border和scrollbar。
* element的宽度是指属性计算的'width'
* 水平的borders和paddings被加进来。

这样计算得到了最优(preferred)宽度，还需要计算最大和最小宽度。如果preferred的值高于最大值，那就会使用最大宽度，如果小于最小值，那就使用最小值。这个值会被缓存，在layout前都不会发生变化。

###换行
在layout中，renderer需要换行的时候，它会停止layout，然后冒泡给父renderer，父renderer会增加额外的renderer并对进行它们进行layout
