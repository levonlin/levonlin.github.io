title: DOM元素的属性（property）和特性（attribute）
date: 2016-09-11 17:29:50
tags: [前端,DOM,javascript,html]
categories: [前端]
---
## 从jQuery的`prop()`和`attr()`方法说开去
jQuery中有两个获取DOM元素属性的方法：`prop()`和`attr()`，看似可以互相替换，但若搞不清这两个方法获取的到底是什么“属性”，有时就会出现令人困惑的结果。
官方文档中，用了下面的例子来解释这两者的不同：

```html
<input type="checkbox" checked="checked" />
```

| 操作 | 结果 |
| --- | --- |
| elem.checked | true (Boolean) Will change with checkbox state |
| $( elem ).prop( "checked" ) | true (Boolean) Will change with checkbox state
| elem.getAttribute( "checked" )  | "checked" (String) Initial state of the checkbox; does not change |
| $( elem ).attr( "checked" ) (1.6) | "checked" (String) Initial state of the checkbox; does not change |
| $( elem ).attr( "checked" ) (1.6.1+) | "checked" (String) Will change with checkbox state |
| $( elem ).attr( "checked" ) (pre-1.6) | true (Boolean) Changed with checkbox state |

我们可以看到，使用`prop()`当于直接使用了DOM元素对象的属性，而`attr()`相当于使用了DOM元素的`getAttribute()`或`setAttribute()`方法（而1.6前和1.6后的效果比较特殊，下面会解释）。这两种不同的实现，决定了两种方法获取到的值并不一样，这就得涉及到DOM元素属性（property）和特性（attribute）间的区别了。


## 属性（property）和特性（attribute）
我们知道DOM的实现就是把一个HTML文档映射为一棵DOM树，而DOM树上的每个节点其实就是一个javascript对象。所以DOM元素属性和普通对象的属性一样，就是DOM对象这个javascript对象上的属性而已，我们可以直接在DOM对象上通过`.`或`[]`来获取和设置它们，比如：

```html
<form>
    <input type="text" name="form-item" value="value0">
</form>
```

```javascript
var formItem = document.querySelector('[name=from-item]');
console.log(formItem.value); /* value0 */
formItem.value = 'value1'; /* 文本框显示“value1”，但此时HTML中的value特性仍为"value0"  */
console.log(formItem.value); /* value1 */
```

而同时在DOM元素节点之外，还有其他类型的节点，比如文本节点、注释节点、还有我们要讨论的特性节点（Attr节点）等等；这些节点当然也是DOM树上的一个js对象。要操作特性节点，可以在DOM元素上通过`getAttribute()`、`setAttribute()`、`removeAttribute()`等方法来实现，或者用`attributes`属性获取特性节点集合再作操作：

```javascript
console.log(formItem.getAttribute('value')); /* value0，不受上面属性设置的影响，仍保持页面加载后html中的值 */

formItem.setAttribute('value', 'value2'); /* 文本框仍显示“value1”，但此时HTML中的value特性已被改为"value2" */
console.log(formItem.getAttribute('value')); /* value2 */
console.log(formItem.value); /* value1，不随特性节点的改变而改变 */
```

上面的例子中，尤其要区分两点：
* 属性和特性两者不是一一对应的：Attr节点对应的就是HTML各标签中的特性，这些特性有的未必会被内置为DOM元素的属性，比如HTML5的`data-*`特性等自定义特性；而DOM元素的属性也未必都是HTML中的特性，比如一些DOM元素的操作方法
* 即使特性节点名和DOM元素的属性名一致，这两者的操作和行为也是不同的：
    * DOM元素的属性是DOM对象原生实现的，符合一般对象属性的行为；这些属性操作和同名的HTML特性节点无关，但可以在显示上覆盖HTML特性节点的设置
    * 对于特性节点的操作都是针对HTML文档上的特性；对特性的操作不会改变同名属性值，只是改变HTML的文档内容而已

所以我们可以得到属性和特性的根本区别了：

**DOM元素的属性（property）是该对象所拥有的属性，而特性（attribute）则是该元素在HTML中的所拥有的特性节点。property是对象属性，本身不操作特性节点，但可以覆盖HTML中的同名特性的效果，是js操作；attribute是DOM节点对象，只用于获取和设置HTML特性，是文本操作。**

## 用处
通过上面的讨论，既然属性和特性有根本性区别，那也就意味着他们的应用场合不太一样、也不能混淆：
* property的操作是纯js操作，用于获取和设置原生的一些特性；并且对于事件属性如`onclick`可以获得处理函数、对于`style`属性可以获得一个对象。
* attribute的操作是文本操作，用于获取和设置HTML文档中的特性内容，注意这些内容都是字符串形式；同时它能操作的特性也不只是原生限定的那几种，对于一些拓展特性如`data-*`也可以操作。

回到一开始举的jQuery的例子，`elem.checked`由于是属性操作，所以可以返回一个布尔值。而`elem.getAttribute( "checked" )`由于是针对HTML特性的文本操作，所以返回的就是一个字符串，当然这个字符串的值也未必是"checked"，而是由HTML中指定的值决定的。

至于为什么`attr`方法会在jQuery 1.6之前的版本、1.6本身和1.6之后出现各种不一致的行为呢？首先`prop`方法是在1.6版本之后引入的，之前只有`attr`时，该方法对返回属性是还是特性没有作严格区分；而在1.6版本开始会明确让`attr`返回特性、`prop`负责处理属性，同时1.6后为考虑向后兼容，`attr`返回特性会随属性的改变而改变，但返回的已经只能是“特性形式”的字符串了。