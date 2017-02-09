title: 【译】怎么样构建HTML表单
date: 2016-05-03 16:04:54
tags: [html,前端,翻译]
categories: 前端
---
> * [系列文章说明](http://levonlin.info/2016/04/28/%E3%80%90%E8%AF%91%E3%80%91HTML%E8%A1%A8%E5%8D%95%E6%8C%87%E5%8D%97-%E7%AC%AC%E4%B8%80%E4%B8%AAHTML%E8%A1%A8%E5%8D%95/)
> * [原文](https://developer.mozilla.org/en-US/docs/Web/Guide/HTML/Forms/How_to_structure_an_HTML_form)

在建立[HTML表单](https://developer.mozilla.org/en-US/docs/HTML/Forms)时，最重要的一件事就是如何用正确的方式构建它。而之所以重要，原因有二：一是保证表单能被正确使用、二是这能保证你的表单是无障碍的（可以被能力不同的人使用）。而正因HTML表单的[无障碍性](https://developer.mozilla.org/en-US/docs/Accessibility)很重要，故在本文中我们将了解如何使一个表单无障碍，这并不很难、只是有几个技巧需要你了解下。

表单的灵活性决定了它们是[HTML](https://developer.mozilla.org/en-US/docs/HTML)中最复杂的结构。通过使用专门的表单元素和属性，我们可以建立任何形式的表单。也确实有过一些更丰富多彩的脚本技术如[XForms](https://developer.mozilla.org/en-US/docs/XForms)（现已过时），但很不幸这些表单技术并未被各大浏览器广泛应用。因此，我们往往得依赖Javascript来处理表单；但在本文中，我们只会详细讨论如何使用表单元素，若你想知道如何构建自定义表单组件，可参阅[怎样创建定制表单组件]()。

## 整体结构
### from元素
[`<form>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/form)元素是用来正式定义表单的元素，其属性决定了表单会有什么行为。当你想要建立一个HTML表单时，就必须以该元素开头。很多无障碍技术或浏览器插件能够检测到`<form>`元素，同时他们也实现了特殊的方式来让表单更易于使用。

>注：严禁把一个form元素嵌套在另一个里边。那样会导致不可预知的行为，具体取决于用户使用了何种浏览器。

`<form>`元素支持下列属性，它们全是可选的：

**`<form>`元素的属性**

| 属性名      | 默认值   |  描述  |
| ----        | ----     |  ----  |
| [accept-charset](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/form#attr-accept-charset) | UNKNOWN | 一个空格分隔或逗号分隔的列表（HTML5中，只有空格可作为分隔符。），这个列表包括了服务器支持的字符编码。浏览器以这些编码被列举的顺序使用它们。默认值是一个保留字符串“UNKNOWN”。这个字符串指的是和包含这个form元素的文档使用相同的编码。 |
| [action](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/form#attr-action) |  | 处理这个表单所提交信息的程序所在的URL。 |
| [autocomplete](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/form#attr-autocomplete) | on | 指示表单组件是否能够拥有被浏览器自动补全的默认值。该属性共有两个可选值：`on`或`off`。 |
| [enctype](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/form#attr-enctype) | application/x-www-form-urlencoded | 当 method 属性值为 post 时, enctype 是提交form给服务器的内容的 [MIME 类型](http://en.wikipedia.org/wiki/Mime_type) 。可能的取值有：application/x-www-form-urlencoded (默认值)、multipart/form-data (用于一个 type 属性设置为 "file" 的`<input>`元素)、text/plain (HTML5)。 |
| [method](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/form#attr-method) | get | 浏览器使用这种 [HTTP](http://www.w3.org/Protocols/rfc2616/rfc2616.html) 方式来提交 form。可能的值有:post、get。 |
| [name](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/form#attr-name) |  | 这个form的名字。一个文档中的多个form当中，name必须唯一而不仅仅是一个空字符串。作为替代，最好使用`id`。 |
| [novalidate](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/form#attr-novalidate)        |    (false)    | 这个布尔类型的属性指示了，当提交时form是否没有被验证。 |
| [target](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/form#attr-target) | _self | 用一个关键字指示在何处展示提交表单后收到的响应，展示的地方可以是`<iframe>`、标签页、浏览器窗口等；下面是该属性几个可能的值：`_self` (把响应加载到同一浏览器环境（[`<iframe>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/iframe)、标签页、浏览器窗口等）)、`_blank` (把响应加载到一个新的浏览器环境)、`_parent` (把响应加载到父级浏览器环境，若无父级环境则和`_self`一致)、`_top` (把响应加载到最顶层的浏览器环境（即已无父环境的浏览器环境），若无父级环境则和`_self`一致) |

要注意的是，可能你会经常在`<form>`元素外使用表单组件，而这样做的话这些组件就和任何表单都没有关系了。虽然这些表单组件确实可以用在表单之外，但如此一来你就得给它们设定好用途了，毕竟它们单独使用时是没有特殊效果的，你必须用Javascript来自定义。

从技术上讲，HTML5已经引进了一个`form`属性给各个表单元素，该属性能让你明确绑定一个元素和一个表单，即使元素不再`<form>`的内部。然而很遗憾，该特性的跨浏览器实现暂时还没能让人觉得它是可靠的。


### fieldset和legend元素
[`<fieldset>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/fieldset)元素可以很方便地用于创建一组具有相同目的的组件。一个`<fieldset>`元素可以被一个[`<legend>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/legend)元素所标记，`<legend>`元素用于明确地描述`<fieldset>`元素的目的。许多无障碍技术会使用`<legend>`元素作为`<fieldset>`元素内各组件label的一部分。例如，某些屏幕阅读器如[jaws](http://www.freedomscientific.com/products/fs/jaws-product-page.asp)或[NVDA](http://www.nvda-project.org/)会在读出各组件的label前先读出legend里的内容。

举个小例子：

```html
<form>
  <fieldset>
    <legend>Fruit juice size</legend>
    <p>
      <input type="radio" name="size" id="size_1" value="small" />
      <label for="size_1">Small</label>
    </p>
    <p>
      <input type="radio" name="size" id="size_2" value="medium" />
      <label for="size_2">Medium</label>
    </p>
    <p>
      <input type="radio" name="size" id="size_3" value="large" />
      <label for="size_3">Large</label>
    </p>
  </fieldset>
</form>
```

本例中，屏幕阅读器会将为第一个组件读出"Fruit juice size small"，为第二个组件读出"Fruit juice size medium"，为第三个组件读出"Fruit juice size large"。

上面这个例子是最重要的用法之一，当你有一堆单选按钮时，你得保证它们嵌套于`<fieldset>`元素中。而一般来说，`<filedset>`元素也能用于给一个表单分段。

由于该元素对无障碍技术的影响，`<fieldset>`元素已经成为构建无障碍表单的关键元素；但这也不是说你可以滥用它。当你构建表单时，可以试着听一下屏幕阅读器如何读取它，若听起来很奇怪，那就有必要改进你的表单结构了。

`<fieldset>`元素支持以下的特有属性：

**`<fieldset>`元素的属性**

| 属性名      | 默认值   |  描述  |
| ----        | ----     |  ----  |
| [disable](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/fieldset#attr-disabled) | (false) | 若设置了该布尔值，表单中的后代元素（除了其第一个`<legend>`元素的后代）将被禁用和禁止编辑。这些元素将不再接收任何浏览器事件，譬如鼠标点击或和输入焦点有关的事件。通常浏览器会将这些元素显示为灰色。 |

### label元素
[`<label>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/label)元素用于给某个表单组件定义一个标签。若你想构建一个无障碍表单，这个元素可就是最重要的元素了。

`<label>`元素支持以下的属性：

**`<label>`元素的属性**

| 属性名      | 默认值   |  描述  |
| ----        | ----     |  ----  |
| [for](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/label#attr-for) |  | 值取在同一文档中可标记的组件的ID。 文档中第一个ID值匹配该属性值的元素就是被这个label元素标记的元素。 |

`<label>`元素用`for`属性绑定其对应的组件，`for`属性可引用关联组件的`id`值。此外组件也可以嵌套于其`<label>`元素，但即使是这样，设置`for`属性仍被认为是最佳做法，因为一些无障碍设备无法理解label和组件间的隐性关系（译注：其实嵌套就相当于自动设置了`for`属性，不存在什么隐性关系，综合下文来看也这应该属于“最佳做法”的范畴吧）。

而即使不考虑无障碍技术，若有一个指派给特定组件的label，会让用户在点击label时，就能在所有浏览器下激活相应的组件。这就非常有便于使用单选按钮和复选框啦。

```html
<form>
  <p>
    <input type="checkbox" id="taste_1" name="taste_cherry" value="1">
    <label for="taste_1">I like cherry</label>
  </p>
  <p>
    <label for="taste_2">
      <input type="checkbox" id="taste_2" name="taste_banana" value="1">
      I like banana
    </label>
  </p>
</form>
```

某些无障碍设备在处理指派多个label的单个组件时会出现问题。因此，你得把组件嵌套进相关联的元素，才能构建一个无障碍的表单（译注：不是很能理解这句话，上面不是说这种做法一些无障碍设备不能理解吗？而且解决方法不应该是不准多对一吗，和嵌套有半毛钱关系？）。

来看下面这个例子：

```html
<form>
  <p>Required fields are followed by <strong><abbr title="required">*</abbr></strong>.</p>

  <!-- 当你要标记的组件是label的后代时，就没必要在label上使用for属性了。 -->
  <!-- 所以这个： -->
  <label>
    <span>Name: </span>
    <input type="text" name="username" required />
    <strong><abbr title="required">*</abbr></strong>
  </label>  

  <!-- 和这个是一样的： -->  
  <div>
    <label for="username">Name: </label>
    <input id="username" type="text" name="username" required />
    <strong><abbr title="required">*</abbr></strong>
  </div>

  <p>
    <label for="birth"> <!-- 在这里，for属性就是多余的了。 -->
      <span>Date of birth: </span>
      <input type="text" id="birth" name="userbirth" maxlength="10" /> <em>formated as mm/dd/yyyy</em>
    </label>
  </p>
</form>
```

在这个例子中，第一部分代码写的是必填元素的规则。该规则必须在表单头部以保证在用户找到必填元素之前，屏幕阅读器等无障碍设备能将其展示或读给用户。这样用户才能知道他到底该干嘛。

第一个文本域是必填的，所以在其label元素内指明了该文本域的名字以及该文本域是必填的。如此一来，屏幕阅读器会把label读作"Name star"或"Name required"（这取决于屏幕阅读器的设定，通常与第一段写的那些规则相一致）。而若你使用了两个label，那就不能确保用户会被提示该元素是必填的。

第二个表单元素（译注：`id`为`birth`那个）也采用了同样的手段，使用上面例子的代码后，你就能保证用户在进入该字段时会被告知如何格式化日期数据了。

### output元素
该元素用于存放计算的输出结果，它定义了用于获取运算数据的输入框和用于展示结果的元素间的联系。对于一些无障碍技术来讲，该元素也可以认为是一个保持活动的区域（也就是说当[`<output>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/output)元素的内容发生变化时，无障碍设备就能感知该变化并能对其做出响应）。

'<output>`元素支持以下的属性：

**`<output>`元素的属性**

| 属性名      | 默认值   |  描述  |
| ----        | ----     |  ----  |
| [for](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/output#attr-for) |  | 一个用空格分隔的其他元素的ID列表，指示那些提供输入值（或其他效应）参与运算的元素 |

### 表单的常用HTML结构
除了一些表单的具体结构，我们还应知道表单其实就是HTML代码。这就意味着你可以用所有的HTML技术来构建一个表单。

就如你已经看到的例子中那样，通常我们会把一个label及其对应的组件包裹在一个[`<p>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/p)或[`<div>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/div)元素中。

而除了`<fieldset>`元素，我们通常也会使用HTML的标题和`section`来构建复杂的表单。

HTML的列表则常用在使用复选框和单选框时。

来看一个简单的支付表单例子：

```html
<form>
  <h1>Payment form</h1>
  <p>Required fields are followed by <strong><abbr title="required">*</abbr></strong>.</p>
 
  <section>
    <h2>Contact information</h2>
    
    <fieldset>
      <legend>Title</legend>
      <ul>
        <li>
          <label for="title_1">
            <input type="radio" id="title_1" name="title" value="M." />
            Mister
          </label>
        </li>
        <li>
          <label for="title_2">
            <input type="radio" id="title_2" name="title" value="Ms." />
            Miss
          </label>
        </li>
      </ul>
    </fieldset>
    
    <p>
      <label for="name">
        <span>Name: </span>
        <input type="text" id="name" name="username" required />
        <strong><abbr title="required">*</abbr></strong>
      </label>
    </p>
    
     <p>
      <label for="mail">
        <span>E-mail: </span>
        <input type="email" id="mail" name="usermail" required />
        <strong><abbr title="required">*</abbr></strong>
      </label>
    </p>
  </section>
 
  <section>
    <h2>Payment information</h2>
    
    <p>
      <label for="card">
        <span>Card type:</span>
        <select id="card" name="usercard">
          <option value="visa">Visa</option>
          <option value="mc">Mastercard</option>
          <option value="amex">American Express</option>
        </select>
      </label>
    </p>
    <p>
      <label for="number">
        <span>Card number:</span>
        <input type="text" id="number" name="cardnumber" required />
        <strong><abbr title="required">*</abbr></strong>
      </label>
    </p>
    <p>
      <label for="date">
        <span>Expiration date:</span>
        <input type="text" id="date" name="expiration" required />
        <strong><abbr title="required">*</abbr></strong>
        <em>formated as mm/yy</em>
      </label>
    </p>
  </section>
 
  <section>
    <p>
      <button>Validate the payment</button>
    </p>
  </section>
</form>
```

看一看执行效果吧（这里加了点CSS）

[演示](https://developer.mozilla.org/en-US/docs/Web/Guide/HTML/Forms/How_to_structure_an_HTML_form/Example)

## HTML组件
构建表单时，我们需要一些组件来从用户那儿收集数据。所以在本文中，我们还将看到如何呈现这些组件；若你想了解这些组件是如何工作的，可参见[原生表单组件]()。

### input元素
这个元素相当特殊，因为它可以变成不少东西。只要简单设置下其`type`属性，它就能发生大变化。为简单起见，`type`的值可归为四种类型：单行文本框、无文本输入的控件、日期控件和按钮。而正因这种多样性，[`<input>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input)元素支持众多属性，但我们很难知道其中那个是相关的、哪个是必须的，这取决于`type`属性的值。

下面的表格就是一个汇总（要获得完整的属性列表，请访问[`<input>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input)元素的页面）：

| `type`属性的值 | 描述     |  必要属性  |  可选属性  |
| ----           | ----     |  ----      |  ----      |
| **单行文本框** | | | |
| text | 这是最基本的文本框，该值也是`type`属性的默认值，这时不会执行自动校验 |  | [autocomplete](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-autocomplete), [list](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-list), [maxlength](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-maxlength), [pattern](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-pattern), [placeholder](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-placeholder), [readonly](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-readonly), [required](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-required), [size](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-size), [spellcheck](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-spellcheck) |
| email | 用于编辑一或多个电子邮箱地址的文本框 |  | [autocomplete](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-autocomplete), [list](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-list), [maxlength](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-maxlength), [multiple](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-multiple), [pattern](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-pattern), [placeholder](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-placeholder), [readonly](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-readonly), [required](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-required), [size](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-size) |
| password | 文本框的值会被隐藏起来 |  | [autocomplete](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-autocomplete), [list](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-list), [maxlength](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-maxlength), [readonly](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-readonly), [required](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-required), [size](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-size) |
| search | 用于输入查询字符串的文本框 |  | [autocomplete](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-autocomplete), [autosave](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-autosave), [list](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-list), [maxlength](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-maxlength), [pattern](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-pattern), [placeholder](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-placeholder), [readonly](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-readonly), [required](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-required), [size](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-size), [spellcheck](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-spellcheck) |
| tel | 用于编辑电话号码的文本框 |  | [autocomplete](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-autocomplete), [list](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-list), [maxlength](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-maxlength), [pattern](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-pattern), [placeholder](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-placeholder), [readonly](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-readonly), [required](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-required), [size](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-size) |
| url | 用于编辑绝对URL的文本框 |  | [autocomplete](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-autocomplete), [list](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-list), [maxlength](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-maxlength), [pattern](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-pattern), [placeholder](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-placeholder), [readonly](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-readonly), [required](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-required), [size](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-size) |
| **无文本输入的控件** | | | |
| checkbox | 复选框 |  | [checked](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-checked), [required](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-required) |
| color | 用于指定颜色的控件 |  | [autocomplete](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-autocomplete), [list](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-list), [required](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-required) |
| file | 用于让用户选择文件的控件 |  | [accept](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-accept), [multiple](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-multiple), [required](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-required) |
| hidden | 不显示的控件，但它的值会被提交给服务器 |  |  |
| number | 用于获得浮点数的控件 |  | [autocomplete](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-autocomplete), [list](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-list), [max](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-max), [min](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-min), [readonly](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-readonly), [required](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-required), [step](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-step) |
| radio | 单选框 |  | [checked](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-checked), [required](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-required) |
| range | 用于获得非精确值的控件 |  | [autocomplete](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-autocomplete), [list](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-list), [max](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-max), [min](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-min), [required](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-required), [step](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-step) |
| **时间和日期控件**（尚未支持，译注：最新的浏览器已实现了部分控件） | | | |
| date | 用于获得日期（年月日，没有钟点）的控件 |  | [autocomplete](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-autocomplete), [list](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-list), [max](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-max), [min](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-min), [readonly](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-readonly), [required](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-required) |
| datetime | 用于获得日期和时间（时分秒，及二分之一秒）的控件，基于UTC时区 |  | [autocomplete](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-autocomplete), [list](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-list), [max](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-max), [min](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-min), [readonly](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-readonly), [required](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-required) |
| datetime-local | 用于获得日期和时间的控件，不含时区 |  | [autocomplete](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-autocomplete), [list](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-list), [max](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-max), [min](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-min), [readonly](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-readonly), [required](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-required) |
| month | 用于获得年份和月份的控件，不含时区 |  | [autocomplete](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-autocomplete), [list](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-list), [max](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-max), [min](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-min), [readonly](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-readonly), [required](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-required) |
| time | 用于获得时间的控件，不含时区 |  | [autocomplete](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-autocomplete), [list](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-list), [max](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-max), [min](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-min), [readonly](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-readonly), [required](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-required) |
| month | 用于获得年份和周数的控件，不含时区 |  | [autocomplete](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-autocomplete), [list](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-list), [max](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-max), [min](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-min), [readonly](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-readonly), [required](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-required) |
| **按钮** | | | |
| button | 无默认行为的按钮 |  | [formaction](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-formaction), [formenctype](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-formenctype), [formmethod](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-formmethod), [formnovalidate](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-formnovalidate), [formtarget](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-formtarget) |
| image | 图片按钮 | [src](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-src), [alt](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-alt) | [width](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-width), [height](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-height), [formaction](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-formaction), [formenctype](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-formenctype), [formmethod](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-formmethod), [formnovalidate](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-formnovalidate), [formtarget](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-formtarget) |
| reset | 用于将表单内容重置为默认值的按钮 |  | [formaction](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-formaction), [formenctype](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-formenctype), [formmethod](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-formmethod), [formnovalidate](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-formnovalidate), [formtarget](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-formtarget) |
| submmit | 用于提交表单的按钮 |  | [formaction](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-formaction), [formenctype](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-formenctype), [formmethod](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-formmethod), [formnovalidate](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-formnovalidate), [formtarget](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-formtarget) |

如果我们给`type`属性设置的值不被浏览器支持，那它就会把`<input>`元素渲染为和使用`text`值一样的效果。这样能确保表单可以使用，即使它看起来不那么吸引人。

虽然`<input>`元素是个强大的工具，但它也并非万能，所以还得一些其他元素来处理这些`<input>`力不能及的情况。

### textarea元素
该元素用于声明多行文本框。其行为与单行文本框基本一致，除了能让用户在输入文本时换行。此外该元素也接受几个额外的属性，以控制其在渲染时能占多大地方：

**[`<textarea>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/textarea)元素的属性**

| 属性名      | 默认值   |  描述  |
| ----        | ----     |  ----  |
| [cols](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/textarea#attr-cols) | 20 | 文本控件的可视宽度，单位是字符的平均宽度 |
| [rows](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/textarea#attr-rows) |  | 文本控件的可视文本行数 |
| [wrap](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/textarea#attr-wrap) | soft | 表明控件如何包含文本，可取值为`hard`或`soft` |

注意`<textarea>`元素的用法与`<input>`元素稍有不同。`<input>`元素是个自闭合元素，这意味着它不能包含任何子元素。而另一方面，`<textarea>`元素则是个可以直接包含文本内容的普通元素。

这就造成两个影响：

* 若你想给`<input>`元素定义默认值，你得使用`valur`属性；而对于`<textarea>`元素，你只需把默认文本置于其起始标签之间即可。
* 默认地，`<textarea>`元素只接受文本内容；也就是说任何输入到`<textarea>`元素内的HTML内容都会以纯文本的形式显示出来。

下面这个例子中，`<textarea>`元素会以相同形式渲染出来：

```html
<form>
  <p>
    <label for="text_1">With regular HTML</label><br>
    <textarea id="text_1" name="regular"><p>I'm a paragraphe</p></textarea>
  </p>
  <p>
    <label for="text_2">With escaped HTML</label><br>
    <textarea id="text_2" name="escaped">&lt;p&gt;I'm a paragraphe&lt;/p&gt;</textarea>
  </p>
  <p>
    <button>Send me</button>
  </p>
</form>
```

### select, option和optgroup元素
[`<select>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/select)元素能让你构建选择框（也叫组合框）。选择框是一种让用户选择一或多个预定义的值的组件。单值和多值选择框的不同将在[原生表单组件]()一文中进行讨论。

选择框中的每个值都会用一个[`<option>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/option)元素进行定义，而这些`<option>`元素也可以置于[`<optgroup>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/optgroup)元素中进行分组。

来看个例子：

```html
<form>
  <p>
    <label for="myFruit">Pick a fruit</label>
    <select id="myFruit" name="fruit">
      <!-- 这里耍了个小把戏，你以为你选择了根香蕉，其实你是吃了个橘子 >:-) -->
      <option value="orange">Banana</option>
      <option>Cherry</option>
      <optgroup label="berries">
        <option>Blueberry</option>
        <option>Raspberry</option>
        <option>Strawberry</option>
      </optgroup>
    </select>
  </p>
</form>
```

若`<option>`元素设置了`value`属性，那么在表单提交时该值就会被提交。若`value`属性未设置，则使用`<option>`元素的内容作为整个选择框的值。

至于`<optgroup>`元素，其`label`属性将会先于其内容进行展示。而且它虽然看起来像一个选项，但实质上它是不可选的。

**`<option>`元素的属性**

| 属性名      | 默认值   |  描述  |
| ----        | ----     |  ----  |
| [label](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/option#attr-label) |  | 该属性是用于描述选项的文本，若`label`属性未被定义，那它的值就默认取元素的文本内容 |
| [selected](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/option#attr-selected) | (false) | 目前来讲，该布尔值表明选项是否在一开始就被选中 |

**`<optgroup>`元素的属性**

| 属性名      | 默认值   |  描述  |
| ----        | ----     |  ----  |
| [label](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/optgroup#attr-label) |  | 一组选项的名字。**该属性是强制要有的** |

### datalist元素
该元素通过为给定的组件预设值，实现了对已有组件的拓展，最广为人知的例子就是文本框的自动补全列表了。这些用于填充的值实际上是通过`<datalist>`元素里的`<option>`元素进行设置的。

要把`<datalist>`元素和某个组件绑定起来，你得先设置目标组件的`list`属性；该属性需指定为相应的`<datalist>`元素的`id`属性。

`<datalist>`元素是最近才被加入到HTML表单中的，故仍有些浏览器不支持它。这里有一个巧妙的兼容技巧，用于应对这些浏览器：

```html
<form>
  <p>
    <label for="myFruit">What is your favorite fruit?</label>
    <input type="text" id="myFruit" name="fruit" list="fruitList" />
    
    <datalist id="fruitList">
      <label for="suggestion">or pick a fruit</label>
      <select id="suggestion" name="altFruit">
        <option value="banana">Banana</option>
        <option value="cherry">Cherry</option>
        <option value="strawberry">Strawberry</option>
      </select>
    </datalist>
  </p>
</form>
```

支持`<datalist>`的浏览器将会忽略其中那些非`<option>`元素，且最终效果和预期的一样。而支持`<datalist>`的浏览器将会显示label和选择框。当然，也有别的方法来处理`<datalist>`缺乏支持的问题，但那得用到javascript，而这通常不是个好的选择。

![datalist-demo](https://developer.mozilla.org/files/4583/datalist-safari.png)

### meter和progress元素
这两个元素常用于图形化展示给定的数字值，两者的区别主要是语义上的：

* [`<meter>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/meter)元素代表一个静态值，其相对位置在最大值和最小值之间。
* [`progress`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/progress)元素代表一个在最大值和最小值间随时间变化的值。值得注意的是，改变该元素的值（也就是显示进度）必须交给Javascript来完成，该元素自身没有任何机制来做到这一点。

默认的，这俩元素支持下列特有属性：

**`<meter>`元素的属性**

| 属性名      | 默认值   |  描述  |
| ----        | ----     |  ----  |
| [min](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/meter#attr-min) | 0 | 度量范围的数值下界 |
| [max](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/meter#attr-max) | 1 | 度量范围的数值上界 |
| [low](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/meter#attr-low) | `min`值 | 比度量范围的下界要高的数值界限 |
| [high](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/meter#attr-high) | `max`值 | 比度量范围的上界要低的数值界限 |
| [optimum](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/meter#attr-optimum) |  | 最佳数字值 |

**译注：溢出low、high间的范围时，浏览器会以特殊样式显示`<metter>`数值**

**`<progress>`元素的属性**

| 属性名      | 默认值   |  描述  |
| ----        | ----     |  ----  |
| [max](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/progress#attr-max) |  | 该属性用于描述`<progress>`对应的任务完成前需要做多少工作量 |

### button元素
[`<button>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/button)元素是最便于创建表单按钮的方式。一个按钮可以是三种类型质疑，由以下`type`属性值决定：

* `submmit`按钮会把表单数据发送给由`<form>`元素的`action`属性定义的页面。
* `reset`按钮会立即把所有表单组件重置为默认值。从用户体验的角度看，现在认为使用这种按钮是一种糟糕的、应该避免的做法，因为它很容易让用户不小心就丢掉他们已填写的内容。
* 匿名（`button`）按钮就没有固有属性了，你得使用Javascript来给其实现功能。

**`<button>`元素的属性**

| 属性名      | 默认值   |  描述  |
| ----        | ----     |  ----  |
| [type](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/button#attr-type) | submit | 按钮的类型。可能的值有：`button`, `reset`, `submit` |
| [formaction](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/button#attr-formaction) |  | 如果按钮是个提交按钮，该属性的值会重写`<form>`元素的`action`属性 |
| [formentype](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/button#attr-formentype) |  | 如果按钮是个提交按钮，该属性的值会重写`<form>`元素的`enctype`属性 |
| [formmethod](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/button#attr-formmethod) |  | 如果按钮是个提交按钮，该属性的值会重写`<form>`元素的`method`属性 |
| [formnovalidate](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/button#attr-formnovalidate) |  | 如果按钮是个提交按钮，该属性的值会重写`<form>`元素的`novalidate`属性 |
| [formtarget](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/button#attr-formtarget) |  | 如果按钮是个提交按钮，该属性的值会重写`<form>`元素的`target`属性 |

从技术上讲，用`<button>`或`<input>`元素定义的按钮间基本是没有区别的。只在按钮的标记上有一个值得注意的区别：在`<input>`元素内标记只能是字符数据；而在`<button>`元素内，标记的内容可以是HTML，所以此处按钮的样式可以被按需定制。

>**注意**：因为一些历史原因，`<button>`元素一直不被大量使用，且许多表单开发者更倾向于使用由`<input>`构建的按钮。而罪魁祸首就是旧版本IE的一个bug，在IE6和IE7中，若你给`<button>`元素加了`name`和`value`属性，表单就不会发送该`value`属性而是直接发送按钮的内容。但这个bug已经在IE8之后给修复了，所以现在不再有什么理由来回避使用`<button>`元素了。

### 公共属性
许多用来定义表单组件的元素拥有一些自己的属性，而这里还有一个所有表单元素的公共属性汇总，它们能给你跨组件的控制能力。下面就是这些公共属性的列表：

| 属性名      | 默认值   |  描述  |
| ----        | ----     |  ----  |
| autofocus | (false) | 这个布尔属性让你能够指定在页面加载时需要自动获得输入焦点的元素，除非用户重写了它，比如在另一控件上输入。在一个文档中，只能有唯一一个表单元素能指定该值。 |
| disabled | (false) | 这个布尔属性表明用户不能与某个元素交互。如该属性未指定，则会继承自容器元素如`<fieldset>`的设置；若无容器元素设置了`disabled`属性，则该元素就是可用的 |
| form |  | 和组件关联的form元素。这个属性的值必须是同一文档下某`<form>`元素的`id`值。理论上，这能让你在`<form>`元素外设置表单组件。但在实际应用中，并没有浏览器支持该特性。 |
| name |  | 元素的名字，该属性会随表单数据提交 |
| value |  | 指定元素初始值 |

## 使用ARIA来构建HTML表单
[ARIA](https://developer.mozilla.org/en-US/docs/Accessibility/ARIA)是一项[W3C候选推荐标准](http://www.w3.org/TR/wai-aria/)，用于添加到HTML以增强富Internet应用的可访问性。我们将在[怎样创建定制表单组件]()一文中更具体地讨论它的使用，但这里有一些基础知识需要先知道。

在开始之前，要注意浏览器上对ARIA和无障碍技术的支持虽然还远称不上完美，但也已在持续改进之中了。出现这种问题就是因为，浏览器在遇到ARIA属性时，必须给操作系统的无障碍访问层发送消息，而并不是所有浏览器都能跨平台地做好这点。同时无障碍设备自身只能链接到操作系统的无障碍访问层去处理浏览器传来的消息。让人吃惊的是，有些无障碍设备连这点都做不好。

所以使用无障碍技术并不意味着你的web应用就是无障碍的，而是意味着你已经尽力来做到这点了。暂时来讲，ARIA只是尽最大努力的技术，但还是聊胜于无吧。

若你还想深入了解ARIA和HTML表单的结合使用，请自行阅读[ARIA文档的相关章节](https://developer.mozilla.org/en-US/docs/Accessibility/ARIA/forms)。

### [aria-labelledby](https://developer.mozilla.org/en-US/docs/Accessibility/ARIA/ARIA_Techniques/Using_the_aria-labelledby_attribute)属性
该属性便于在不适用`<label>`元素时定义一个label。可将其放在组件元素上并引用要作为label的元素的`id`属性。

```html
<form>
  <p id="fruitLabel">What's your favorite fruit</p>
  <p>
    <input type="text" name="fruit" aria-labelledby="fruitLabel">
  </p>
</form>
```

从概念上，这个属性与`<label>`元素的`for`属性正好相反。使用`for`属性时引用的是组件的`id`，而使用`aria-labelledby`时则引用label的`id`。

### [aria-describedby](https://developer.mozilla.org/en-US/docs/Accessibility/ARIA/ARIA_Techniques/Using_the_aria-describedby_attribute)属性
该属性和`aria-labelledby`作用类似，主要就是语义上有不同。label用于定义对象的本质内容，而描述则提供更多用户可能要用到的信息。这个属性不建议直接用在表单元素上；最好还是使用`aria-labelledby`属性，除非你想为当前元素提供额外信息。毕竟该属性就是用来提供大段描述的。

### [aria-label](https://developer.mozilla.org/en-US/docs/Accessibility/ARIA/ARIA_Techniques/Using_the_aria-label_attribute)属性
该属性用于给定的组件在DOM中没有明确的label时，它能让你提供一个能通过无障碍设备使用的组件，而无需专门为其创建一个节点来做到这点。

```html
<form>
  <p>
    <input type="search" name="q" aria-label="Search" />
    <input type="submit" value="Go" />
  </p>
</form>
```

### [role](https://developer.mozilla.org/en-US/docs/Accessibility/ARIA/ARIA_Techniques)属性
这是最重要的一个ARIA属性了，它能让你为给定的HTML元素提供无障碍设备可理解的、特定的语义信息。现在已经有不少可用的role了，且其中一些是表单专用的。

ARIA致力于给那些在HTML中尚不可用的组件、或已经存在的元素提供语法，我们将会在[怎样创建定制表单组件]()一文中看到如何使用这些role。

用于表单组件的role有：
* [Button](https://developer.mozilla.org/en-US/docs/Accessibility/ARIA/ARIA_Techniques/Using_the_button_role)
* [Checkbox](https://developer.mozilla.org/en-US/docs/Accessibility/ARIA/ARIA_Techniques/Using_the_checkbox_role)
* [Progressbar](https://developer.mozilla.org/en-US/docs/Accessibility/ARIA/ARIA_Techniques/Using_the_progressbar_role)
* Radio
* [Slider](https://developer.mozilla.org/en-US/docs/Accessibility/ARIA/ARIA_Techniques/Using_the_slider_role)
* Spinbutton
* [textbox](https://developer.mozilla.org/en-US/docs/Accessibility/ARIA/ARIA_Techniques/Using_the_textbox_role)

值得注意的还有几个复合的role：
* [Listbox](https://developer.mozilla.org/en-US/docs/Accessibility/ARIA/ARIA_Techniques/Using_the_listbox_role)
* Radiogroup

若你觉得这些role很好用，可在这里了解更多；ARIA是个很庞大的规范，深入探索它能帮你为远超HTML表单之外的领域增强无障碍性。

## 结论
现在你已经拥有了所有可用于构建你的HTML表单的知识；下篇文章将会深入其实现细节和功能需求：[原生表单组件]()。

## 参见
* [A List Apart: Sensible Forms: A Form Usability Checklist](http://www.alistapart.com/articles/sensibleforms/)
