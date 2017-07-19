title: 【译】HTML表单指南---第一个HTML表单
date: 2016-04-28 19:30:09
tags: [html,前端,翻译]
categories: 前端
---
# 前言
这个系列译自[mdn上的一份表单指南](https://developer.mozilla.org/zh-CN/docs/Web/Guide/HTML/Forms)，原文详尽阐述了表单相关的基础知识。而表单作为一个经典的页面交互方式，是每个前端工程师绕不开的话题，通过翻译这个系列的文章既是有助于扫清自己的知识盲区，也希望借此能让更多人受惠吧~

先列下目录：

* [第一个HTML表单](./#第一个HTML表单)
* [怎样构建HTML表单](http://levonlin.info/2016/05/03/%E3%80%90%E8%AF%91%E3%80%91%E6%80%8E%E4%B9%88%E6%A0%B7%E6%9E%84%E5%BB%BAHTML%E8%A1%A8%E5%8D%95/)
* [原生表单组件](http://levonlin.info/2016/05/24/%E3%80%90%E8%AF%91%E3%80%91%E5%8E%9F%E7%94%9F%E8%A1%A8%E5%8D%95%E7%BB%84%E4%BB%B6/)
* HTML表单的CSS
    - [HTML表单样式](http://levonlin.info/2016/06/05/%E3%80%90%E8%AF%91%E3%80%91HTML%E8%A1%A8%E5%8D%95%E6%A0%B7%E5%BC%8F/)
    - [HTML表单高级样式](http://levonlin.info/2016/06/29/%E3%80%90%E8%AF%91%E3%80%91HTML%E8%A1%A8%E5%8D%95%E9%AB%98%E7%BA%A7%E6%A0%B7%E5%BC%8F/)
    - [表单组件的属性兼容表](http://levonlin.info/2016/08/06/%E3%80%90%E8%AF%91%E3%80%91%E8%A1%A8%E5%8D%95%E7%BB%84%E4%BB%B6%E7%9A%84%E5%B1%9E%E6%80%A7%E5%85%BC%E5%AE%B9%E6%80%A7%E8%A1%A8/)
* [发送表单数据](http://levonlin.info/2016/09/24/%E3%80%90%E8%AF%91%E3%80%91%E5%8F%91%E9%80%81%E8%A1%A8%E5%8D%95%E6%95%B0%E6%8D%AE/)
* [表单数据校验](http://levonlin.info/2016/12/31/%E3%80%90%E8%AF%91%E3%80%91%E8%A1%A8%E5%8D%95%E6%95%B0%E6%8D%AE%E6%A0%A1%E9%AA%8C/)
* [怎样创建定制表单组件](http://levonlin.info/2017/05/31/%E3%80%90%E8%AF%91%E3%80%91%E6%80%8E%E6%A0%B7%E5%88%9B%E5%BB%BA%E5%AE%9A%E5%88%B6%E8%A1%A8%E5%8D%95%E7%BB%84%E4%BB%B6/)
* [通过JavaScript发送表单](http://levonlin.info/2017/06/21/%E3%80%90%E8%AF%91%E3%80%91%E9%80%9A%E8%BF%87JavaScript%E5%8F%91%E9%80%81%E8%A1%A8%E5%8D%95/)
    - [使用FormData对象](http://levonlin.info/2017/07/15/%E3%80%90%E8%AF%91%E3%80%91%E4%BD%BF%E7%94%A8FormData%E5%AF%B9%E8%B1%A1/)
* [遗留浏览器中的表单](http://levonlin.info/2017/07/16/%E3%80%90%E8%AF%91%E3%80%91%E9%81%97%E7%95%99%E6%B5%8F%E8%A7%88%E5%99%A8%E4%B8%AD%E7%9A%84%E8%A1%A8%E5%8D%95/)

> **声明**：本文源于mdn，遵循mdn的[相关协议](https://developer.mozilla.org/en-US/docs/MDN/About#Copyrights_and_licenses)，翻译过程可能会加入我个人的理解，转载请注明出处。

好，该说的都说完了，正文开始~

# 第一个HTML表单
>[原文](https://developer.mozilla.org/en-US/docs/Web/Guide/HTML/Forms/My_first_HTML_form)

这是一篇介绍HTML表单的文章，通过一个简单的contact表单，我们可以看到构建HTML表单的所有基本知识。本文假设你已掌握基本的HTML和CSS，但还不了解HTML表单。

## 开始之前
### 啥是HTML表单？
HTML表单是用户和网站或引用的一个主要交互点。表单允许用户发送数据给网站，大多数时候数据会发到服务器上，然而页面也可以把数据拦下来给自己用。

一个HTML表单由一或多个部件组成，这些部件可以是文本框（单行或多行）、选择框、按钮、复选框、或单选按钮。通常，部件们会伴随一个描述它们使用目的的label出现。

### 处理表单需要什么？
处理HTML时，你只需一个文本编辑器和一个浏览器即可。当然，你也也可以使用诸如[Visual Studio](http://www.microsoft.com/visualstudio)、 [Eclipse](http://www.eclipse.org/)，[Aptana](http://www.aptana.com/)之类的IDE。

此外，HTML表单和常规的HTML的主要区别在于表单收集的数据往往会被发送到服务器，这时你就需要搭个服务器来接受和处理这些数据了。至于如何搭建服务器，本文不作讨论，欲知详情，你可以看一篇这方面的文章：[发送表单数据](http://levonlin.info/2016/09/24/%E3%80%90%E8%AF%91%E3%80%91%E5%8F%91%E9%80%81%E8%A1%A8%E5%8D%95%E6%95%B0%E6%8D%AE/)。

## 设计你的表单
在开始撸代码前，最好先花点时间来构思下我们的表单。一个快速设计模型有助于你定义希望从用户那获得的数据集。从用户体验（UX）的观点来看，要知道你的表单越大，你越有可能失去用户。所以你应该保持简单和专注，只问你真想要的内容。要具体讨论表单的用户体验已超出本文的范围，给几篇这方面的文章把：

* Smashing Magazine有不少[和表单UX有关的好文章](http://uxdesign.smashingmagazine.com/tag/forms/)，但其中最重要的莫属[Extensive Guide To Web Form Usability](Extensive Guide To Web Form Usability)啦。
* UXMatters上的资源也蛮丰富的，从[基本的最佳实践](http://www.uxmatters.com/mt/archives/2012/05/7-basic-best-practices-for-buttons.php)到类似[多页表单](http://www.uxmatters.com/mt/archives/2010/03/pagination-in-web-forms-evaluating-the-effectiveness-of-web-forms.php)复杂内容都有。

在本文，我们将建立一个简单的contact表单，草图如下：

![](https://developer.mozilla.org/files/4579/form-sketch-low.jpg)

在我们的表单里，有三个文本框和一个按钮。文本框用来询问用户的名字、邮箱和他们想发送的消息，点击按钮则会把数据发给服务器。

## 尽情地写一波HTML吧
现在我们要开始编码了，建立起我们的contact表单，一共需要这几个的HTML元素： `<form>`, `<label>`, `<input>`, `<textarea>`, `<button>`。

### form元素：
所有的HTML表单都和下面代码一样以`<form>`元素开头：

```html
<form action="/my-handling-form-page" method="post">
</form>
```

`<form>`元素让我们定义了一个表单。虽然它也是个类似`<div>`或`<p>`一样的容器元素，但它还支持一些特有属性来定义表单行为。这些属性是可选的，而最佳实践是至少得设置`action`和`method`属性：

* `action`属性定义了表单收集的数据会被送往的地址（URL）
* `method`属性则定义了用什么http方式来发送数据（比如"get"或"post"方式）

若你还想知道这些属性是如何工作的，请阅读[发送表单数据](http://levonlin.info/2016/09/24/%E3%80%90%E8%AF%91%E3%80%91%E5%8F%91%E9%80%81%E8%A1%A8%E5%8D%95%E6%95%B0%E6%8D%AE/)。

### 添加label、input和textarea元素
我们简单的contact表单只有三个带label的文本框：用于姓名输入域的是个基本的单行文本框；用于邮箱的输入域也是单行文本框，不过它只能填入邮箱地址；最后用于发生消息的输入域则是个基本的多行文本框。

HTML代码如下：

```html
<form action="/my-handling-form-page" method="post">
    <div>
        <label for="name">Name:</label>
        <input type="text" id="name" />
    </div>
    <div>
        <label for="mail">E-mail:</label>
        <input type="email" id="mail" />
    </div>
    <div>
        <label for="msg">Message:</label>
        <textarea id="msg"></textarea>
    </div>
</form>
```

`<div>`元素在这里用于结构化我们的代码并方便我们定义样式。此外还要注意`<label>`元素`for`属性的使用，该属性是一种显式联系label和表单组件的方法，可以给它赋予想要关联组件的`id`值。这样一来，用户就可以通过点击label来激活表单组件了。如果你还想了解`for`
属性的其他好处，就参考这篇文章吧：[怎样构建HTML表单](http://levonlin.info/2016/05/03/%E3%80%90%E8%AF%91%E3%80%91%E6%80%8E%E4%B9%88%E6%A0%B7%E6%9E%84%E5%BB%BAHTML%E8%A1%A8%E5%8D%95/)

至于`<input>`元素，`type`属性是其最重要的属性，因为该属性定义了`input`元素的行为。欲知更多，请阅读[原生表单组件](http://levonlin.info/2016/05/24/%E3%80%90%E8%AF%91%E3%80%91%E5%8E%9F%E7%94%9F%E8%A1%A8%E5%8D%95%E7%BB%84%E4%BB%B6/)。

在我们的示例里，一个文本框中用了该属性的默认值`text`，该值表示一个基本的单行文本框，用于接收无控制或验证的任何文本。而另外一个文本框则使用了值`email`，表示定义一个只接收合法邮箱地址单行文本框，也就是说它会对用户输入的数据进行一些检查。我们会在[表单数据校验](http://levonlin.info/2016/12/31/%E3%80%90%E8%AF%91%E3%80%91%E8%A1%A8%E5%8D%95%E6%95%B0%E6%8D%AE%E6%A0%A1%E9%AA%8C/)这篇文章里讨论更多和表单验证有关的内容，

最后，我们来比较下`<input/>`和`<textarea></textarea>`间的语法差别。`<input/>`就是所谓的自动闭合元素，这种元素需要你加个"/"而不是结束标签来闭合（译注：这只是xhtml的强制要求，但在html里不用"/"也是可以的）；而`<textarea>`与之相反，并非自动闭合，这时你就得乖乖加上结束标签来闭合了。这点语法差别，其实决定了这两种元素在表单上设定默认值方法有所不同。

若要定义`<input>`元素的默认值，你得和下面的示例一样使用`value`属性：

```html
<input type="text" value="by default this element is filled with this text" />
```


至于`<textarea>`，你只需像下面一般，把默认值放进它的起止标签间即可：

```html
<textarea>by default this element is filled with this text</textarea>
```

### 再加个button来完成表单
我们的表单代码已经快准备好了，只差个让用户发送数据的按钮而已，再加个`<button>`元素就可以很简单地完成任务：

```html
<form action="/my-handling-form-page" method="post">
    <div>
        <label for="name">Name:</label>
        <input type="text" id="name" />
    </div>
    <div>
        <label for="mail">E-mail:</label>
        <input type="email" id="mail" />
    </div>
    <div>
        <label for="msg">Message:</label>
        <textarea id="msg"></textarea>
    </div>
    
    <div class="button">
        <button type="submit">Send your message</button>
    </div>
</form>
```

按钮共有三种类型：`submit`, `reset`, `button`。

* 点击`submit`型按钮，会按照我们在`<form>`元素上定义的`action`属性，把表单数据发给指定的页面。
* 点击`reset`型按钮，会直接重置所有表单组件为它们的默认值。从用户体验的角度来看，这其实是个不太好的操作。
* 点击`button`型按钮，什么都不会发生。。。听起来有点蠢，但其实这对于用javascript来定义自定义按钮是再好不过啦。

当然，你也可以用`<input>`元素来生成上面那几个按钮，和使用`<button>`相比，最大的区别就是`<input>`元素只允许使用纯文本作为它的内容，而`<button>`元素可以使用其他的HTML内容。（译注：比如图片，其实`<input>`元素通过css也能实现类似效果。此外还有个更大的差别，就是在旧版本IE中会出现`<input>`构造的按钮提交的是其`value`属性值，然而`<button>`按钮提交的不是`value`值而是元素的文本内容，不过考虑到要把按钮也设置`value`提交的情况实在少见，这个差别其实也没多大意义）

## 用CSS装扮漂亮点
HTML表单已经有了，在你的浏览器下看看吧，其实还是有点丑的：

![](https://developer.mozilla.org/files/4049/form-no-style.png)

下面我们给它加点CSS装点装点，先让form元素居中并带上点边框：

```css
form {
    /* 用来使表单相对页面居中 */
    margin: 0 auto;
    width: 400px;
    /* 设置表单外沿 */
    padding: 1em;
    border: 1px solid #CCC;
    border-radius: 1em;
}
```

接下来，给表单组件间加些间隔：

```css
form div + div {
    margin-top: 1em;
}
```

然后再来解决label们，最佳做法是设置所有label为统一大小并沿同一方向对齐。这里我们使用右对齐，但某些情况下使用左对齐也可以：

```css
label {
    /* 确保所有label大小相同并适当对齐 */
    display: inline-block;
    width: 90px;
    text-align: right;
}
```

其实HTML表单的组件样式还是蛮难整的，其中文本框还算简单、但其他组件就难说了，若想深入了解如何设置表单组件样式，可见[HTML表单样式](http://levonlin.info/2016/06/05/%E3%80%90%E8%AF%91%E3%80%91HTML%E8%A1%A8%E5%8D%95%E6%A0%B7%E5%BC%8F/)。

首先我们进行下一些通用配置：协调字体、大小和边框：

```css
input, textarea {
    /* 确保所有文本框有相同的字体设置
       默认设置一个等宽字体 */
    font: 1em sans-serif;

    /* 给所有文本框设置相同的大小 */
    width: 300px;
    -moz-box-sizing: border-box;
    box-sizing: border-box;

    /* 协调文本框边框的外形 */
    border: 1px solid #999;
}
```

HTML表单还支持不少伪类，用以表现各元素的状态。比如，我们可以在组件被激活时作突出显示，这样可以方便用户知道表单填到哪一步了：

```css
input:focus, textarea:focus {
    /* 让元素被激活时突出显示 */
    border-color: #000;
}
```

多行文本框还需要一些独有的自定义样式，`<textarea>`这个内联元素会默认让它的底部与别的文本的基线对齐，而我们往往并不想这样。想要完美对齐label和文本框，我们得把`<textarea>`的`vertical-align`属性设为`top`。

此外还得注意`resize`属性，它能方便用户缩放`<textarea>`的大小。

```css
textarea {
    /* 对齐多行文本框和他们的label */
    vertical-align: top;

    /* 给输入文本留下足够的空间 */
    height: 5em;

    /* 允许用户垂直缩放textarea但这并不会在所有浏览器上有效 */
    resize: vertical;
}
```

按钮也得加点特殊样式才行。为此，我们先将按钮置于一个`class`设为"button"的`<div>`中。因为我们还希望按钮能域其他表单组件对齐，所以我们得模仿出一个和其他`label`一样的效果（译注：在这里就是占同样大小），加上内外边距来实现吧。

```css
.button {
    /* 将按钮置于和文本框相同的位置 */
    padding-left: 90px; /* 和label元素一样大小 */
}
button {
    /* 这个额外的外边距大致充当了label和对应文本框间的间距 */
    margin-left: .5em;
}
```

现在我们的表单看起来漂亮多了：

![](https://developer.mozilla.org/files/4051/form-style.png)

## 将数据发给你的web服务器
如前所述，HTML表单就是为了方便从用户那获取数据并发送到服务端。所以最后、也是看起来最棘手的，就是在服务端处理这些表单数据了。

其实`action`和`method`属性已经给`<form>`元素定义了数据发送到哪儿、如何发生数据了。

然而这还不够，我们还得给我们的数据起个名字。这些名字在两端都是很重要的：在浏览器端，这能告诉浏览器如何给各部分的数据命名；而在服务端，这也让服务器能够依据数据名来处理每块数据。

要给你的数据命名，就得给那些需要收集特定数据的表单组件加上`name`属性：

```html
<form action="/my-handling-form-page" method="post">
    <div>
        <label for="name">Name:</label>
        <input type="text" id="name" name="user_name" />
    </div>
    <div>
        <label for="mail">E-mail:</label>
        <input type="email" id="mail" name="user_email" />
    </div>
    <div>
        <label for="msg">Message:</label>
        <textarea id="msg" name="user_message"></textarea>
    </div>
    
    <div class="button">
        <button type="submit">Send your message</button>
    </div>
</form>
```

在本例中，表单会发送三组数据，依次命名为user_name、user_email和user_message，他们会以HTTP POST的方式发送到URL"/my-handling-form-page"。

而在服务端，负责处理URL"/my-handling-form-page"的脚本会从HTTP请求中以3组键/值对的形式接收这些数据。至于脚本如何处理这些数据就取决于你的需求了，各种服务端语言（PHP, Python, Ruby, Java, C#等）有它们自有的机制来做这件事。要深入探究这方面就不是本文的主题了，欲知详情，我们会在[发送表单数据](http://levonlin.info/2016/09/24/%E3%80%90%E8%AF%91%E3%80%91%E5%8F%91%E9%80%81%E8%A1%A8%E5%8D%95%E6%95%B0%E6%8D%AE/)这篇文章中给你一些例子。

## 结论
恭喜！你终于完成你的第一个HTML表单啦~下面就是最终效果的在线演示：

[演示](https://mdn.mozillademos.org/en-US/docs/Web/Guide/HTML/Forms/My_first_HTML_form/Example$samples/A_simple_form?revision=1012447)

接下来是时候更上一层楼了，其实HTML表单远比我们在这里展现的强大，[本指南的其他文章](./#前言)将会帮助你掌握剩下的内容。
