title: 元素auto宽高的影响因素
date: 2016-05-13 19:13:38
tags: [前端,css]
categories: 前端
---
我们知道，元素在没有设置宽高的情况下，默认的宽高值都为`auto`。而这个`auto`到底是如何让元素自动拥有宽高的呢，也就是说，元素的宽度和高度到底会受到什么因素的影响？本文将通过一个例子来对这一问题一探究竟，HTML结构和初始css如下：

```html
    <div class="container">
        <div class="test"></div>
    </div>
```

```css
    .container {
        position: relative;
        width: 200px;
        height: 200px;
        background-color: #ff9;
    }
    .test {
        border: 1px solid #979797;
        background-color: #f1f1f1;
    }
```

## 元素普通流定位（position为static或relative）
### auto宽度
先看上面代码运行结果：

![](http://ww4.sinaimg.cn/large/85ad0d9cgw1f3xjhpp3aqj206405sa9w.jpg)

再往元素里加点内容：

![](http://ww4.sinaimg.cn/large/85ad0d9cgw1f3xjh9u8jjj206506c3yd.jpg)

所以不设置`position`属性时（也就是取默认值`static`），就如上面的运行结果所示，元素的宽度似乎会自适应于容器的宽度，而高度则和元素内容高度有关。

但要注意所谓的宽度自适应并不是指元素的宽度就一定会等于的容器宽度，而是指元素的**宽度+左右padding+左右border+左右margin**等于的容器宽度。比如下面我再给`.test`元素加上margin之后，元素的宽度可就不等于容器的宽度了：

![](http://ww3.sinaimg.cn/large/85ad0d9cgw1f3xjigtzbrj206805w3yf.jpg)

在进一步测试极端情况：

![](http://ww1.sinaimg.cn/large/85ad0d9cgw1f3xjir3546j20au065jra.jpg)

此时元素的宽度被margin挤得不见，文本溢出了。

### auto高度
由上文可见元素的auto高度只会一直随内容的高度在同步变化，并不会去“自适应”容器的高度，即使元素已经溢出容器：

![](http://ww4.sinaimg.cn/large/85ad0d9cgw1f3xjj2eflwj205t08bjsc.jpg)

当然，上面只是讨论了元素内容为文本的情况，所以auto高度才这么老实地跟随内容变化。而如果内容是块级元素，虽然一般情况下auto高度还是随内容高度变化的，但若内容采用绝对定位或浮动时，就会导致我们很熟悉的“溢出”（高度塌陷）了：

![](http://ww3.sinaimg.cn/large/85ad0d9cgw1f3xjjc31wyj206m06cmx3.jpg)

![](http://ww1.sinaimg.cn/large/85ad0d9cgw1f3xjjvhz4fj206906c3yf.jpg)

同时通过这两个例子，也可以看到auto宽度还是继续在自适应容器的宽度。

### 结论
结论就是：在默认定位的情况下，元素的auto宽度始终都会自适应于容器的宽度；而auto高度则和元素内容的高度有关，除非遇到内容浮动或绝对定位时引发的高度塌陷。

若采用相对定位，结果和上面也是一样的。原理也很好理解，毕竟相对定位时元素还是在原位置渲染的，只是多了个可以设置相对偏移罢了，各位可以自己试试看~

## 元素绝对定位和浮动
### auto宽度
现在我们让`.test`元素绝对定位：

![](http://ww2.sinaimg.cn/large/85ad0d9cgw1f3xjkeskfcj205p05u0sk.jpg)

再加点内容：

![](http://ww1.sinaimg.cn/large/85ad0d9cgw1f3xjkoyy4mj205z05xjr8.jpg)

只有添加了内容元素才会有宽度，显然宽度只会适应内容的宽度咯。但还有个特殊情况，就是文本内容的宽度会因为换行飘忽不定，所以当我们再加长文本长度后：

![](http://ww1.sinaimg.cn/large/85ad0d9cgw1f3xjkxku60j205x05wq2t.jpg)

`.test`元素的文本居然自动换行，导致宽度适应容器了，再禁止换行看看：

![](http://ww1.sinaimg.cn/large/85ad0d9cgw1f3xjlbs4htj20b8066dfr.jpg)

嗯，在不能换行时，就还是只会适应内容的宽度。所以下面要解决的，就是文本内容的换行规则是什么，或者说文本内容的宽度受什么影响？

规则其实不难：在可以自动换行（`white-space: normal/pre-wrap/pre-line`）时，文本中最长的一个单词或中文字符的长度就是最小的换行单位（若设置了`word-break: break-all`甚至可以取每个字母的长度作为换行单位），这个最小的换行单位是不可分割的。在文本容器宽度不够时，文本就只会在空格或`-`等字符处自动换行，而不会去分割换行单位。这条规则是不能违背的，我们可以来弄个超长的单词看看：

![](http://ww1.sinaimg.cn/large/85ad0d9cgw1f3xjlo557gj208u06cdfs.jpg)

上面的结果说明，`.test`元素的文本确实会自动换行来让元素宽度适应容器，但也只能是在换行规则下尽力而为，不会为适应容器而去分割单词；当然，若设置`word-break: break-all`就可以“尽力”得到这样的结果了：

![](http://ww1.sinaimg.cn/large/85ad0d9cgw1f3xjlx6i0qj206o06kweg.jpg)

而如果`.test`元素到了容器外，文本依然会换行让`.test`元素宽度“适应”容器，哪怕此时给挤压成这样：

![](http://ww2.sinaimg.cn/large/85ad0d9cgw1f3xjm3cx53j20aq06e3yg.jpg)


### auto高度
最后来看下auto高度，上面几个例子中auto高度也是一直随文本内容的高度在同步变化。而如果内容是浮动时，也应该是这样，因为设置容器绝对定位也是清除浮动的方法之一嘛：

![](http://ww2.sinaimg.cn/large/85ad0d9cgw1f3xjmc9umcj2066065dfq.jpg)

但当内容是绝对定位时，还是会溢出，而且因为内容溢出导致宽度也没了：

![](http://ww3.sinaimg.cn/large/85ad0d9cgw1f3xjmiv3qhj205z060t8m.jpg)

### 结论
综上，结论就是：

1. 内容为纯文本的元素绝对定位时，则其文本会尽量尝试换行以让元素的auto宽度适应容器的宽度。
2. 而若元素内容只是定宽的块级元素，就不存在这种auto宽度变来变去的情况。且当内容是绝对定位或浮动的，不会让元素的宽高被直接置0。
3. 当元素内容既有纯文本又有块级元素时，显然就得比较两者谁更宽，元素的auto宽度最终由最宽者决定。
4. auto高度只和元素的内容的总高度有关，除非遇到内容绝对定位时引发的高度塌陷。

同样，经过验证，`.test`采用浮动定位时，结论和上面讨论的绝对定位是一样的。

## 2016-08-06更新：关于表单元素
自己原来的想法还是too young了，没有考虑到表单元素这种存在.由于浏览器对一些表单元素的原生实现是基于操作系统的，这就导致表单元素的auto宽高并不符合上面提到的那些规律。

比如一个按钮，不管是在何种定位之下、也不管容器如何，只要不手动指定宽高它就还是那个样子。而且由于和操作系统有关，所以浏览器并没有给我们提供能够取消这种默认行为的属性，也就是说我们不能采用诸如`-webkit-appearance: none;`这样的做法来覆盖默认样式。故而上面对auto宽高的讨论对这些表单元素都是不适用的，面对表单元素时我们没必要再auto宽高的问题，有什么需求就老实去去手动指定好了。

想了解更多关于表单元素原生的内容，可参考我翻译的[原生表单组件](http://levonlin.info/2016/05/24/%E3%80%90%E8%AF%91%E3%80%91%E5%8E%9F%E7%94%9F%E8%A1%A8%E5%8D%95%E7%BB%84%E4%BB%B6/)。

