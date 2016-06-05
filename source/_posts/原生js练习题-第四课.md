title: 原生js练习题---第四课
date: 2016-03-24 15:50:26
tags: [javascript,前端]
categories: 前端
---
## 0x1setTimeout应用
实现效果：
<p data-height="257" data-theme-id="17410" data-slug-hash="yOoPNd" data-default-tab="result" data-user="levonlin" class="codepen">See the Pen <a href="http://codepen.io/levonlin/pen/yOoPNd/">4-01setTimeout应用</a> by levonlin (<a href="http://codepen.io/levonlin">@levonlin</a>) on <a href="http://codepen.io">CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>

又见导航条，先看下css，这里用的是雪碧图背景做出圆角的效果，虽然是经典的方法、兼容性好，但这种代码写起来实在是有点丑陋。因为需要三层嵌套，分别用背景图绘制左圆角、右圆角和中间背景，导致dom结构变复杂了、更进一步影响js的代码简洁。

而关于ui的动效，用的正是这一系列题目里经常出现的“hover父元素激活子元素”的方法来实现，于是自然又得用`mouseover`和`mouseout`这对兄弟了。但是我还是被这题坑了不少时间，因为有两个两点关键的问题想了好久才解决好：

首先得明白这题和前面题目的不同，前面那些题目其实都可以用css的`hover`伪类来实现，但这题不行，因为子选单和导航条的主体间是有**间隙**的，在鼠标从导航条到子选单的路上，子选单就会消失，根本到达不了。。。要解决这个bug，就需要题目提到的`setTimeout`登场了，离开选单时，我们可以让它延迟消失、给用户的鼠标到达子选单或重回导航条的时间；等再回到导航条或子选单就取消掉这个延时即可。

紧接着第二个大坑出现了，在前面的总结里也提到怎么用好`mouseover`和`mouseout`，由于这两个事件会冒泡，同时由于他们只在跨界时触发，而且一旦元素占地过小就会被浏览器忽略；为防止冒上来的事件乱成一团、或者该触发的没触发，就应该做到减少元素嵌套，直接把事件监听绑定在具体元素上。

然而问题是，这个法则在导航条的主体是可行的、但是子选单因为前面提到的三层嵌套构造圆角，已经无法减少嵌套了，同时还得考虑到子选单也是嵌套在导航条里的啊。。。这样一来某些元素除去子元素的范围、占地就很小了，会出现前面提到的该触发却没触发的问题。具体在这题里的表现，就是当你的鼠标离开子选单时，由于子选单被子元素挤占得只剩一点地方了（具体可以按f12看一下），根本不能靠它触发`mouseout`了，若要解决这个问题，就不得不接收里面子元素冒泡才会产生`mouseout`事件，有了事件才能进行元素显隐的控制嘛。

但这又导致前面提到的另一个问题：举例来说，鼠标离开子选单时，只要鼠标一扫冒泡上来的`mouseout`、`mouseover`事件就一大堆，而这时只有鼠标离开子选单那个`mouseout`才是应该奏效的。既然我们不能阻止和筛选子元素上的`mouseout`事件，那看来只有让它和`mouseover`的效果相抵消了，因为只有在导航条里才会触发这对事件，离开导航条后就没有`mouseover`来和`mouseout`抵消了。同理，反过来进入子选单时自然就用`mouseout`来抵消`mouseover`达到筛选的目的。

于是就能总结出第二个坑的解决方法了：
1. 首先要利用子元素的冒泡，这时就不能使用事件代理了（事件代理函数本身就有筛选事件来源的功能，没法获取子元素冒泡上来的事件），只能退而求其次，用一个循环来绑定事件吧。
2. 通过`mouseout`和相应的`mouseover`事件进行抵消，达到变相筛选出事件的效果。但要注意，一般用这种抵消都会引起闪烁的，所以抵消并不是个通用的解决方法，只是因为正好由于这题使用了延时和解除延时，显隐的结果不会立刻显现，天然就防止了子选单闪个不停的结果出现。

剩下的代码里还要解决几个小问题：如解决使用了延时带来的子选单切换卡顿、依照剩余可占宽度决定子选单该往左还是往右定位、同时让箭头自动保持在中间等等；相比上面两个坑都算简单的啦。

写到这里才把解题思路捋清了，可见这看似简单的几行js要写出来得有多蛋疼，这里真得吐槽下这个ui的问题：把子选单搞成细细的横向条子其实还好，采用hover作选择本来也ok，但这时你还专门让子选单和导航条主体间有间隙，导致实现起来比较难之外，用户也很容易误操作啊，那么点地方放那么多`mouseout`和相应的`mouseover`事件的触发点，手一抖、鼠标一滑很容易就选到别的地方去啊！！

## 0x2自动播放一幻灯片效果
实现效果：
<p data-height="257" data-theme-id="17410" data-slug-hash="VazJOd" data-default-tab="result" data-user="levonlin" class="codepen">See the Pen <a href="http://codepen.io/levonlin/pen/VazJOd/">4-02自动播放一幻灯片效果</a> by levonlin (<a href="http://codepen.io/levonlin">@levonlin</a>) on <a href="http://codepen.io">CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>

这次是做一个轮播图，不同于那种滑动的轮播效果，这里用的是只是将图片叠在一起然后改变透明度而已（我还另外在展示的图片上加了个`z-index`，防止拖一下图片就现形）。选择控件上，用的仍然是这一系列题目喜闻乐见的“hover一个元素激活其他元素”，只不过这题因dom结构简单，实现起来可比上一题简单多了。

主要逻辑做起来也就两个方面，通过监听控件来增删类名以改变样式、再加上自动定时播放就可以，这里我把播放和停止都封装在一个单例里了（注意是定义在IIFE里），结构更清晰一点，也减少了全局变量。

但还有一个问题，那就是淡入效果的实现，思路自然是定时修改透明度，然而由于一开始给图片直接添加类名已经让它的透明度为1，导致图片展示时是不透明-->透明-->不透明的过程，效果很不自然，于是干脆就直接把修改类名展示图片的代码去了，直接用淡入展示，效果更好同时又精简了代码。

最后再提醒下自己，用`RegExp`构造正则时，千万别忘记`\`的转义！

## 0x3自动改变方向一幻灯片效果
实现效果：
<p data-height="257" data-theme-id="17410" data-slug-hash="jqGVra" data-default-tab="result" data-user="levonlin" class="codepen">See the Pen <a href="http://codepen.io/levonlin/pen/jqGVra/">4-03自动改变方向一幻灯片效果</a> by levonlin (<a href="http://codepen.io/levonlin">@levonlin</a>) on <a href="http://codepen.io">CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>

在上面一题多加个方向判断函数而已，同时这里我把和轮播有关的东西全都封装进了`autoSlider`这个单例里去了，全局变量现在就只剩这个单例，接口也可以写得简洁到只有一个初始化调用，真是清爽啊~

## 0x4agruments应用一求出函数参数的总和
简单体验`arguments`而已，直接贴代码：

```javascript
function sum() {
    var result = 0;
    for (var i = 0; i < arguments.length; i++) {
        result += arguments[i];
    }
    return result;
}

console.log(sum(1, 3, 5, 7));
```

## 0x5css函数一设置/读取对象的属性
实现效果：
<p data-height="257" data-theme-id="17410" data-slug-hash="ONOrYL" data-default-tab="result" data-user="levonlin" class="codepen">See the Pen <a href="http://codepen.io/levonlin/pen/ONOrYL/">4-05css函数一设置、读取对象的属性</a> by levonlin (<a href="http://codepen.io/levonlin">@levonlin</a>) on <a href="http://codepen.io">CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>

简单的样式获取和修改，虽然增删类名方式就可以轻松实现，但其实这题要的不只是效果，而是要模拟jquery的`css`函数。由于设定上，该函数的参数应该有三种情况：

* 接受一个css属性名读取值
* 接收一个对象（hash表）批量设置css属性
* 传入键、值两个参数直接设置某个css属性

所以要按参数个数和参数的类型分别进行操作，类型判断最好使用`Object.prototype.toString`，这样不会出现使用`typeof`时模棱两可的结果。如果遇到不合法的参数数目和类型，就直接用`throw`抛出错误。

至于样式的获取，使用了标准的`window.getComputedStyle`方法，这个方法的兼容性其实也够用了（IE8+），所以完全可以不用老旧的`currentStyle`。

而在设置样式属性时，要明白`.`和`[]`操作符的不同，使用点操作符号的属性名必须首先是合法的变量名，而且不能是动态的引用（其实就是字符串了，只是引号可以省略罢了）；中括号操作符就不存在这种问题，所以`elelment.sytle['background-color']`这种用法也能跑。

## 0x6当前输入框高亮显示
实现效果：
<p data-height="257" data-theme-id="17410" data-slug-hash="WwdxJK" data-default-tab="result" data-user="levonlin" class="codepen">See the Pen <a href="http://codepen.io/levonlin/pen/WwdxJK/">4-06当前输入框高亮显示</a> by levonlin (<a href="http://codepen.io/levonlin">@levonlin</a>) on <a href="http://codepen.io">CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>

继续事件模拟css伪类的行为，这次终于不是老冤家`hover`了，改成了文本框`focus`，很自然就想到老套路：事件代理+增删类名。但要注意焦点事件也有一对冒泡和不冒泡的事件存在，其中由于火狐不支持冒泡的`focusin`和`focusout`，只支持不冒泡的`focus`和`blur`，除非只考虑移动端，否则依赖冒泡的事件代理是不能用在处理焦点事件上滴。所以这里只好用一个循环来完成对所有元素的监听了。

至于修改的样式，其实就相当于自定义文本框背景和`outline`样式了，原题是用了背景图来做，但这完全可以用css模拟嘛。然而由于这里输入框是圆角的，除了火狐外[并没有浏览器](http://www.zhangxinxu.com/wordpress/2015/04/css3-radius-outline/)实现`outline`的圆角，所以不能直接修改`outline`样式了，只能假借边框来实现该效果。同时还得记得把浏览器默认`outline`关掉，否则效果可就大打折扣。

## 0x7数组练习：各种数组方法的使用
实现效果：
<p data-height="257" data-theme-id="17410" data-slug-hash="JXpdpB" data-default-tab="result" data-user="levonlin" class="codepen">See the Pen <a href="http://codepen.io/levonlin/pen/JXpdpB/">4-07数组练习：各种数组方法的使用</a> by levonlin (<a href="http://codepen.io/levonlin">@levonlin</a>) on <a href="http://codepen.io">CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>

简单的数组练习，就不按照原题那样分几个数组了，这里直接在一个数组上操作，再搭配一个简单的处理流程：先用一个事件代理来监听和分发按钮的点击事件，处理完再输出到dom即可。

具体的数组操作是比较简单的，不过要注意`concat`和`slice`这两个方法与众不同，它们不是直接在调用数组上操作，而是另外生成新数组进行操作，返回的也是新数组。利用这一点可以实现数组的深拷贝，但若是想作用到原数组就得另外赋值了。

## 0x8事件练习：封装兼容性添加、删除事件的函数
实现效果：
<p data-height="257" data-theme-id="17410" data-slug-hash="eZVvyN" data-default-tab="result" data-user="levonlin" class="codepen">See the Pen <a href="http://codepen.io/levonlin/pen/eZVvyN/">4-08事件练习：封装兼容性添加、删除事件的函数</a> by levonlin (<a href="http://codepen.io/levonlin">@levonlin</a>) on <a href="http://codepen.io">CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>

虽然做这系列的题本不想考虑老旧浏览器，但兼容事件毕竟是个经典问题，权当回顾下js高级程序设计里的内容了。采用的的也是书里的兼容方法，能用DOM2级别`addEventListener`和`removeEventListener`就用，再者就是兼容`IE8`以下的`attachEvent`和`detachEvent`（有this指向`window`、反向执行的坑），这两者都能绑定多个事件处理程序；最后实在不行再用只能绑定一个处理程序、但兼容性最佳的DOM0级。

## 0x9星级评分系统
实现效果：
<p data-height="257" data-theme-id="17410" data-slug-hash="PNQywV" data-default-tab="result" data-user="levonlin" class="codepen">See the Pen <a href="http://codepen.io/levonlin/pen/PNQywV/">4-09星级评分系统</a> by levonlin (<a href="http://codepen.io/levonlin">@levonlin</a>) on <a href="http://codepen.io">CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>

星星只是使用背景图来改变亮灭而已，所以只要在容器上挂上对星星的`click`、`mouseover`、`mouseout`三个事件代理，剩下的就是一些DOM和数据操作了。

---第四课完---
