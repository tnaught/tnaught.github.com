两个相关术语：`stacking context`(栈上下文)和`stack level`(栈的级别)
z-index只对定位元素生效,定位元素是指position熟悉不是static值的元素.每一个具有z-index的定位元素都生成了一个`stacking context`,而z-index的值则表示该元素的`stack level`.z-index的值可以为整数(负数有效)和auto,z-index为auto的元素不会生成`stacking context`,除非元素为根元素.每一个`stacking context`都是受到上层`stacking context`的约束。

对于每一个`stacking context`,图层由低到高的顺序是:
1.	`stacking context`元素的background和borders
2.	z-index为负数的子级`stacking context`
3.	普通文档流_非行内_非定位后代元素
4.	非定位的浮动元素
5.	普通文档流_行内_非定位后代元素,包括inline tables和inline blocks
6.	z-index为0的子级`stacking context`
7.	z-index为正数的子级`stacking context`