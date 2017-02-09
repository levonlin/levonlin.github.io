title: 【译】HTML表单高级样式
date: 2016-06-29 15:44:25
tags: [html,前端,css,翻译]
categories: 前端
---
> * [系列文章说明](http://levonlin.info/2016/04/28/%E3%80%90%E8%AF%91%E3%80%91HTML%E8%A1%A8%E5%8D%95%E6%8C%87%E5%8D%97-%E7%AC%AC%E4%B8%80%E4%B8%AAHTML%E8%A1%A8%E5%8D%95/)
> * [原文](https://developer.mozilla.org/en-US/docs/Web/Guide/HTML/Forms/Advanced_styling_for_HTML_forms)

在本文中，我们将了解如何在[HTML](https://developer.mozilla.org/en-US/docs/CSS)表单上使用[CSS](https://developer.mozilla.org/en-US/docs/HTML)，为那些难于自定义的表单组件加以样式。如[前文](https://developer.mozilla.org/en-US/docs/HTML/Forms/Styling_HTML_forms)所述，文本框和按钮很适合使用CSS，而现在我们得来探索HTML表单样式的那些坑了。

在进一步讨论前，先回顾下两种HTML表单组件：

**比较糟糕的**
一些元素只能使用很少的样式，而且得依赖一些复杂的技巧，偶尔还得用到CSS3的高级知识。

**丑陋的**
别指望用CSS给这些元素添加样式了。在最好的情况，你还能写一点不能跨浏览器支持的代码，而且还不可能完全控制这些元素的样式。

## CSS的表现力
除了文本框和按钮，表单组件面临的最大问题，是CSS在多数情况下没有足够的表现力来恰当地给复杂组件添加样式。

近来HTML和CSS的演进已在拓展CSS的表现力：

* [CSS2.1](http://www.w3.org/TR/CSS21/selector.html#dynamic-pseudo-classes)很受限，只提供了三个伪类：
    - [:active](https://developer.mozilla.org/en-US/docs/Web/CSS/:active)
    - [:focus](https://developer.mozilla.org/en-US/docs/Web/CSS/:focus)
    - [:hover](https://developer.mozilla.org/en-US/docs/Web/CSS/:hover)
* [CSS Selector Level 3](http://www.w3.org/TR/css3-selectors/)新增了几个和HTML表单相关的伪类：
    - [:enabled](https://developer.mozilla.org/en-US/docs/Web/CSS/:enabled)
    - [:disabled](https://developer.mozilla.org/en-US/docs/Web/CSS/:disabled)
    - [:checked](https://developer.mozilla.org/en-US/docs/Web/CSS/:checked)
    - [:indeterminate](https://developer.mozilla.org/en-US/docs/Web/CSS/:indeterminate)
* [CSS Basic UI Level 3](http://dev.w3.org/csswg/css3-ui/#pseudo-classes)也添加了几个伪类用于描述组件状态：
    - [:default](https://developer.mozilla.org/en-US/docs/Web/CSS/:default)
    - [:valid](https://developer.mozilla.org/en-US/docs/Web/CSS/:valid)
    - [:invalid](https://developer.mozilla.org/en-US/docs/Web/CSS/:invalid)
    - [:in-range](https://developer.mozilla.org/en-US/docs/Web/CSS/:in-range)
    - [:out-of-range](https://developer.mozilla.org/en-US/docs/Web/CSS/:out-of-range)
    - [:required](https://developer.mozilla.org/en-US/docs/Web/CSS/:required)
    - [:optional](https://developer.mozilla.org/en-US/docs/Web/CSS/:optional)
    - [:read-only](https://developer.mozilla.org/en-US/docs/Web/CSS/:read-only)
    - [:read-write](https://developer.mozilla.org/en-US/docs/Web/CSS/:read-write)
* [CSS Selector Level 4](http://dev.w3.org/csswg/selectors4/)正处于开发状态，而且讨论的重点并不在于增加更多内容以改进表单：
    - [:user-error](https://developer.mozilla.org/en-US/docs/Web/CSS/:user-error)只是`:invalid`伪类的一个增强版、

虽然上述这些都是个好的开始，但其中仍有两个问题：第一，某些浏览器并不会实现CSS2.1之外的特性。第二，这些改进并未好到能处理诸如日期选择器之类的复杂组件。

浏览器厂商也为拓展CSS在表单上的表现力做了些试验，最好得了解下哪些可以使用。

>**警告：**虽然这些实验挺有趣的，但**它们并非标准、并不可靠**。若你要使用它们（通常你也不会这么做），你得自己担起风险，而且使用非标准属性也是[可能阻碍Web发展的做法](http://www.alistapart.com/articles/every-time-you-call-a-proprietary-feature-css3-a-kitten-dies/)。

* [Mozilla的CSS拓展](https://developer.mozilla.org/en-US/docs/CSS/CSS_Reference/Mozilla_Extensions)
    - [:-moz-placeholder](https://developer.mozilla.org/en-US/docs/Web/CSS/:-moz-placeholder)
    - [:-moz-submit-invalid](https://developer.mozilla.org/en-US/docs/Web/CSS/:-moz-submit-invalid)
    - [:-moz-ui-invalid](https://developer.mozilla.org/en-US/docs/Web/CSS/:-moz-ui-invalid)
    - [:-moz-ui-valid](https://developer.mozilla.org/en-US/docs/Web/CSS/:-moz-ui-valid)
* [Webkit的CSS拓展](https://developer.mozilla.org/en-US/docs/CSS/CSS_Reference/Webkit_Extensions)
    - [::-webkit-input-placeholder](https://developer.mozilla.org/en-US/docs/Web/CSS/::-webkit-input-placeholder)
    - [更多拓展](http://trac.webkit.org/wiki/Styling%20Form%20Controls)
* [Microsoft的CSS拓展](http://msdn.microsoft.com/en-us/library/ie/hh869403%28v=vs.85%29.aspx)
    - [:-ms-input-placeholder](http://msdn.microsoft.com/en-us/library/ie/hh772745%28v=vs.85%29.aspx)
* [Opera没有和HTML表单相关的拓展](http://www.opera.com/docs/specs/)

### 控制表单元素的外观
基于 WebKit- (Chrome, Safari) 和 Gecko- (Firefox) 的浏览器为HTML组件提供了最高等级的定制。这些定制也是跨平台的，所以浏览器需要一个机制来转换那些能被改变样式的表单组件的原生外观和体验。

于是它们使用了私有属性：[-webkit-appearance](https://developer.mozilla.org/en-US/docs/Web/CSS/-webkit-appearance) 或 [-moz-appearance](https://developer.mozilla.org/en-US/docs/Web/CSS/-moz-appearance)。**这些属性是非标准的，也不应被使用**。实际上，它们在Webkit和Gecko上的表现也不尽相同。但是，有一个值是应该知道的：`none`，使用该值你就可以获得对组件样式的（几乎所有）控制权。

所以，若你在一个元素上应用样式时遇到问题，可以试着使用这些私有属性（译注：用`none`值覆盖默认值）。下面我们会看到几个例子，但最为人熟知的用例是重置Webkit浏览器上搜索框的样式。

```html
<form>
    <input type="search">
</form>
```

```html
<style>
input[type=search] {
    border: 1px dotted #999;
    border-radius: 0;
    
    -webkit-appearance: none;
}
</style>
```

>**注意：**在我们讨论Web技术时，是难于预测未来的，但拓展CSS的表现力确实很难。另一些做了探索工作的标准如[Shadow DOM](http://dvcs.w3.org/hg/webcomponents/raw-file/tip/spec/shadow/index.html)提供了新的视角。我们对完全可配置样式的表单的追求还远未结束。

## 示例
### 多选框和单选框
给多选框和单选框添加样式是很让人凌乱的。例如，多选框和单选框的大小往往不会发生改变，而且不同浏览器的表现相当不同。

#### 一个简单例子
考虑如下示例：

```html
<span><input type="checkbox"></span>
```

```css
span {
    display: inline-block;
    background: red;
}

input[type=checkbox] {
    width : 100px;
    height: 100px;
}
```

不同浏览器的处理如下：

| 浏览器 | 渲染效果 |
| ---    | ---      |
| Firefox 16 (Mac OSX) | ![](https://developer.mozilla.org/files/4165/checkbox-firefox-macos.png)|
| Chrome 22 (Mac OSX) | ![](https://developer.mozilla.org/files/4163/checkbox-chrome-macos.png)|
| Opera 12.01 (Mac OSX) | ![](https://developer.mozilla.org/files/4167/checkbox-opera-macos.png)|
| Internet Explorer 9 (Windows 7) | ![](https://developer.mozilla.org/files/4169/checkbox-IE9-win7.png)|
| Internet Explorer 7 (Windows XP) | ![](https://developer.mozilla.org/files/4171/checkbox-IE7-winxp.png)|

#### 复杂点的例子
由于Opera和IE没有诸如`-webkit-appearance`和`-moz-appearance`之类的特性，所以使用这类特性是不太合适的。幸运的是，在这种情况下用CSS还能找出解决办法来。举一个常见的例子：

```html
<form>
  <fieldset>
    <p>
      <input type="checkbox" id="first" name="fruit-1" value="cherry">
      <label for="first">I like cherry</label>
    </p>
    <p>
      <input type="checkbox" id="second" name="fruit-2" value="banana" disabled>
      <label for="second">I can't like banana</label>
    </p>
    <p>
      <input type="checkbox" id="third" name="fruit-3" value="strawberry">
      <label for="third">I like strawberry</label>
    </p>
  </fieldset>
</form>
```

加一些基本样式：

```css
body {
  font: 1em sans-serif;
}

form {
  display: inline-block;

  padding: 0;
  margin : 0;
}

fieldset {
  border : 1px solid #CCC;
  border-radius: 5px;
  margin : 0;
  padding: 1em;
}

label {
  cursor : pointer;
}

p {
  margin : 0;
}

p+p {
  margin : .5em 0 0;
}
```

现在，我们来加样式以获得一个定制的复选框。

我们的计划是用我们自己的图像来替换原生的复选框。首先得准备一张具有所有复选框所需状态的图像，这些状态有：未勾选、已勾选、禁用未勾选、禁用已勾选。该图像可用CSS雪碧图来做：

![](https://developer.mozilla.org/files/4173/checkbox-sprite.png)

先从隐藏原生的复选框开始，我们只是简单地把它们从页面的可视范围中挪出。这里有两个要重点考虑的事：

* 别使用`display:none`来隐藏复选框，因为如前面提到的，我们需要保证复选框对用户可用。使用`display:none`的话，复选框就不再是用户可访问的，即不能再勾选或者不勾选它。
* 我们将使用一些CSS3选择器来实现我们的样式。为支持老旧浏览器，可以在我们要用的选择器前加[:root](https://developer.mozilla.org/en-US/docs/Web/CSS/:root)伪类。在已有的实现中，支持我们需要的选择器的浏览器也支持`:root`伪类，而剩下的浏览器就不支持了。所以这是一种用来识别老旧浏览器的方便做法，老旧浏览器中将会看到普通的复选框、而现代浏览器中将会看到定制的复选框。

```css
:root input[type=checkbox] {
  /* 原生的复选框会从页面的可视范围中被挪出 */
  position: absolute;
  left: -1000em;
}
```

现在我们已经移除了原生的复选框，可以添加我们自己的了，这里会在原生复选框后面的[`<label>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/label)元素使用[:before](https://developer.mozilla.org/en-US/docs/Web/CSS/:before)伪元素。下面的选择器中，我们先用[属性选择器](https://developer.mozilla.org/en-US/docs/CSS/Attribute_selectors)来获取复选框；然后使用[相邻兄弟选择器](https://developer.mozilla.org/en-US/docs/CSS/Adjacent_sibling_selectors)来获取原来复选框后的`label`。最后我们通过给`:before`伪元素添加样式，用其来显示我们定制的复选框。

```css
:root input[type=checkbox] + label:before {
  content: "";
  display: inline-block;
  width  : 16px;
  height : 16px;
  margin : 0 .5em 0 0;
  background: url("https://developer.mozilla.org/files/4173/checkbox-sprite.png") no-repeat 0 0;

/* 下一属性用于在文本基线调整复选框的位置 */

  vertical-align: bottom;
  position: relative;
  bottom: 2px;
}
```

接下来用原来复选框的[:checked](https://developer.mozilla.org/en-US/docs/Web/CSS/:checked)和[:disabled](https://developer.mozilla.org/en-US/docs/Web/CSS/:disabled)伪类来改变我们定制的复选框的状态。由于我们使用了CSS雪碧图，我们只需要调整背景的位置而已。

```css
:root input[type=checkbox]:checked + label:before {
  background-position: 0 -16px;
}

:root input[type=checkbox]:disabled + label:before {
  background-position: 0 -32px;
}

:root input[type=checkbox]:checked:disabled + label:before {
  background-position: 0 -48px;
}
```

最后也是很重要的一步：当用户使用键盘在不同表单组件间浏览时，每个组件应该能看到聚焦的效果。由于我们隐藏了原生的复选框，所以只能自己实现这一特性来让用户知晓他们正处于何处。下列的CSS实现了对我们的定制复选框的聚焦：

```css
:root input[type=checkbox]:focus + label:before {
  outline: 1px dotted black;
}
```

最终效果如下：

<p><iframe height="130" src="https://mdn.mozillademos.org/en-US/docs/Web/Guide/HTML/Forms/Advanced_styling_for_HTML_forms$samples/A_more_complex_example?revision=784065" width="250"></iframe></p>

### 处理选择框噩梦
[`<select>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/select)元素被认为是一个“丑陋的”组件，因为不太可能给它添加跨平台的样式。当然，还是有一些可以探讨的东西的，这里就不长篇大论了，先看个例子：

```html
<select>
  <option>Cherry</option>
  <option>Banana</option>
  <option>Strawberry</option>
</select>
```

```css
select {
  width   : 80px;
  padding : 10px;
}

option {
  padding : 5px;
  color   : red;
}
```

后面的表格展示了不同浏览器如何在两种情况下处理这一样式。（渲染效果中）前两列只是简单的例子，后两列则使用了些定制的CSS来获得对组件外观的更多控制，如下所示：

```css
select, option {
  -webkit-appearance : none; /* 获得对Webkit浏览器里外观的控制 */
  -moz-appearance : none; /* 获得对Gecko浏览器里外观的控制 */

  /* 获得对Presto (Opera) 和 Trident (IE)浏览器里外观的控制
     注意这也能在Gecko浏览器里起作用，且对Webkit浏览器有副作用 */  
  background : none;
}
```

| 浏览器 | 普通渲染（关闭） | 普通渲染（打开） | 调整后渲染（关闭） | 调整后渲染（打开） |
| --- | --- | --- | --- | --- |
| Firefox 16 (Mac OSX) | ![](https://developer.mozilla.org/files/4201/select-firefox-macos.png) | ![](https://developer.mozilla.org/files/4199/select-firefox-macos-open.png) | ![](https://developer.mozilla.org/files/4197/select-firefox-macos-custom.png) | ![](https://developer.mozilla.org/files/4195/select-firefox-macos-custom-open.png) |
| Firefox 16 (Windows 7) | ![](https://developer.mozilla.org/files/4209/select-firefox-win7.png) | ![](https://developer.mozilla.org/files/4207/select-firefox-win7-open.png) | ![](https://developer.mozilla.org/files/4205/select-firefox-win7-custom.png) | ![](https://developer.mozilla.org/files/4203/select-firefox-win7-custom-open.png) |
| Chrome 22 (Mac OSX) | ![](https://developer.mozilla.org/files/4183/select-chrome-macos.png) | ![](https://developer.mozilla.org/files/4181/select-chrome-macos-open.png) | ![](https://developer.mozilla.org/files/4179/select-chrome-macos-custom.png) | ![](https://developer.mozilla.org/files/4177/select-chrome-macos-custom-open.png) |
| Chrome 22 (Windows 7) | ![](https://developer.mozilla.org/files/4191/select-chrome-win7.png) | ![](https://developer.mozilla.org/files/4189/select-chrome-win7-open.png) | ![](https://developer.mozilla.org/files/4187/select-chrome-win7-custom.png) | ![](https://developer.mozilla.org/files/4185/select-chrome-win7-custom-open.png) |
| Opera 12.01 (Mac OSX) | ![](https://developer.mozilla.org/files/4225/select-opera-macos.png) | ![](https://developer.mozilla.org/files/4223/select-opera-macos-open.png) | ![](https://developer.mozilla.org/files/4221/select-opera-macos-custom.png) | ![](https://developer.mozilla.org/files/4219/select-opera-macos-custom-open.png) |
| Internet Explorer 9 (Windows 7)    | ![](https://developer.mozilla.org/files/4217/select-IE9-win7.png) | ![](https://developer.mozilla.org/files/4215/select-IE9-win7-open.png) | N/A | N/A |
| Internet Explorer 7 (Windows XP) | ![](https://developer.mozilla.org/files/4213/select-IE7-winxp.png) | ![](https://developer.mozilla.org/files/4211/select-IE7-winxp-open.png) | N/A | N/A |

如你所见，即使使用了 `-*-qppearance` 属性，仍会有问题存在：

* [padding](https://developer.mozilla.org/en-US/docs/Web/CSS/padding)属性在不同操作系统和浏览器中的处理是不一致的。
* 老旧的IE不支持平滑的样式。
* 火狐没有能给予下拉箭头样式的方式。
* 若想要给下拉菜单中的[`<option>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/option)元素以样式，则Chrome和Opera在不同系统下的表现不尽相同。

同时，在本例中，我们只讨论了三个CSS属性；想想要考虑更多的CSS属性会有多么混乱。可见，CSS确实不太适合用来改变这些组件的外观和体验，但它仍让你能做些调整，如果你愿意忍受不同浏览器或不同操作系统上的不同的话。

我们将在下篇文章：[表单组件的属性兼容表]()中尝试指出哪些属性是可用的。

## 通向漂亮表单之路：一些有用的库和拓展工具
尽管CSS在复选框和单选框上的表现力已经够用了，但离其支持高级表单组件仍然遥遥无期。即使在`<select>`元素上有一些可能，但文件组件、日期选择器等仍不能被添加样式。

若你想获得对表单组件的完整控制权，你就得依赖Javascript，别无选择。在[怎样创建定制表单组件]()一文中，我们将了解如何自己实现它，而如今有一些很有用的库可以帮到你：

* [Uni-form](http://sprawsm.com/uni-form/)是一个规范了表单格式和使用CSS给予表单样式的框架。在和jQuery一起使用时，它也提供了些额外的可选特性。
* [Formalize](http://formalize.me/)是一些常见Javascript框架（如jQuery、Dojo、YUI等）的一个插件，用于规范化和定制表单。
* [Niceforms](http://www.emblematiq.com/lab/niceforms/)是个提供了完整web表单定制的独立Javascript方法。你可以使用一些内建的主题、也可自己创建。

下面几个库则不止用于处理表单，但它们在处理HTML表单时有很多有趣的特性：

* [jQuery UI](jQuery UI )提供了些非常有趣的可定制高级组件，比如日期选择器（特别关注了无障碍访问）。
* [Twitter Bootstrap](http://twitter.github.com/bootstrap/base-css.html#forms)非常有用，如果你想规范化你的表单的话。
* [WebShim](http://afarkas.github.com/webshim/demos/demos/webforms.html)是一个庞大的工具，用于处理那些支持HTML5的浏览器。其web表单部分挺有用的。

要知道，绑定CSS和Javascript会引起副作用。所以若你选择了上述的一种库，就得时常保证在脚本失效时会可回退的样式表。造成脚本失效的原因很多，特别在移动端，故你的Web站点或app设计得能最好地处理这些情况。

## 结论
当在HTML表单上使用CSS仍存在许多坑时，有很多方法可以绕过这些坑。本来是没有确切、通用的解决方案的，但现代浏览器带来了新可能。而现在，最佳方案是研究不同浏览器对用在HTML表单组件的CSS的支持程度。

下篇文章，我们将探索各种HTML表单组件对那些最重要的CSS属性的支持程度：[表单组件的属性兼容表]()。

## 参见

* [Dive into HTML5: Forms](http://diveintohtml5.info/forms.html)
* [Useful ideas and guidelines for good web form design](http://www.smashingmagazine.com/2011/06/27/useful-ideas-and-guidelines-for-good-web-form-design/)
