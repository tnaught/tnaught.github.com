---
layout: post
title: "(译)浏览器是如何工作之part4-renderengineflow"
---

###渲染树和DOM树的关系
渲染树同DOM树有对应的关系，不过不是一对一的关系。

* 1:0，不显示的dom不会插入render tree，例如head、display为none的元素(不包括visibility为hidden元素)。
* 1：多，有的dom对应多个视觉对象。一种情况是dom结构过于复杂不能用一个矩形描述。例如select，一个select元素对应3个renderer：显示区域、下拉列表、按钮区域。另外一种可能的情况是文本太长，被分割成多行显示，每一个行就是一个单独的renderer。
* 1:1，但是两者在树中的位置不对应。浮动定位和绝对定位得元素是在文档流外的，被放在render tree中不同的位置上，映射到真实的frame上。

###render tree构建的流程
在FF中，表现层(presentation)监听了DOM的更新事件，表现层委托FrameConstructor去构造frame，构造器计算样式同时创建frame。

在webkit中计算样式和创建renderer的过程称为attachment。每一个dom都有一个attach方法，**dom的更新和attachment动作是同时进行的**，在dom中插入一个节点就会调用新node的attach方法。

html和body标签处理完成后生成了render tree的根节点。**根节点对应了css规范中的containing block，最顶层的block，包含其他所有blocks。它的大小是整个viewport的大小。FF中把它叫做ViewPortFrame，webkit叫做RenderView。document就是指这个render object。**剩下的renderer就是由新node的插入生成。

###样式的计算
node进行attach的时候会计算视觉属性然后生成renderer。这个样式包括不同来源的stylesheets、行内样式和HTML的视觉属性(**译至visual properties，不知道是什么鬼**)。visual properties会被翻译成css样式属性。

stylesheets的来源有：浏览器默认样式、页面开发人员定义的样式、用户自定义的样式。

样式的计算是一个很复杂的工程，因为样式规则很复杂，规则的存储和匹配都具有相当的难度。

浏览器的解决方案有：
####共享样式数据
