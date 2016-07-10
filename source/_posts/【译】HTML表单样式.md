title: 【译】HTML表单样式
date: 2016-06-05 14:13:20
tags: [html,前端,css]
categories: [前端,翻译]
---
> * [系列文章说明](http://levonlin.info/2016/04/28/%E3%80%90%E8%AF%91%E3%80%91HTML%E8%A1%A8%E5%8D%95%E6%8C%87%E5%8D%97-%E7%AC%AC%E4%B8%80%E4%B8%AAHTML%E8%A1%A8%E5%8D%95/)
> * [原文](https://developer.mozilla.org/en-US/docs/Web/Guide/HTML/Forms/Styling_HTML_forms)

本文中，我们将学习如何使用[CSS](https://developer.mozilla.org/en-US/docs/CSS)来让[HTML](https://developer.mozilla.org/en-US/docs/HTML)表单看起来更漂亮，这可能需要窍门才能做到。由于历史及技术上的原因，表单组件并不太适合使用CSS；而也正因为有这些困难，许多的开发者会选择[创建定制表单组件]()来获得对外观和体验的控制。然而，在现代浏览器中，网页设计师可以拥有更多对表单元素的控制权了。让我们来深入了解下吧。

## 为何难以使用CSS给表单组件添加样式？
在web发展的早期，大约1995年，表单控件就已经在[the HTML 2 specification](http://www.ietf.org/rfc/rfc1866.txt)中给添加到HTML了。由于表单组件的复杂性，浏览器开发商们就选择了依靠操作系统来管理和渲染它们。

几年之后，CSS诞生了，这就在技术上使得用原生组件来实现表单的做法也有了样式需求。然而在CSS的早期，给表单控件添加样式并未被优先考虑。

由于用户们习惯了在交互平台上的视觉体验，浏览器开发商不得不让表单控件可以被添加样式；而说实话，在今天也依然难以重构所有表单控件让它们可被样式化。

即使到了现在，也依然没有一个单独的浏览器实现了所有CSS 2.1规范。然而随着时间推移，浏览器开发商们已经改进了表单元素的CSS支持，虽然其可用性仍受诟病，但现在你已经可以使用CSS来给[HTML表单](https://developer.mozilla.org/en-US/docs/HTML/Forms)添加样式了。


### 并非所有组件受CSS的影响都是平等的
如今在表单使用CSS时依然有一些困难；这些问题可归为三类：

#### 还好的
若存在跨平台问题，一些元素可以只添加少许的样式，有如下几个结构元素：

1. [`<form>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/form)
2. [`<fieldset>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/fieldset)
3. [`<label>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/label)
4. [`<output>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/output)

此外，还有所有的文本框组件（单行或多行），以及按钮。

#### 比较糟糕的
一些元素只能使用很少的样式，而且得依赖一些复杂的技巧，偶尔还得用到CSS3的高级知识。

这其中包括了[`<legend>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/legend)元素；该元素不能在跨平台时被恰当地定位。此外，复选框及单选框不能直接添加样式；然而有了CSS3之后你就可以做到这点了。[placeholder](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-placeholder)的内容是不能通过标准方法来添加样式的，但所有实现了它的浏览器都会以私有的CSS伪元素或伪类的形式让你能给它添加样式。

至于要具体如何处理这些特殊情况，我们会在[HTML表单高级样式]()一文中讨论。

#### 丑陋的
某些元素是不能用CSS添加样式的。它们包括所有的高级UI组件比如范围滑块、颜色、日期控件，以及下拉组件（包括[`<select>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/select), [`<option>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/option), [`<optgroup>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/optgroup), [`<datalist>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/datalist) 等元素）。文件选择器组件也被认为是不能添加样式的，而新的[`<progress>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/progress)和[`<meter>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/meter)元素也在此之列。

这些组件的主要问题在于，它们拥有非常复杂的结构，而CSS没有足够的表现力来给这些组件的各个细节添加样式。若你非得定制这些组件，就只能依靠Javascript来构建一棵能让你添加样式的DOM树。我们将在[如何创建定制表单组件](https://developer.mozilla.org/en-US/docs/HTML/Forms/How_to_build_custom_form_widgets)一文中学习如何做到这一点。

## 基本样式
在用CSS给那些[易于添加样式的元素](./#还好的)的元素以样式时，你不必面对任何困难，因为它们多数表现得和其它HTML元素一样。然而，每个浏览器的用户代理样式表会导致一些不一致的情况，所以，这里会有几个技巧来帮你轻松地给它们添加样式。

### 搜索框
搜索框是文本框中唯一一种需要点技巧来添加样式的。在基于webkit的浏览器（chrome, safari等）中，你得用`-webkit-appearance`属性来作下调整。我们将会在[HTML表单高级样式]()一文更深入地探讨该属性。

#### 例子
```html
<form>
  <input type="search">
</form>
```

```css
input[type=search] {
  border: 1px dotted #999;
  border-radius: 0;

  -webkit-appearance: none;
}
```

![](https://developer.mozilla.org/files/4153/search-chrome-macos.png)

在上面这张Chrome的搜索框截图中，连个文本框都设置了边框，但第一个文本框没有使用`-webkit-appearance`属性进行渲染，而第二个贼使用了`-webkit-appearance:none`。它们间的差别值得注意。

### 字体和文本
CSS字体和文本特性在任何组件中都可以被轻易使用（当然，你也可以在表单组件上使用[@font-face](https://developer.mozilla.org/en-US/docs/Web/CSS/@font-face)）。然而，不同浏览器的行为表现通常是不一样的。某些组件默认不会从父元素那继承[font-family](https://developer.mozilla.org/en-US/docs/Web/CSS/font-family)和[font-size](https://developer.mozilla.org/en-US/docs/Web/CSS/font-size)，同时许多浏览器会使用系统的默认样式来作为替代。要让你的表单的外观与其他内容保持一致，你可以在样式表中添加如下规则：

```css
button, input, select, textarea {
  font-family : inherit;
  font-size   : 100%;
}
```

下面的截图体现了设置之后的不同；左边是MAC OSX的Firefox中元素的默认渲染效果，其使用了系统的默认字体样式。而右边则是使用了上面的字体协调样式后的相同元素。

![](https://developer.mozilla.org/files/4157/font-firefox-macos.png)

要使用系统默认样式还是自定义样式以适应页面内容，仍存在很多争议。这个决定权在于身为网页或web应用设计师的你身上。

### 盒模型
所有的文本框都完全支持CSS盒模型相关的属性（[width](https://developer.mozilla.org/en-US/docs/Web/CSS/width), [height](https://developer.mozilla.org/en-US/docs/Web/CSS/height), [padding](https://developer.mozilla.org/en-US/docs/Web/CSS/padding), [margin](https://developer.mozilla.org/en-US/docs/Web/CSS/margin), [border](https://developer.mozilla.org/en-US/docs/Web/CSS/border)）。然而以前要呈现这些组件时，浏览器都得依赖系统的默认样式。至于如何把这些样式混用到你的页面中，这得取决于你。

若你想保持这些原生组件的样子和体验，你会在给它们实现一致的样式时遇到点困难。这是因为每个组件都有它们独有的边框、内边距和外边距的规定。所以，如果你希望在几个不同的组件间保持相同的大小，你就得使用[box-sizing](https://developer.mozilla.org/en-US/docs/Web/CSS/box-sizing)属性：

```css
input, textarea, select, button {
  width : 150px;
  margin: 0;

  -webkit-box-sizing: border-box; /* 兼容基于Webkit的旧版浏览器 */
     -moz-box-sizing: border-box; /* 兼容基于Gecko的旧版浏览器（Firefox < 29） */
          box-sizing: border-box;
}
```

![](https://developer.mozilla.org/files/4161/size-chrome-win7.png)

上面的截图中，左边一列是不使用`box-sizing`构建的，而右边一列则使用了该属性并赋予其值`border-box`。可见设置该属性让所有的元素都占据了相同的空间大小，而覆盖了系统给各种组件的默认规则。

### 定位
定位HTML表单元素通常不是什么大问题，然而有两个特殊元素值得你关注一下：

#### legend
`<legend>`元素可以很好地支持样式，除了定位。在每种浏览器中，`<legend>`元素都位于其父`<fieldset>`元素的上边框以上，根本没办法在HTML文档流中改变其定位、让其远离那个上边框。你只能使用[position](https://developer.mozilla.org/en-US/docs/Web/CSS/position)属性来让其绝对或相对定位，否则它就只能视作是fieldset边框的一部分。

以为无障碍技术的原因，使得`<legend>`成为很重要的元素（它作为fieldset中各个表单组件的label，并以此被无障碍设备读出），通常他会和一个标题做搭配，并以无障碍技术可识别的形式隐藏起来，就像这样：

```html
<fieldset>
  <legend>Hi!</legend>
  <h1>Hello</h1>
</fieldset>
```

```css
legend {
  width: 1px;
  height: 1px;
  overflow: hidden;
}
```

#### textarea
所有浏览器都默认将[textarea](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/textarea)元素当作内联元素，并让它与文本的底线对齐。而这种设定通常并不是我们想要的，使用[display](https://developer.mozilla.org/en-US/docs/Web/CSS/display)属性可以很容易就将其从`inline-block`改为`block`。但若你还想把它当内联元素使用，那通常得改变其垂直对齐方式：

```css
textarea {
  vertical-align: top;
}
```

## 实例
来看一个给表单以样式的例子吧，通过例子，许多相关的知识点会更容易理解些。而我们要构建的，是如下图所示的contact表单：

![](https://developer.mozilla.org/files/4149/screenshot.png)

### HTML
相比[本指南第一篇文章]()，这里的HTML稍微多了点内容；只有几个额外的字段和一个标题而已。

```html
<form>
  <h1>to: Mozilla</h1>

  <div id="from">
    <label for="name">from:</label>
    <input type="text" id="name" name="user_name">
  </div>

  <div id="reply">
    <label for="mail">reply:</label>
    <input type="email" id="mail" name="user_email">
  </div>

  <div id="message">
    <label for="msg">Your message:</label>
    <textarea id="msg" name="user_message"></textarea>
  </div>
 
  <div class="button">
    <button type="submit">Send your message</button>
  </div>
</form>
```

### CSS
有趣的部分开始了，但在我们编码之前，还需要三个额外的资源：

1. 明信片[背景](https://developer.mozilla.org/files/4151/background.jpg)
2. 一套打字机字体：[fontsquirrel.com上的"Secret Typewriter"](http://www.fontsquirrel.com/fonts/Secret-Typewriter)
3. 一套手写字体：[fontsquirrel.com上的"Journal"](http://www.fontsquirrel.com/fonts/Journal)

现在我们可以投入写代码了。首先，我们要准备好[@font-face](https://developer.mozilla.org/en-US/docs/Web/CSS/@font-face)的定义以及`<body>`、`<form>`元素的基本样式：

```css
@font-face{
  font-family : "handwriting";

  src : url('journal.eot');
  src : url('journal.eot?') format('eot'),
        url('journal.woff') format('woff'),
        url('journal.ttf') format('truetype');
}

@font-face{
  font-family : "typewriter";

  src : url('veteran_typewriter.eot');
  src : url('veteran_typewriter.eot?') format('eot'),
        url('veteran_typewriter.woff') format('woff'),
        url('veteran_typewriter.ttf') format('truetype');
}

body {
  font  : 21px sans-serif;

  padding : 2em;
  margin  : 0;

  background : #222;
}

form {
  position: relative;

  width  : 740px;
  height : 498px;
  margin : 0 auto;

  background: #FFF url(background.jpg);
}
```

然后我们来定位标题和所有表单元素：

```css
h1 {
  position : absolute;
  left : 415px;
  top  : 185px;
 
  font : 1em "typewriter", sans-serif;
}

#from {
  position: absolute;
  left : 398px;
  top  : 235px;
}

#reply {
  position: absolute;
  left : 390px;
  top  : 285px;
}

#message {
  position: absolute;
  left : 20px;
  top  : 70px;
}
```

接下来，我们得开始对表单元素自身做配置了。首先，确保`<label>`使用了正确的字体：

```css
label {
  font : .8em "typewriter", sans-serif;
}
```

文本框需要使用一些公共样式。简单起见，可以移除它们的边框和背景，然后重新定义其内外边距：

```css
input, textarea {
  font    : .9em/1.5em "handwriting", sans-serif;

  border  : none;
  padding : 0 10px;
  margin  : 0;
  width   : 240px;

  background: none;
}
```

而当这些输入框获得焦点时，还得让它们用一个浅灰色、半透明背景做高亮。注意为了移除一些浏览器自带默认的高亮，还需要配置[outline](https://developer.mozilla.org/en-US/docs/Web/CSS/outline)属性：

```css
input:focus, textarea:focus {
  background   : rgba(0,0,0,.1);
  border-radius: 5px;
  outline      : none;
}
```

现在我们的文本框已经整好了，但我们还得调整单行和多行文本框以作适配，因为通常它们看起来是一点都不相同的。

对单行文本框需要一些微调以让其在IE下看起来漂亮点。IE不是基于字体的自然高度来定义文本框高度的（但其它所有浏览器都这么做），要修复这点，我们得给文本框指定一个明确的高度，如下所示：

```css
input {
    height: 2.5em; /* 针对IE */
    vertical-align: middle; /* 可选配置，能在旧版IE中看起来漂亮点 */
}
```

`<textarea>`元素应被预设置为块级元素进行渲染。这里还有两个重要的属性，[resize](https://developer.mozilla.org/en-US/docs/Web/CSS/resize)和[overflow](https://developer.mozilla.org/en-US/docs/Web/CSS/overflow)。由于我们采用固定大小的设计，所以得使用`resize`属性来防止用户改变多行文本框的大小。而`overflow`属性则让文本框在不同浏览器下的效果趋于一致；因为有的浏览器默认使用值`auto`而另一些使用值`scroll`。本例中，最好得保证各个浏览器下都使用`auto`：

```css
textarea {
  display : block;

  padding : 10px;
  margin  : 10px 0 0 -10px;
  width   : 340px;
  height  : 360px;

  resize  : none;
  overflow: auto;
}
```

[`<button>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/button)元素可以很方便地使用CSS；这样你就可以尽情发挥了，即使用上[伪元素](https://developer.mozilla.org/en-US/docs/CSS/Pseudo-elements)也没问题！

```css
button {
  position     : absolute;
  left         : 440px;
  top          : 360px;

  padding      : 5px;

  font         : bold .6em sans-serif;
  border       : 2px solid #333;
  border-radius: 5px;
  background   : none;

  cursor       : pointer;

-webkit-transform: rotate(-1.5deg);
   -moz-transform: rotate(-1.5deg);
    -ms-transform: rotate(-1.5deg);
     -o-transform: rotate(-1.5deg);
        transform: rotate(-1.5deg);
}

button:after {
  content: " >>>";
}

button:hover,
button:focus {
  outline   : none;
  background: #000;
  color   : #FFF;
}
```

随意尝试下吧，试了你才知道你可以做到！

## 结论
如你所见，如果我们想构建只含文本框和按钮的表单，那么用CSS来提供样式是件很容易的事。若你还想了解多些能让你更轻松地处理表单组件的CSS技巧，可以参见[normalize.css项目](http://necolas.github.com/normalize.css)的表单部分。

[下篇文章]()，我们会学习如何处理那些属于“比较糟糕的”和“丑陋的”类别的表单组件。
