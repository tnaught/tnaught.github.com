---
layout: post
title: '关于字符编码你都知道哪些'
date:   2015-05-18
---

看过的东西如果不写下来，真的很容易忘记，前一段时间看过阮一峰老师写的博客[字符编码笔记：ASCII，Unicode和UTF-8](http://www.ruanyifeng.com/blog/2007/10/ascii_unicode_and_utf-8.html)，今天想关于编码的概念的时候已经记不起之前看过的内容了，所以又去重新看了一遍，为了加深记忆，所以也仿照做一个笔记。

###1.  厘清概念  
-   字符集：  
计算机可以支持显示的字符的一个集合，简单的来讲，就是把所有的字符做了一个分类，给每一个分类一个命名，类似于语言可以区分英语、中文、日文一样，字符集包括`ASCII`、`GB2312`、`Unicode`等。字符集中的字符需要转化为计算机可以处理的符号需要编码，正确的为字符编码需要三个要素：字库表(`character repertoire`)、编码字符集(`coded character set`)、字符编码(`character encoding form`)，字库表对应用来的显示的字符，编码字符集对应显示字符的一个序号(`code point`)，方便区分和索引，而字符编码是计算机实际存储的格式，即字库的一种实现形式。理论上可以直接使用编码字符集中的编码来存储，但是对于不同的数字用同样的空间去存储会造成浪费，所以就需要字符编码转换成计算机易存储的模式。

-   字符编码：  
字符在计算机中实际存储的转换过程。

-   两者的关系：  
字符集为字符编码提供了依据，有些字符集在定义的时候同时规定了字符编码方式。

###2.  常用字符集和字符编码  
对于ASCII、GB2312、Big5、GBK、GB 18030 之类的遗留方案来说，基本上一个字符集方案只使用一种编码方案。提及他们的时候说的是一个字符集和字符编码的联合方案。Unicode则有一些不同，Unicode/UCS首先是一个统一的字符集标准，同时定义了几种可选的编码方案encoding form，主要包括UTF-8、UTF-16、UTF-32。所以可以看出Unicode和UTF-8的区别在于Unicode是一套完整的编码字符集，而UTF-8只是这个字符集的一种具体实现方案。
>值得一提的是，在windows(以win7为例)系统中保存记事本可选的编码有：ANSI、Unicode、Unicode big endian、UTF-8。ANSI指的是当前系统的遗留编码，中文系统为GB2312对应的字符编码，英文为ASCII字符编码；Unicode指的是带有BOM(byte-order mark)的小尾序little endian UTF-16；Unicode big endian相应的是带有BOM的长尾序UTF-16，_big endian/little endian/BOM可以参考阮一峰老师的那边博文，见参考资料1_；UTF-8指的是带BOM的UTF-8。

###3.  在html和javascript中的字符编码
-   url编码  
url中只允许使用英文字母、阿拉伯数字和某些标点符号，不能用其他文字和符号，所以其他文字和符号需要编码才能在url中使用，编码方式取决于浏览器。
引用参考文章[关于URL编码](http://www.ruanyifeng.com/blog/2010/02/url_encoding.html)中的几个结论：

>结论1:网址路径的编码，用的是utf-8编码。  
结论2:查询字符串的编码，用的是操作系统的默认编码。  
结论3:GET和POST方法的编码，用的是网页的编码。  
结论4:在Ajax调用中，IE总是采用GB2312编码（操作系统的默认编码），而Firefox总是采用utf-8编码。

-   meta标签中的content-type  
    {% highlight html %}
    <meta http-equiv="content-type" content="text/html;charset=UTF-8" />
    {% endhighlight %}
    
-   http请求中的Accept-Charset、Accept-Encoding、Content-Encoding  
Accept开头的属性值是浏览器发给服务器(request header)用来声明浏览器支持的特性。Content开头的属性值是服务器返回内容的特性。Accept-encoding中的encoding并不是支付编码，而是压缩的方式，默认值为：identity，常见的值有：compress,gzip(表示支持compress和gzip两种类型)，对应的response header为content-encoding。

-   escape：  
返回字符的Unicode编码值，除了ASCII字母、数字、标点符号@*_+-./以外，对其他所有字符进行编码。在\u0000到\u00ff之间的符号被转成%xx的形式，其余符号被转成%uxxxx的形式。对应的解码函数是unescape()。

>值得一提的是，form表单提交的时候会把空格转换为+，服务端处理的时候+号会转换为空格，所以使用+号的时候要小心。(需要测试)

-   encodeURI：主要用于对整个URI进行编码，不对URI中的一些组成符号如?#&/=+;:$,进行编码，编码后输入符号的utf-8形式，并在每个直接前加上%，对应的解码函数是decodeURI。需要注意的是，它不对单引号'编码

-   encodeURIComponent/decodeURIComponent：用于对URI中的局部进行编码，对?#&/=+;:$,符号都会编码，编码结果用encodeURI，对应的解码函数是decodeURIComponent。

__参考资料__：  
1.[字符编码笔记：ASCII，Unicode和UTF-8](http://www.ruanyifeng.com/blog/2007/10/ascii_unicode_and_utf-8.html)  
2.[Windows 记事本的 ANSI、Unicode、UTF-8 这三种编码模式有什么区别？](http://www.zhihu.com/question/20650946#answer-1426823)  
3.[字符集和字符编码（Charset & Encoding）](http://www.cnblogs.com/skynet/archive/2011/05/03/2035105.html)  
4.[十分钟搞清字符集和字符编码](http://cenalulu.github.io/linux/character-encoding/)  
5.[关于URL编码](http://www.ruanyifeng.com/blog/2010/02/url_encoding.html)
