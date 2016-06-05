title: 原生js练习题---第二课（上）
date: 2016-02-28 14:28:57
tags: [javascript,前端]
categories: 前端
---
## 0x1百度输入法
实现效果：
<p data-height="257" data-theme-id="17410" data-slug-hash="bpGMLK" data-default-tab="result" data-user="levonlin" class='codepen'>See the Pen <a href='http://codepen.io/levonlin/pen/bpGMLK/'>2-01百度输入法</a> by levonlin (<a href='http://codepen.io/levonlin'>@levonlin</a>) on <a href='http://codepen.io'>CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>

主要是切换的判断条件，一开始我还用点击次数的奇偶性来判断-_-||，其实直接检查菜单有没有显示即可。检查的时候有个小trick，初始状态菜单的`style`属性为空，因为我们接下来要点按钮显示，所以把这种情况和`style.display`为`none`的情况归为一类丢到`else`里最省事。

## 0x2点击Div，显示其innerHTML
实现效果：
<p data-height="257" data-theme-id="17410" data-slug-hash="XdJrBM" data-default-tab="result" data-user="levonlin" class='codepen'>See the Pen <a href='http://codepen.io/levonlin/pen/XdJrBM/'>2-02点击Div，显示其innerHTML</a> by levonlin (<a href='http://codepen.io/levonlin'>@levonlin</a>) on <a href='http://codepen.io'>CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>

这里遍历了div绑上事件，也可以用事件代理来做。

## 0x3求出数组中所有数字的和
实现效果：
<p data-height="257" data-theme-id="17410" data-slug-hash="wGBPQz" data-default-tab="result" data-user="levonlin" class='codepen'>See the Pen <a href='http://codepen.io/levonlin/pen/wGBPQz/'>2-03求出数组中所有数字的和</a> by levonlin (<a href='http://codepen.io/levonlin'>@levonlin</a>) on <a href='http://codepen.io'>CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>

简单地的表单数据获取和处理，注意对直接对字符串使用加号就是拼接，想用加法还得手动转换为数字。

## 0x4弹出层效果
实现效果：
<p data-height="257" data-theme-id="17410" data-slug-hash="EKaovB" data-default-tab="result" data-user="levonlin" class='codepen'>See the Pen <a href='http://codepen.io/levonlin/pen/EKaovB/'>2-04弹出层效果</a> by levonlin (<a href='http://codepen.io/levonlin'>@levonlin</a>) on <a href='http://codepen.io'>CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>

经典的弹出层效果，关键在CSS上，js只监听个样式的显示与否就ok。

## 0x5函数传参，改变Div任意属性的值
实现效果：
<p data-height="257" data-theme-id="17410" data-slug-hash="xVGKLR" data-default-tab="result" data-user="levonlin" class="codepen">See the Pen <a href="http://codepen.io/levonlin/pen/xVGKLR/">2-05函数传参，改变Div任意属性的值</a> by levonlin (<a href="http://codepen.io/levonlin">@levonlin</a>) on <a href="http://codepen.io">CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>

依然是对`style`对象做文章，既可直接设置`style`对象，也可以用DOM2的`setProperty()`方法。同样，清空`style`对象里的样式既可以直接将它置`null`，也可将其`cssText`属性清空。

## 0x6图片列表：鼠标移入移出改变图片透明度
实现效果：
<p data-height="257" data-theme-id="17410" data-slug-hash="pyJOBO" data-default-tab="result" data-user="levonlin" class="codepen">See the Pen <a href="http://codepen.io/levonlin/pen/pyJOBO/">2-06图片列表：鼠标移入移出改变图片透明度</a> by levonlin (<a href="http://codepen.io/levonlin">@levonlin</a>) on <a href="http://codepen.io">CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>

还是用js模仿css的hover效果（何苦这样-_-||），老套路，事件代理+增删类名搞定。因为事件代理是挂在祖先元素上的，现在就只能监听`mouseover`和`mouseout`事件了，因为`mouseenter`和`mouseleave`在光标进出子元素时是不会触发的。所以这样看来，`mouseover`和`mouseout`在子元素上也能触发这个特点简直就是为事件代理而生的嘛。

## 0x7简易选项卡
实现效果：
<p data-height="257" data-theme-id="17410" data-slug-hash="LNppBr" data-default-tab="result" data-user="levonlin" class="codepen">See the Pen <a href="http://codepen.io/levonlin/pen/LNppBr/">2-07简易选项卡</a> by levonlin (<a href="http://codepen.io/levonlin">@levonlin</a>) on <a href="http://codepen.io">CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>

继续模仿hover效果，还要加上对另一个div的样式控制。但是这次光标离开也仍要保持hover的状态，直到另一选项被hover，所以不能借助`mouseout`事件、只能遍历元素移除样式了，顺便也在遍历过程中取得另一个div要激活的内容。

## 0x8简易JS年历
实现效果：
<p data-height="257" data-theme-id="17410" data-slug-hash="jqbxaw" data-default-tab="result" data-user="levonlin" class="codepen">See the Pen <a href="http://codepen.io/levonlin/pen/jqbxaw/">2-08简易JS年历</a> by levonlin (<a href="http://codepen.io/levonlin">@levonlin</a>) on <a href="http://codepen.io">CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>

和上一题差不多，只是这次把修改类名换成修改数据而已。
