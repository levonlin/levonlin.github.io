title: 原生js练习题---第六课
date: 2016-04-27 12:55:15
tags: [javascript,前端]
categories: 前端
---
## 0x1完美拖拽
实现效果：
<p data-height="300" data-theme-id="17410" data-slug-hash="GZYNGW" data-default-tab="result" data-user="levonlin" data-embed-version="2" class="codepen">See the Pen <a href="http://codepen.io/levonlin/pen/GZYNGW/">6-01完美拖动</a> by levonlin (<a href="http://codepen.io/levonlin">@levonlin</a>) on <a href="http://codepen.io">CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>

这里没有使用h5的拖动，毕竟原题也是考察借助鼠标事件实现自定义的拖动，所以就借鉴了《js高级程序设计》里的自定义拖动自己封装了个拖动api，当然由于做这个系列题目使用的都是es5的语法，所以IE8往下就兼容不到了（有兴趣的可以自己一试）。

首先有一个自定义的事件对象构造函数`EventTarget`，内部封装了事件的监听、触发、移除监听三个基本操作。再由`EventTarget`拓展一个和拖动操作有关的单例对象，这个对象是的主要任务就是把指定元素上的触发的鼠标事件转换为触发我们自定义的拖动事件，该对象接口方法介绍如下：

* `enable()`：开启整个文档上拖动事件与鼠标事件的关联
* `disable()`：关闭拖动事件与鼠标事件的关联，也就是点击时不再触发自定义的拖动操作
* `addHandler(eventName, handler)`：给相应拖动事件添加处理函数。*注意由于拖动事件是全局关联，所以若处理函数是针对指定元素的操作，不仅要先在该元素上添加`class="draggable"`，而且在处理函数里还要判断触发事件的就是该元素才行*
* `removeHandler(eventName, handler)`：移除指定的拖动事件上的某个处理函数

再介绍下三个自定义的拖动事件：

* `dragstart`：由`mousedown`转换而来，事件对象的属性有：
    - type: 'dragstart'
    - target: 鼠标点击的draggable元素
    - clientX, clientY: 同`mousedown`
* `drag`：由`mousemove`转换而来，事件对象的属性有：
    - type: 'drag'
    - target: 鼠标点击的draggable元素
    - clientX, clientY: 同`mousemove`
    - diffX, diffY: 用于校正`clientX`, `clientY`与实际要达到的拖动位置的偏差。需在`dragstart`做指定，否则默认值为0
* `dragend`：由`mouseup`转换而来，事件对象的属性有：
    - type: 'dragend'
    - target: 鼠标点击的draggable元素
    - clientX, clientY: 同`mouseup`

api有了，我们就可以调用`enable`开启拖动，再和原生的事件一样给题目中的拖动块添加三个事件处理函数即可。只是要注意由于`addHandler`方法不能指定元素，所以还得在拖动块上添加`class="draggable"`、并在处理函数里做个判断才行。利器在手，接下来要做的限制移动范围、展示坐标、回放拖动等都是小case啦，这题的代码就当是这套api的示例了。

## 0x2仿腾讯微博效果
实现效果:
<p data-height="300" data-theme-id="17410" data-slug-hash="KzEPQM" data-default-tab="result" data-user="levonlin" data-embed-version="2" class="codepen">See the Pen <a href="http://codepen.io/levonlin/pen/KzEPQM/">6-02仿腾讯微博效果</a> by levonlin (<a href="http://codepen.io/levonlin">@levonlin</a>) on <a href="http://codepen.io">CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>

这次来个大制作，只要再搭个后台弄点数据交互，几乎就是一个完整的webApp了。就页面而言，主要就是实现表单获取数据和数据列表的展现，所以这里分别封装了两个单例`MsgForm`和`MsgList`对应表单和展示列表，来实现相关的功能。

`MsgForm`只提供了`init`方法来添加各个表单组件的事件监听。该部件最重要的就是对表单的验证了，既要有提交时的验证，也要保证能在输入时即时给出字数提醒。正则用的溜的话其实也不难。

至于`MsgList`则主要实现增删DOM的操作，难点就是呈现和隐藏两个动画的实现。这里利用`setTimeout`手动调节出了效果，写得还是比较ugly的，拓展性还不强。

最后注意几个语法上的问题，一是键盘事件也是可以使用辅助按键属性：`ctrlKey`, `altKey`, `shiftKey`, `metaKey`，而不用像第五课做过的一题那样去缓存按键了。第二就是用js获取的css属性值，不论是通过`element.style`还是`window.getComputedStyle()`，返回的都是字符串，所以要进行运算只能手动转为数字；这一点和js中获取宽高、位置的属性如`offsetWidth`等直接返回数字不同。

## 0x3自定义多级右键菜单
实现效果：
<p data-height="300" data-theme-id="17410" data-slug-hash="eZoZqR" data-default-tab="result" data-user="levonlin" data-embed-version="2" class="codepen">See the Pen <a href="http://codepen.io/levonlin/pen/eZoZqR/">6-03自定义多级右键菜单</a> by levonlin (<a href="http://codepen.io/levonlin">@levonlin</a>) on <a href="http://codepen.io">CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>

第五课第六题中已经通过`contextmenu`事件实现了一级右键菜单，所以这题只要在上面再添加事件唤出子菜单即可。

首先解决第五课第六题题在实现时留下的一个bug：菜单有时会显示到容器外。原因很简单，由于是先定位菜单再显示菜单，导致要依靠宽高的定位并不能起作用；所以这里保证菜单先显现出来拥有宽高即可。

然后就是实现多级子菜单了，这里直接使用css的`hover`伪类控制显隐，只把定位交给`mouseover`事件来做即可。因为如果使用`mouseover`和`mouseout`控制显隐，由于冒泡的特性会导致移到子菜单前父菜单就已经消失的问题，虽然前面做过的题目有用过`setTimeout`延迟消失来解决这一问题的，但这样写起来实在太丑陋了，不考虑兼容老旧浏览器情况下还是用css做更清晰高效。

至于如何定位，也是沿用第五课的思路：设置各级子菜单绝对定位；显示菜单时先计算容器右侧和底部的剩余空间；再分别判断能否容得下子菜单的宽高，若容得下则子菜单出现在父元素右下角，否则就做出相应的变化。

最后再总结遇到的几个问题：
* 子菜单定位时遇到的问题：js的`offsetTop`, `offsetLeft`属性获得的都是相对父容器的位置而不是绝对位置，要获取绝对位置必须进行向上遍历。
* 子菜单`li`元素的宽度不能容得下其文本内容，导致文本折叠：不设置宽度的绝对定位元素，其宽度是由**最长的文本内容**决定的。若文本自动换行那最长文本就是最长单词而已，所以得设置`white-space: nowrap;`使文本不换行，才能保证`li`元素的宽度能够容得下其所有文本内容。而具体关于元素宽高为`auto`时其宽高在不同情况会受到什么影响，我会专门写一篇文章来分析分析~

---第六课完---
