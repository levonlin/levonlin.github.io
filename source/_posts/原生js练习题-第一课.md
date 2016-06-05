title: 原生js练习题---第一课
date: 2016-02-17 17:26:42
tags: [javascript,前端]
categories: 前端
---
## 0x0关于这个系列
差不多把《js高级程序设计》刷完了，所谓实践出真知，尤其编程这种实践和经验相当重要的事情，不能光说不做。但以我现在的水平，直接上手那些大项目是还是不够格的，还是得一步步扎实好基础再有能力去开拓新世界、赶上前端界风起云涌的浪潮。

于是找来了前辈们留下的经典[原生js练习题](http://pandoraui.github.io/learning-javascript/)，以期把看过的内容用起来，加深自己对原生js的掌握。我是这么安排的：

1. 每一题先按自己已有的知识水平快速实现出功能来，不追求过度优化；面向未来、尽量使用标准ES5方法，不考虑兼容老旧和非标准浏览器。
2. 再通过查资料、对比他人的实现、按照前人代码优化的经验来对编程过程中出现的问题和不足进行总结、优化。并以此进行一次重构，让代码质量更上一层楼的同时也使自己掌握那些最佳实践。
3. 最后我要将自己做每道题的心得写成一个系列的博文、每课一篇，并把实现好的代码放到[我的codepen](http://codepen.io/levonlin/pens/tags/?selected_tag=%E5%8E%9F%E7%94%9Fjs%E7%BB%83%E4%B9%A0%E9%A2%98)演示。这样一来起到内化、总结作用，二来相当一个打卡、监督我自己完成，三来也是为了方便交流。

正是想到只闭门造车是不够的，还要在与其他开发者的长期交流中进一步提升自己、结交志同道合的伙伴，才有写这个系列博文的想法、才有了这个开篇。希望借自己的努力帮到更多的人吧，如果交流交流还能让hr收了小弟我，那也是极好的~

## 0x1控制div属性
实现效果：
<p data-height="257" data-theme-id="17410" data-slug-hash="mVYjym" data-default-tab="result" data-user="levonlin" class='codepen'>See the Pen <a href='http://codepen.io/levonlin/pen/mVYjym/'>1-01控制div属性</a> by levonlin (<a href='http://codepen.io/levonlin'>@levonlin</a>) on <a href='http://codepen.io'>CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>

简单的样式转换，对div的`style`属性修改样式即可，不过怎么优雅地修改就得好好想想了。

我把新样式存在一个对象里，并保证其属性名与style对象的属性名一致，这样既清楚又方便调用和维护。重置样式则直接将style对象的`cssText`属性清空即可，但我一开始还傻傻地用`window.getComputedStyle()`去获取原来的样式来替换。。。

这里还借用了构造闭包技巧来获取某次循环的循环变量值，原来cloudAi童鞋给的实现里是将这些量逐个绑到按钮上，但我还是感觉用上闭包这种利器更优雅。

最后，代码要遵循解耦应用逻辑/事件处理程序的原则，点击事件只负责提取获取按钮的索引即可，修改样式的事交给独立的应用程序去做。于是我在优化时把事件处理程序那一坨修改样式挪到外面去变成独立方法了。

**更新**：发现还可以在几个按钮所在的容器上使用事件代理来实现，这样就可以把那个循环和里面的复杂逻辑抛弃了，同时减少了事件处理程序的数目、提升了性能，也使程序更具拓展性。

顺便也把`changeStyle`方法优化了一下，原来那种依靠下标来确定功能、指望按钮的位置能和样式对象的属性顺序相同的方式太不靠谱了，而且实现起来复杂、代码读起来也不清晰。现在改为用id来确定功能，同时使用`switch-case`语句，有效解决了上述问题。

## 0x2网页换肤
实现效果：
<p data-height="257" data-theme-id="17410" data-slug-hash="NxZBZe" data-default-tab="result" data-user="levonlin" class='codepen'>See the Pen <a href='http://codepen.io/levonlin/pen/NxZBZe/'>1-02网页换肤</a> by levonlin (<a href='http://codepen.io/levonlin'>@levonlin</a>) on <a href='http://codepen.io'>CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>

这题也是简单的样式转换，倒是因为我只关注js久了，写起页面来反倒生疏了，连个基本的导航栏都要写半天，所以说前端这种涉及面广的工作还是得常练常写。

关键也就两部分---皮肤的切换、三个皮肤选择方块的样式切换。首先，还是得给三个皮肤选择方块找个`click`事件代理，方便事件的响应，然后就可以在响应里实现切换了。

这里把皮肤的样式存于不同的css文件，再在js里用个对象保存下各自的url，切换样式也就只负责切换link元素的href值而已，这样做既降低耦合，又添加、方便配置主题。

至于皮肤选择方块的样式切换，js做的也只是的active类的增删。为增删class还专门用正则实现了三个函数，也算练习了下正则。这里要注意使用RegExp()构造正则表达式时传的参数，只传表达式的内容就好了，不要再传个`//`；同时因为`\`用于字符串转义了，故应使用`\\`实现正则里的转义。

此外，这次看起来简单的开发过程中还因为使用`for-in`遍历`children`属性出了点bug让我摸索了好久，这里先不展开，我会再另外写篇文章好好分析这个问题。

## 0x03函数接收参数并弹出
实现效果：
<p data-height="257" data-theme-id="17410" data-slug-hash="NxQOWW" data-default-tab="result" data-user="levonlin" class='codepen'>See the Pen <a href='http://codepen.io/levonlin/pen/NxQOWW/'>函数接收参数并弹出</a> by levonlin (<a href='http://codepen.io/levonlin'>@levonlin</a>) on <a href='http://codepen.io'>CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>

简单的表单数据获取。

## 0x04用循环将三个Div变成红色
实现效果：
<p data-height="257" data-theme-id="17410" data-slug-hash="qbeJxJ" data-default-tab="result" data-user="levonlin" class='codepen'>See the Pen <a href='http://codepen.io/levonlin/pen/qbeJxJ/'>用循环将三个Div变成红色</a> by levonlin (<a href='http://codepen.io/levonlin'>@levonlin</a>) on <a href='http://codepen.io'>CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>

简单的样式转换，使用`getElementsByClassName`最方便XD。

## 0x05鼠标移入移出改变样式
实现效果：
<p data-height="257" data-theme-id="17410" data-slug-hash="PNYWqN" data-default-tab="result" data-user="levonlin" class='codepen'>See the Pen <a href='http://codepen.io/levonlin/pen/PNYWqN/'>1-05鼠标移入移出改变样式</a> by levonlin (<a href='http://codepen.io/levonlin'>@levonlin</a>) on <a href='http://codepen.io'>CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>

继续样式转换，虽然用css的hover伪类便能实现，但既然是js的练习题就得用用鼠标事件了。有两组事件可供选择，`mouseenter`和`mouseleave`、`mouseover`和`mouseout`，区别就在后者在鼠标移入移出子元素时还会再触发一次，而前者不会。


## 0x06记住密码提示框
实现效果：
<p data-height="257" data-theme-id="17410" data-slug-hash="RawoXm" data-default-tab="result" data-user="levonlin" class='codepen'>See the Pen <a href='http://codepen.io/levonlin/pen/RawoXm/'>1-06记住密码提示框</a> by levonlin (<a href='http://codepen.io/levonlin'>@levonlin</a>) on <a href='http://codepen.io'>CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>

和上面一题如出一辙，还是采用监听鼠标事件`mouseover`和`mouseout`修改样式。同时也复习一下label标签的用法：除了用`for`属性与指定的表单元素关联外，直接把表单元素包含在label里面也能实现关系。

这里还有个样式上的小问题，就是多选框的底部往往不会和同一label下的文字底部对齐的，为了让他们大致上对齐点，最好将多选框的`vertical-align`属性设为`middle`。

---第一课完---
