title: 原生js练习题---第五课
date: 2016-04-13 17:27:02
tags: [javascript,前端]
categories: 前端
---
## 0x1模拟select控件
实现效果：
<p data-height="257" data-theme-id="17410" data-slug-hash="yOKeqm" data-default-tab="result" data-user="levonlin" class="codepen">See the Pen <a href="http://codepen.io/levonlin/pen/yOKeqm/">5-01模拟select控件</a> by levonlin (<a href="http://codepen.io/levonlin">@levonlin</a>) on <a href="http://codepen.io">CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>

比较简单的点击事件处理，也就处理点击选择框展示菜单、点击菜单选择、点击页面任意角落隐藏菜单这三件事。这里注意点击选择框展示菜单时一定要阻止冒泡，要不然会冒到顶层元素直接触发隐藏菜单的事件，导致菜单点不出来。

至于样式方面，还是用背景图的方式来实现一个拟物化的效果，这种做法虽然兼容性好，但是需要固定元素的宽高位置、使用固定的背景偏移来“凑出”一个效果。

当然这些固定也不一定是绝对的，遇上背景图不够大简单的方法就是设置repeat；遇上复杂背景还可以靠增加元素层级、逐层放一个背景片段、最后再组合起来的方式来实现（这里就得设置背景`no-repeat`了）。比如这里圆角搜索框的实现就是祖父一个圆角、父元素再一个圆角、最后子元素放上主体背景做出来的。然而这种事后的补救和折中总给人繁琐而且不够优雅的感觉，多少也是和设计沟通不到位的结果吧-_-||...

## 0x2点击页面，显示单击的坐标、0x4阻止右键菜单（阻止默认事件）
实现效果：
<p data-height="300" data-theme-id="17410" data-slug-hash="JXvPbN" data-default-tab="result" data-user="levonlin" data-embed-version="2" class="codepen">See the Pen <a href="http://codepen.io/levonlin/pen/JXvPbN/">5-02点击页面，显示单击的坐标</a> by levonlin (<a href="http://codepen.io/levonlin">@levonlin</a>) on <a href="http://codepen.io">CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>

熟悉一下鼠标事件而已，除了获取视口坐标、页面坐标、屏幕坐标，这里还顺便实践了下鼠标事件的4个按键属性（其中按meta键时会唤出开始菜单，所以无效了）和`button`属性。由于`click`事件和鼠标右键无关，无法用`button`属性判断是否点了右键，所以这里改用`mouseup`事件代替；同时也把简单的第四题一并做了，阻止了点右键后冒出右键菜单。

## 0x3用户按下键盘，显示keyCode
实现效果：
<p data-height="300" data-theme-id="17410" data-slug-hash="ZWoBpG" data-default-tab="result" data-user="levonlin" data-embed-version="2" class="codepen">See the Pen <a href="http://codepen.io/levonlin/pen/ZWoBpG/">5-03用户按下键盘，显示keyCode</a> by levonlin (<a href="http://codepen.io/levonlin">@levonlin</a>) on <a href="http://codepen.io">CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>

再熟悉下键盘事件，为充分体验到每个按键的效果，防止f5刷新等，这里还阻止了默认事件。

至于获取输入的内容，要注意`keyCode`（针对keydown/keyup）、`charCode`（针对keypress）属性只能获取非字符、小写字母和数字的ascii码，而且即使你按了shift键也不能获取大写字母和其他字符。

所以要获取输入的是哪个字符应该使用`key`（FF和IE，返回按键文本字符）或`keyIdentifier`（webkit，返回形如’U+unicode码’的字符串），通过这个练习能体会到他们的不同。

## 0x5跟随鼠标移动（大图展示）
实现效果：
<p data-height="300" data-theme-id="17410" data-slug-hash="Vaxgmz" data-default-tab="result" data-user="levonlin" data-embed-version="2" class="codepen">See the Pen <a href="http://codepen.io/levonlin/pen/Vaxgmz/">5-05跟随鼠标移动（大图展示）</a> by levonlin (<a href="http://codepen.io/levonlin">@levonlin</a>) on <a href="http://codepen.io">CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>

继续鼠标事件，核心思路就是监听`mouseover`、`mouseout`、`mousemove`三个事件分别实现大图的展示、隐藏、移动，还是通过三个事件代理就可轻松实现。

具体实现上，主要考察通过`Image`对象来加载大图，首先，这样做就使大图在变成在后期加载，免去了加载页面主体时被几个大图阻塞的问题；第二，还能通过`Image`对象的`complete`属性和`load`事件来判断是否加载完，根据加载进度进行相应的操作，以提高用户体验。同时在大图展示上，如果右侧显示区域不够, 还要对大图显示位置做相应调整，这就涉及到一点小计算了。

## 0x6自定义右键菜单
实现效果：
<p data-height="300" data-theme-id="17410" data-slug-hash="EKRVav" data-default-tab="result" data-user="levonlin" data-embed-version="2" class="codepen">See the Pen <a href="http://codepen.io/levonlin/pen/EKRVav/">5-06自定义右键菜单</a> by levonlin (<a href="http://codepen.io/levonlin">@levonlin</a>) on <a href="http://codepen.io">CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>

思路其实很简单，阻止默认的`contextmenu`事件换成显示自定义菜单，在加个点击事件实现隐藏菜单即可。但在交互细节上还有一些可优化的点：
* 首先，要出现自定义菜单的地方未必是整个页面，若是只在某一区域内出现，那么菜单定位就要相对这个区域才行，而`clientX`和`clientY`都是相对整个屏幕而言的，这时就需要我们重置下坐标系了。
* 第二，和上面一题一样，点右键后右边和下边的空间未必能容得下整个菜单，所以还要根据剩余空间来决定菜单出现的位置，防止菜单出界导致样式失调。

## 0x7用键盘控制Div
实现效果：
<p data-height="300" data-theme-id="17410" data-slug-hash="jqpRzL" data-default-tab="result" data-user="levonlin" data-embed-version="2" class="codepen">See the Pen <a href="http://codepen.io/levonlin/pen/jqpRzL/">5-07用键盘控制Div</a> by levonlin (<a href="http://codepen.io/levonlin">@levonlin</a>) on <a href="http://codepen.io">CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>

原本以为只是个简单的键盘事件练习，没想到遇到的坑还是挺多的，因为浏览器的键盘事件是不支持组合按键的，只能一次触发一个按键上的事件。而这题明显就要求使用组合键了，一个简单的思路就是给要用到的组合按键分配一个对应的布尔值，按下一个按键触发`keydown`就标记该按键为`true`，而松开按键触发`keyup`时则标记为`false`，这样后续的其他按键事件触发时就能获知按键的组合了。

对于`ctrl`和其他键组合，可以直接使用上面提到的思路，即先标记`ctrl`，按下其他按键时就可判断是否同时按住`ctrl`，执行相应的动作即可。

而在使用方向键时，除了组合的需求，还要实现长按按键使方块连续运动的效果。但长按时`keydown`的触发却是在触发一次后，等一段时间再连续触发，如果只在`keydown`事件的处理函数中对方块的运动做处理，就会使得方块的运动效果显得卡顿而不流畅。

那该如何是好？原题给出思路是：让`keydown`事件负责标记按键就好了，而方向键的事件处理使用`setInterval`设个周期比较小的定时器持续监听，由于周期小，长按时就会立刻执行相应的事件处理，效果更加流畅。

除了效果的流畅，还要注意阻止默认事件，毕竟`ctrl`和方向键都是浏览器常用的功能键。同时和前面的题目一样，也必须限制效果的生成范围，不能让小方块跑到没边的地方去了。

## 0x8 Div闪烁
实现效果：
<p data-height="300" data-theme-id="17410" data-slug-hash="xVabpQ" data-default-tab="result" data-user="levonlin" data-embed-version="2" class="codepen">See the Pen <a href="http://codepen.io/levonlin/pen/xVabpQ/">5-08 Div闪烁</a> by levonlin (<a href="http://codepen.io/levonlin">@levonlin</a>) on <a href="http://codepen.io">CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>

简单的一个定时器应用，用`setInterval`或`setTimeout`都可以实现。

当然，我还是比较喜欢用`setTimeout`，因为`setInterval`完全可以用`setTimeout`的递归调用来模拟，而且还有以下几个原因：

* 首先，链式调用`setTimeout`可以严格保证执行顺序，而`setInterval`就不那么让人省心了，详见[前端碎语（3）](http://levonlin.info/2016/02/24/%E5%89%8D%E7%AB%AF%E7%A2%8E%E8%AF%AD%EF%BC%883%EF%BC%89/)
* 而且`setInterval`一开始还要等时间间隔跑完才调用回调函数，若是使用`setTimeout`就可以直接调用递归函数立即开始；
* 最后，`setTimeout`的递归函数要中止只需破坏递归条件即可，不用再使用`clearTimeout`，还能省去定义一个定时器变量~

--- 第五课完 ---

