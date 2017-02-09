title: 原生js练习题---杂项
date: 2016-05-14 15:17:44
tags: [javascript,前端]
categories: 前端
---
最后一篇了，来做几个查漏补缺的小练习吧：

##   0x1 js条件注释 仅IE执行
实现效果：
<p data-height="300" data-theme-id="17410" data-slug-hash="zqXpVG" data-default-tab="result" data-user="levonlin" data-embed-version="2" class="codepen">See the Pen <a href="http://codepen.io/levonlin/pen/zqXpVG/">7-01js条件注释 仅IE执行</a> by levonlin (<a href="http://codepen.io/levonlin">@levonlin</a>) on <a href="http://codepen.io">CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>

这是兼容老旧IE的的常用手段之一，代码里实现了两种方式：

一是js里的条件注释，可在IE10及以下使用。形如：

```javascript
/*@cc_on
    @if (@_jscript)
        // 注释之内，给IE10及以下执行的javascript
    @else*/
        // 注释之外，给IE11及非IE浏览器执行的javascript
    /*@end
@*/
```

不过貌似codepen会把js里的注释全去掉，这里看不出效果。。。

二是html里的条件注释，只能在IE9及以下使用。通过在注释内添加`<style>`、`<script>`标签用于给IE指定css或js，还可指定IE版本号，形如：

```html
    <!--[if [! / lt / gt / lte / gte] IE [版本号]]>
    <![endif]-->
```

##  0x2 js 实现进制转换
实现效果：
<p data-height="300" data-theme-id="17410" data-slug-hash="aNrbLG" data-default-tab="result" data-user="levonlin" data-embed-version="2" class="codepen">See the Pen <a href="http://codepen.io/levonlin/pen/aNrbLG/">7-02js 实现进制转换</a> by levonlin (<a href="http://codepen.io/levonlin">@levonlin</a>) on <a href="http://codepen.io">CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>

虽然可以自己实现，但其实js已经自带了进制转换的方法：

* n进制转十进制使用`parseInt(num, base)`方法，第二个参数为原来的数制。
* 十进制转n进制使用`toString(base)`方法，参数为目标数制。

用起来是蛮简单的，但要注意到两者间的区别：

1. 调用对象不同，`parseInt`是个全局方法；`toString()`则必须通过`Number`对象而不是基本的数字类型的调用，才能实现进制转换，使用诸如`10.toString(2)`是会报错的。
2. 返回类型不同，`parseInt`返回数字而`toString()`返回字符串。
3. 两者虽然都只支持2至36进制，但在传入超出这个范围的进制时却有不同的处理，其中`parseInt`直接返回NaN，而`toString()`会报`Uncaught RangeError`。

## 0x3 js base64加密转换
实现效果：
<p data-height="300" data-theme-id="17410" data-slug-hash="JXqepv" data-default-tab="result" data-user="levonlin" data-embed-version="2" class="codepen">See the Pen <a href="http://codepen.io/levonlin/pen/JXqepv/">7-03js base64加密转换</a> by levonlin (<a href="http://codepen.io/levonlin">@levonlin</a>) on <a href="http://codepen.io">CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>

这题其实分为两个部分，一个是base64加解密、一个是将图片转为base64加密的dataURI。

[关于base64](https://zh.wikipedia.org/wiki/Base64)

第一部分很简单，因为现在的浏览器都已经自带了base64的加解密的功能，不用再自己编写js实现了。其中，加密使用`btoa()`，解密使用`atob`。而如果只要加密某个子串就再加个正则匹配出来即可。

至于第二部分，需要使用html5的File Api。这个api有个比较固定的使用模式：

* 获取文件集合：首先监听`<input type="file">`元素的`change`事件获取上传文件的集合`files`；
* 获取文件属性：`files`是由一系列File对象组成的，所以接下来可以通过File对象的`type`、`size`、`name`、`lastModifideDate`属性来获取文件的属性；
* 保存文件内容：只靠File对象的属性并不能直接读取文件的内容，而是要借助一个`FileReader`对象来实现。`FileReader`对象提供了几种读取文件数据的方法，其中就有我们需要的`readAsDataURL`方法，该方法会将文件内容转为data URI的形式存放于`FileReader`对象的`result`属性中。至于其他的读取方法，可参见[这个](https://developer.mozilla.org/zh-CN/docs/Web/API/FileReader)。
* 获取文件内容：利用`FileReader`读取文件后我们就可以在其`result`属性中获取文件内容了。但由于文件读取是个异步的操作，所以我们还需要借助`FileReader`对象的`progress`、`error`、`load`等事件，才能从`result`属性中获取完整的文件内容。

当然，这里获取的文件内容只是图片的data URI，要显示图片还得把URI赋给`img`元素的`src`属性。关于data URI的介绍，可参见我写过的[data-URI及其应用](http://levonlin.info/2015/11/19/data-URI%E5%8F%8A%E5%85%B6%E5%BA%94%E7%94%A8/)。

## 0x4 正则应用练习
验证QQ号码的正则表达式：

```javascript
/^[1-9]\d{4, 10}$/
```

就当了解下目前QQ号的特点：
* 以1到9开头
* 总共5至11位

再附赠个[正则速查表](http://pandoraui.github.io/jquery-chm/regexp.html)。

## 0x5 操作cookie及提取数据
原题的网页不见了，附上高程里的`cookieUtil`对象吧：

```javascript
var CookieUtil = {
    get: function (name) {
        var cookieName = encodeURIComponent(name) + '=',
            // 获取名值对起始下标
            cookieStart = document.cookie.indexOf(cookieName),
            cookieValue = null;
        if (cookieStart > -1) {
            // 获取名值对终止下标
            var cookieEnd = document.cookie.indexOf(';', cookieStart);
            if (cookieEnd) {
                cookieEnd = document.cookie.length;
            }
            // 截出并解码
            cookieValue = decodeURIComponent(document.cookie.substring(cookieStart + cookieName.length, cookieEnd));
        }
        return cookieValue;
    },
    set: function (name, value, expires, path, domain, secure) {
        var cookieText = encodeURIComponent(name) + '=' + encodeURIComponent(value);
        if (expires instanceof Data) {
            cookieText += '; expires=' + expires.toUTCString();
        }
        if (path) {
            cookieText += '; path=' + path;
        }
        if (domain) {
            cookieText += '; domain=' + domain;
        }
        if (secure) {
            cookieText += '; secure';
        }
        document.cookie = cookieText;
    },
    // 使用相同的路径、域和安全选项，并且将失效时间设为过去时间以删除cookie
    unset: function (name, path, domain, secure) {
        this.set(name, '', new Date(0), path, domain, secure);
    }
};
```

## 结语
这个系列做了很久了，从寒假到现在整好三个月，几乎成了每天的日常任务。期间既有对基础的巩固、也遇到过各种深坑，但都走过来一一克服了。说实话，在接触到这份习题集时，面对许多没有做过的效果，尤其是后面几个大制作，我实在很难想见自己完成它的一天。但是现在我是真真实实完成了，事实证明，有了扎实的基础、沉下心来对这些效果条分缕析，再复杂的效果也能做出来。

当然，做出来是一回事，做得好、写出规范优雅的代码则是另一回事。由于历史的原因、原题的实现多写得不够优雅甚至有些混乱。而我一开始就要求自己甩掉历史包袱、轻装上阵，事实上这样做虽然让自己偷了很多兼容性上的懒，但正是这样才让自己更专注于实现功能和写出更好的代码。而且我也坚信这是前端未来的发展方向，毕竟和其他领域一样，能真正流传下来、成为未来标准的，必然是优雅的最佳实践，而不是靠堆砌出来的糟糕代码。

至于做记录，也坚持下来了，7篇文章也算是总结了不少东西，希望还能帮到更多的人吧！

这个系列题目主要是针对原生js，虽然这是很重要的基础，但也只是前端知识庞杂体系中的一部分而已。前端能从单纯的切图发展为一个炙手可热的岗位，说明它本身内容就很广博，而且常变常新也是前端界的一大特点。所以仅靠自己小打小闹获得的一点浅见是不能在业界立足的，否则就要面临经验与知识面不够带来的掣肘了。接下来的日子，还是得继续强化主干上的知识点、体验和跟上新变化，以期形成综合的开发能力、构建成自己的技术栈吧！
