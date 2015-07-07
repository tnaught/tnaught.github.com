---
layout: post
title: "(译)浏览器是如何工作之part7——css2visualmodel"
---

###canvas
根据css2的规范，cancas是指渲染formatting structure的空间，在这个空间上浏览器可以绘制内容。理论上在这个空间的每个维度都是无限大的，然后浏览器基于viewport的大小设定了一个初始的宽度。被包含在canvas下的canva是透明的，如果没有设置颜色的话会被赋予一个浏览器定义的颜色。

###css合模型(box model)
css盒模型是根据视觉格式模型(visusal formatting model)进行布局和生成的一个矩形状盒子。每个盒子具有一个内容区域以及可选的padding、border和margin区域。

每一个元素会组成多个这样的盒子。

每一个元素都有一个display的属性来决定生成盒子的类型。

###定位方案
有三种定位方案：

1. 正常--根据对象在文档中的位置进行定位，就是说它在render tree中的位置和在dom tree中的位置相似，根据盒子的类型和大小进行布局。
2. 浮动--首先被当作正常文档流进行布局，然后尽可能向左或向右进行浮动。
3. 绝对定位--对象在render tree中的位置完全不同于在dom tree中的位置

定位方案由position和float两个属性决定

* position为static和relative为正常定位
* absolute和fixed为绝对定位

当position为static使用默认的位置，其他值的时候可以通过left、top、bottom、right指定位置。
 
盒模型的布局方式取决于：

* 盒子类型
* 盒子大小
* 定位模式
* 外部信息，如图片大小、屏幕大小

###盒子类型
块状盒模型：在浏览器窗口有自己的矩形  
行内盒模型：没有自己的block，但是会在一个containing block中。  
块级元素是在垂直方向上排列，行内元素则在水平方向上排列  
行内盒模型会排成一行，分布在一个line boxes中，当lines中的inline boxes按照baseline排列时，即元素的最低部分和另外一个元素的某点(**某点是哪点？**)对齐，而不是和另外一个元素的底端对齐，lines的高度会大于等于lines中最高的inline box。当container box的宽度不够的时候，inlines会被放在多个lines中。这一点在段落中很常见。

###Positioning

####Relative
在正常流的基础上偏移位置

####Floats
浮动元素被移动到line的最左边或者最右边

####Absolute和fixed
完全无视正常流的布局。位置相对于container。fixed的时候container是viewport。

###Layered representation
通过z-index属性进行指定。可以用来表现盒模型的三维状态。  
盒模型会被拆分成很多个栈(称为stacking contexts).在每一个栈上，后面的元素会先paint，前面的元素放在上面，跟接近用户。上面的元素会压盖下面的。
