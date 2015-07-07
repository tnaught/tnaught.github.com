---
layout: post
title: "动态加载script"
---

通常情况下，在页面上使用script标签来加载script资源，这是种静态加载脚本的方法。当网站中的需要加载的script脚本越来越多，同时script文件越来越大的时候，使用这种静态加载脚本的方法会造成页面的阻塞，同时加大服务器的压力。同时，并不是所有的脚本都需要在页面初始化的时候下载和执行。对于大型网站而言，不同的人会编写不同的功能模块，不同功能模块使用的脚本文件也需要按需加载。为了加快页面首次加载的速度，同时满足脚本按需加载的需求，script需要实现动态加载。

[补充](http://www.raychase.net/123):
1. 在ie6/7 firefox2/3 Safari3 Chrome1 和 opera下 script标签会阻碍下载，即script标签下面的网页资源在script加载完之前会停止请求、下载。
2. 在ie6/7下script标签下面的html元素在script加载完之前会停止渲染。
3. 在ie8，safari4，chrome2下script可以并发，但依然阻碍了其他资源的下载

###document.write的方法
    
    document.write('<script src="' + src + '" type="text/javascript"><\/script>');

值得注意的是:DomContentLoaded event要等script下载完成。document.write中如果嵌套使用了document.write,那嵌套的javascript是异步执行的。

问题:
1. document.write中的script下载完后才会加载后面的页面，所以会对页面造成阻塞([demo1]({{ DEMO_PATH }}/script-loader/demo1.html))
2. 它也会阻塞其他的动态脚本。除非多个脚本都通过一个script块中的document.write来加载.([demo2]({{ DEMO_PATH }}/script-loader/demo2.html)/[demo3]({{ DEMO_PATH }}/script-loader/demo3.html)/[demo4]({{ DEMO_PATH }}/script-loader/demo4.html))

###创建dom节点

    var attachScript = function ( url ) {
        var script = document.createElement('script');
        script.src = url;
        script.type = 'text/javascript';
        script.charset = 'utf-8';
        document.getElementsByTagName('head')[0].appendChild(script);
    };
    var urls = ['1.js','2.js'];
    for(var i = 0, len = urls.length; i < len; i++){
        attachScript(urls[i]);
    };

script的执行顺序并不是dom节点的顺序，而是按照script文件接收时间的先后来执行，这个接收时间受浏览器缓存的影响。类似于在script标签上增加了async=true的属性。若1.js的文件比2.js要大，可能先执行2.js，也可能先执行1.js，具有偶然性，不能做事先假定。直接在html页面中使用script标签（没有async属性）的时候，script文件的执行顺序同script书写的先后顺序一致。

注：浏览器对于javascript的运行有两大特性，（1）载入后马上执行（2）执行时会阻塞页面后续的内容（包括页面的渲染，其他资源的下载）

###ajax加载

    <script language="JavaScript">
        function GetHttpRequest () {
            if ( window.XMLHttpRequest ) {
                return new XMLHttpRequest() ;
            }else if ( window.ActiveXObject ) {
                return new ActiveXObject("MsXml2.XmlHttp") ;
            }
        }
        function AjaxPage (sId, url) {
            var oXmlHttp = GetHttpRequest();
            oXmlHttp.OnReadyStateChange = function() {
                if ( oXmlHttp.readyState == 4 ) {
                    if ( oXmlHttp.status == 200 || oXmlHttp.status == 304 ) {
                        IncludeJS( sId, url, oXmlHttp.responseText );
                    }else {
                        alert( 'XML request error: ' + oXmlHttp.statusText + ' (' + oXmlHttp.status + ')' ) ;
                    }
                }
            }
            oXmlHttp.open('GET', url, true);
            oXmlHttp.send(null);
        }
        function IncludeJS(sId, fileUrl, source) {
            if ( ( source != null ) && ( !document.getElementById( sId ) ) ) {
                var oHead = document.getElementsByTagName('HEAD').item(0);
                var oScript = document.createElement( "script" );
                oScript.language = "javascript";
                oScript.type = "text/javascript";
                oScript.id = sId;
                oScript.defer = true;
                oScript.text = source;
                oHead.appendChild( oScript );
            }
        }
        AjaxPage( "scrA", "b.js" );
    </script>