title: 原生js练习题---第三课
date: 2016-03-14 17:34:53
tags: [javascript,前端]
categories: 前端
---
## 0x1用typeof查看数据类型
略过，不过`typeof`用来判断数据类型是不太靠谱的，尤其是涉及到引用类型的时候，除非是要检测一个变量是否有定义，否则最好采用`Object.prototype.toString`方法。

## 0x2用parseInt解析数字，并求和
实现效果：
<p data-height="257" data-theme-id="17410" data-slug-hash="PNGBWp" data-default-tab="result" data-user="levonlin" class="codepen">See the Pen <a href="http://codepen.io/levonlin/pen/PNGBWp/">3-02用parseInt解析数字，并求和</a> by levonlin (<a href="http://codepen.io/levonlin">@levonlin</a>) on <a href="http://codepen.io">CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>

对这道题来说，用户的输入无法三种：数字、非数字、空字符（不输入）；而原题里的实现为防止用户可谓是处心积虑，空字符直接弹窗、而非数字用`keyup`事件实现输一个删一个，用户只能乖乖输入数字了。但有个bug，按着按钮不动是交替触发`keydown`和`keypress`，这时输一个删一个的效果就没用了。

我这里更进一步修复了这个bug，想法很简单---我可以等你输完再把非数字全替换掉，只要把`replace`方法的正则改成全局匹配就ok。不过尴尬的是，既然有了这个无死角防止输入非数字的效果，还要`parseInt`干嘛。。。

## 0x3累加按钮，自加1
实现效果：
<p data-height="257" data-theme-id="17410" data-slug-hash="yOVXKq" data-default-tab="result" data-user="levonlin" class="codepen">See the Pen <a href="http://codepen.io/levonlin/pen/yOVXKq/">3-03累加按钮，自加1</a> by levonlin (<a href="http://codepen.io/levonlin">@levonlin</a>) on <a href="http://codepen.io">CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>

## 0x4输入两个数字，比较大小
实现效果：
<p data-height="257" data-theme-id="17410" data-slug-hash="VamWde" data-default-tab="result" data-user="levonlin" class="codepen">See the Pen <a href="http://codepen.io/levonlin/pen/VamWde/">3-04输入两个数字，比较大小</a> by levonlin (<a href="http://codepen.io/levonlin">@levonlin</a>) on <a href="http://codepen.io">CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>

第二题的变种，这次还是正常点，改用正则判断，不用第二题那魔性的自动删除字符了。

## 0x5页面加载后累加，自加1
实现效果：
<p data-height="257" data-theme-id="17410" data-slug-hash="aNBwjg" data-default-tab="result" data-user="levonlin" class="codepen">See the Pen <a href="http://codepen.io/levonlin/pen/aNBwjg/">3-05页面加载后累加，自加1</a> by levonlin (<a href="http://codepen.io/levonlin">@levonlin</a>) on <a href="http://codepen.io">CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>

第三题的变种，换成`setInterval`触发变化而已。

## 0x6判断数字是否为两位数
实现效果：
<p data-height="257" data-theme-id="17410" data-slug-hash="BKQZqw" data-default-tab="result" data-user="levonlin" class="codepen">See the Pen <a href="http://codepen.io/levonlin/pen/BKQZqw/">3-06判断数字是否为两位数</a> by levonlin (<a href="http://codepen.io/levonlin">@levonlin</a>) on <a href="http://codepen.io">CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>

继续正则判断，这一期的题有点无聊啊。。

## 0x7网页计算器
实现效果：
<p data-height="257" data-theme-id="17410" data-slug-hash="MypdJg" data-default-tab="result" data-user="levonlin" class="codepen">See the Pen <a href="http://codepen.io/levonlin/pen/MypdJg/">3-07网页计算器</a> by levonlin (<a href="http://codepen.io/levonlin">@levonlin</a>) on <a href="http://codepen.io">CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>

这题算是比较综合的题，所以相比前面的题目花了更多的时间，难点就是功能的实现。这里我的实现思路是比较简单的：就是用一个缓存区显示输入，再用一个公式区用缓存的数据构造计算式，最后点击等号时直接把计算式字符串用`eval()`执行输出。

这样一来这题就变成字符串处理了，在构造计算式时有很多分支情况，因为点击数字、小数点和运算符得考虑上一步的输入，一共有五种情况：运算符、数字、小数点、上一步的error、输入溢出屏幕。穷举出所有情况后，接下来就用正则判断出这些分支，以及对这些分支的各自处理了。

如上所述，因为只是简单粗暴的穷举，所以js里写了一堆`if-else`，感觉有点ugly⊙︿⊙。

除了js，还学到了一些新东西：比如文本框可以用`readonly`属性设置只读、用`maxlength`属性限制输入字符的个数（只能限制用户输入，如果像这题里直接修改文本框的`value`的话，这个限制是没有卵用滴）。

再者，在按钮样式设计上，这里使用了sprite图来做按钮效果，相比用纯css有更好的兼容性和可定制性；同时也了解到纯色的背景图片可以做得更小、要用时再平铺开来，能有效提高网页的性能。

## 08简易网页时钟
实现效果：
<p data-height="257" data-theme-id="17410" data-slug-hash="ONmRXb" data-default-tab="result" data-user="levonlin" class="codepen">See the Pen <a href="http://codepen.io/levonlin/pen/ONmRXb/">3-08简易网页时钟</a> by levonlin (<a href="http://codepen.io/levonlin">@levonlin</a>) on <a href="http://codepen.io">CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>

以一秒为周期获取当前时间进行更新即可。要注意的是得输出的时间字符串都必须为两位，这里使用`replace`方法搭配正则进行格式化，由于正则本身就有判断和筛选的作用，就不用再罗哩罗嗦地去判断每个字符串的长度了，写起来更加简洁优雅。

## 09倒计时时钟（100秒）
实现效果：
<p data-height="257" data-theme-id="17410" data-slug-hash="xVdEeb" data-default-tab="result" data-user="levonlin" class="codepen">See the Pen <a href="http://codepen.io/levonlin/pen/xVdEeb/">3-09倒计时时钟（100秒）</a> by levonlin (<a href="http://codepen.io/levonlin">@levonlin</a>) on <a href="http://codepen.io">CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>

核心内容和上一题差不多，只是把计时改变的内容由实时的时间换成了递减的秒数、再加上一个按钮控制动作。为了封装性更好，这里还用了一个单例对象来实现计时器，减少了无谓的全局变量。有了这个干净的接口，代码看起来清爽多了~

---第三课完---


