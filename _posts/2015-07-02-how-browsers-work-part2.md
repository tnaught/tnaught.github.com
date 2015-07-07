---
layout: post
title: "(译)浏览器是如何工作之part2-renderengineflow"
---

网络部件获取到网络资源后，渲染引擎开始工作，一般需要8k的空间。  
基本的工作流程如下：

>1. *parse HTML*，产生**DOM tree**
>2. DOM tree 和 computing styles *attach*，产生**render tree**
>3. *layout* **render tree**，在FF中layout又称为reflow
>4. *paint* **render tree**

`parse`：解析，将文档进行结构化，并进一步翻译为计算机可以理解的语言，一般需要将文档进行词典提取(分词)和语法定义，然后使用至上而下或自下而上的算法遍历文档，最后生成解析树。

`render tree`:  具有颜色、大小等视觉信息的矩形块，矩形块按照现实在屏幕上的顺序排列，矩形块在webkit中称为renderer object，在Gecko中称为frame

`layout`：赋予每一个节点在屏幕上确切的坐标位置

`paint`：使用UI后端部件绘制render object