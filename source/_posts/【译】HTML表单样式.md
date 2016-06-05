title: 【译】HTML表单样式
date: 2016-06-05 14:13:20
tags: [html,前端]
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

下面的截图体现了设置之后的不同；左边是MAC OSX的Firefox中元素的默认渲染效果，其使用了系统的默认字体样式。而右边则是使用了我们的字体协调样式的相同元素。

![](https://developer.mozilla.org/files/4157/font-firefox-macos.png)
