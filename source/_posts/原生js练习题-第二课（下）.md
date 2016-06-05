title: 原生js练习题---第二课（下）
date: 2016-03-06 14:00:13
tags: [javascript,前端]
categories: 前端
---
## 0x1播放列表收缩展开
实现效果：
<p data-height="257" data-theme-id="17410" data-slug-hash="XdmwPW" data-default-tab="result" data-user="levonlin" class="codepen">See the Pen <a href="http://codepen.io/levonlin/pen/XdmwPW/">2-09播放列表收缩展开</a> by levonlin (<a href="http://codepen.io/levonlin">@levonlin</a>) on <a href="http://codepen.io">CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>

值得注意的一个地方是那个箭头，我这里只是用了简单的字符串替换，而原题用了背景图片移动来实现切换箭头，但是似乎那样做会导致整个容器的左右偏移、效果不是很好。

## 0x2鼠标移过显示车标
实现效果：
<p data-height="257" data-theme-id="17410" data-slug-hash="Rarqze" data-default-tab="result" data-user="levonlin" class="codepen">See the Pen <a href="http://codepen.io/levonlin/pen/Rarqze/">2-10鼠标移过显示车标</a> by levonlin (<a href="http://codepen.io/levonlin">@levonlin</a>) on <a href="http://codepen.io">CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>

这题看起来和前面那道改变车标透明度的题差不多，但是却花了我不少时间。原因还是没有深入理解`mouseover`和`mouseout`事件的特点，虽然这两个事件移进子元素也会触发的特点便于实现事件代理，但是这样一来如果单纯在父元素上挂这两个事件就想在子元素上实现出现和消失的效果，就会导致连续触发---子元素出现触发父元素`mouseout`导致消失、再触发父元素`mouseover`，然后子元素又出现了，就这样闪个不停。。。

所以这题和前面那道题最大的不同就在此，因为要实现的是子元素的出现和消失，由于上述原因的掣肘、也因为子元素本身就比父元素要大，故应该采用监听li元素的`mouseover`使车标弹出，监听子元素的`mouseout`使其消失。

然而新问题又出现了，一开始我弹出的车标是个链接嵌套图片，而我只是监听链接的`mouseout`触发车标的消失。但这就导致一个诡异的情况，子元素弹出时鼠标就直接覆盖在图片上了，并不会触发链接上的`mouseover`，而是要一直移动到链接的border上才触发，然后移出border再触发链接上的`mouseover`。因为border本身宽度小，只要鼠标的移动速度过快，浏览器就会反应不过来，链接上的`mouseover`和`mouseout`就不会被触发！！如此一来，当鼠标快速地在各li上扫一遍后，就被扫过区域的图像会一直显示！！

这也说明监听事件其实是个很费计算量的事情，一旦事件发生得很快，浏览器对事件的监听就不太靠谱了。所以要解决上面的问题，就得给浏览器响应的时间，可以改成监听图片的`mouseout`触发车标的消失，但最好地，还是精简dom结构，不再在链接里嵌套图片，把图片内置成链接的背景，这样链接变大就不会有上面的烦恼了。

## 0x3鼠标移过，修改图片路径
实现效果：
<p data-height="257" data-theme-id="17410" data-slug-hash="yOOXNw" data-default-tab="result" data-user="levonlin" class="codepen">See the Pen <a href="http://codepen.io/levonlin/pen/yOOXNw/">02-11鼠标移过，修改图片路径</a> by levonlin (<a href="http://codepen.io/levonlin">@levonlin</a>) on <a href="http://codepen.io">CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>

和上一题基本一个套路，总之要监听`mouseover`这种进入子元素也会触发的事件，就一定得防止连续触发，否则就会和上一题一样出现背离预想的效果、而且性能上也不太好。防止连续触发的方法就是直接对最里层的子元素作用。还有像上一题也提到的，对于这种覆盖的事件还要保证元素的面积够大、让浏览器反应得过来。

还有一个点，就是加载动画的实现，这里用的是一个设置了半透明gif背景的div来做，直接覆盖在展示区上。在`mouseover`触发图片切换时显示这个div，直到要展示图片load完毕，再触发其消失即可。

## 0x4复选框全选、全不选、反选
实现效果：
<p data-height="257" data-theme-id="17410" data-slug-hash="XddLBm" data-default-tab="result" data-user="levonlin" class="codepen">See the Pen <a href="http://codepen.io/levonlin/pen/XddLBm/">2-12复选框全选、全不选、反选</a> by levonlin (<a href="http://codepen.io/levonlin">@levonlin</a>) on <a href="http://codepen.io">CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>

稍微把思路理一下，这题总共监听三个方面的点击事件：全选框、反选链接、以及全体的复选框的代理。具体到每个方面：全选框的点击事件处理全体的复选框的勾选及自身文本内容的改变（‘全选’、‘全不选’）这两件事、而反选链接在此基础上还要处理全选框的勾选；至于全体的复选框则处理全选框的勾选和全选框文本内容的改变这两件事。

这样一理就清晰了，每个监听中的动作也就在三个操作中选：全体的复选框的勾选、全选框的勾选和全选框文本内容的改变，而且这三件操作都得依赖一个“是否全部勾选”的布尔值进行，所以这里分别用三个函数实现这三个操作。最后，我们只要在事件处理程序中获得这个布尔值传给这几个函数就可以了，其中，全选框、反选链接可以从全选框的`checked`属性获得这个值，而全体的复选框要获得就得靠遍历了。

## 0x5操作 input 标签
实现效果：
<p data-height="257" data-theme-id="17410" data-slug-hash="zqBjWz" data-default-tab="result" data-user="levonlin" class="codepen">See the Pen <a href="http://codepen.io/levonlin/pen/zqBjWz/">2-13操作 input 标签</a> by levonlin (<a href="http://codepen.io/levonlin">@levonlin</a>) on <a href="http://codepen.io">CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>

搞不懂原题想实现什么，原来那个版本功能也没做完，于是我就自作主张改成实现表单获值和重置了。通过这个也练习也补了一下表单的知识，关于这方面的总结我写在《表单知识总结》这篇文章里了。

## 0x6操作表单元素 select
实现效果：
<p data-height="257" data-theme-id="17410" data-slug-hash="mPrWVX" data-default-tab="result" data-user="levonlin" class="codepen">See the Pen <a href="http://codepen.io/levonlin/pen/mPrWVX/">2-14操作表单元素 select</a> by levonlin (<a href="http://codepen.io/levonlin">@levonlin</a>) on <a href="http://codepen.io">CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>

还是表单控件的练习，和`input`一样，`select`也有专有的API给我们操纵，比单纯用DOM方法方便高效，借着这次练习把对选项的增删改查都练了一遍吧。增删利用`select`元素的`add`和`remove`方法实现，而要具体访问和修改某个选项就得用`options`属性获得选项集合再操作选项。此外，将`options`元素集的`length`置零还可以直接清空所有选项，但`options`貌似是个只读的属性，给它直接赋null却不能清空选项。

## 0x7简单的select级联列表
实现效果：
<p data-height="257" data-theme-id="17410" data-slug-hash="YqGZEa" data-default-tab="result" data-user="levonlin" class="codepen">See the Pen <a href="http://codepen.io/levonlin/pen/YqGZEa/">2-15简单的select级联列表</a> by levonlin (<a href="http://codepen.io/levonlin">@levonlin</a>) on <a href="http://codepen.io">CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>

监听州列表的`change`事件获取选中的州，再拿州名去获取城市名添加到城市列表即可。注意添加前要把城市列表先清空，防止数据堆积；同时注意选中项的可直接由`select`的`value`属性获得，不用再绕个大弯去获得选中项。

---第二课完 \ (^o^) /---

