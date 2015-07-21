###block formatting contexts,即bfc

正常流下boxes属于a formatting context，这个context可能是block，也可能是inline，也可能兼顾两者。block-level boxes是block formatting context,inline-level boxes是inline formatting context.

####block formatting contexts
floats,absolutely positioned元素,非block boxes的block containers(例如inline-blocks,table-cells,以及table-captions),以及overflow的值不是visible的block boxes（overflow的值传递给viewport的除外）会为它们的内容建立新的block formatting contexts.
>**block boxes和block container的区别?**
block container包含block boxes,除此外还有inline-blocks,table-cells,table-captions等

在一个block formatting context中，box是一个接一个垂直排列，开始于一个containing block的顶端。两相邻boxes之间的垂直距离取决于margin属性的值。同一个block formatting context中的两个相邻的block-level boxes的margin
值会发生合并。

在一个block formatting context中，每一个box的左外边界touches containing box的左边界（对于从右向左的formatting而言，right edges touch）。即使floats存在也是这样（一个box的line boxes可能会因为float收缩`shrink`）,除非这个box确定了一个新的block formatting context()。