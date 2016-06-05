title: 遍历DOM元素的children属性遇到的坑
date: 2016-02-26 18:40:18
tags: [javascript,前端]
categories: 前端
---
## 问题的引出
关于DOM元素的`children`属性，以前我只在意它和`childNodes`属性的区别：即`children`属性只会返回子元素节点集合，而`childNodes`返回的就不止元素节点，还有文本节点等所有子节点集合。这样看来，`children`似乎是我们获取子元素而舍弃其他类型的子节点的最佳选择，虽然说在IE8-的浏览器下用它还会返回注释节点，但兼容起来也是很简单的。

我们知道，`children`返回的子元素集合实际上是一个类似数组的HTMLCollection对象，那接下来我们要获取每个子元素自然要遍历它咯，但是一遍历，问题就出来了：

```html
    <div id="ul">
        <div id='i'></div>
        <div id="ii"></div>
        <div></div>
    </div>

    <script>
        o=document.getElementById('ul').children;
        for (i in o) {
            console.log(o[i]);
        }
    </script>
```

上面的代码使用了`for-in`进行遍历，但我们预料中的结果并未出现，以chrome为例，运行结果是这个：

![](http://ww2.sinaimg.cn/large/85ad0d9cjw1f1d5s4skb0j207y04cjrm.jpg)

这就是我在前面的[原生js练习题-第一课](http://levonlin.info/2016/02/17/%E5%8E%9F%E7%94%9Fjs%E7%BB%83%E4%B9%A0%E9%A2%98-%E7%AC%AC%E4%B8%80%E8%AF%BE/)那篇文章中提到过的坑了，其中有两个奇怪的问题：
1. 多返回了`length`等几个在数组应该是不可枚举的属性。
2. 把有id的元素重复了两次。

## 关于问题1
先说一下，上面提到的第一点在各个浏览器里情况都相同，而第二点关于返回的元素是否重复在各浏览器下情况还不同。

我们先讨论第一点，这里要考虑`for-in`循环遍历对象时的规则比较奇葩：对象自身和继承到的可枚举属性都会被遍历到。所以为确定多遍历到的内容到底是自身还是原型上的属性，我们来验证一下：

```javascript
    console.log(Object.keys(o)); //["0","1","2","i","ii"]
    console.log(Object.getOwnPropertyNames(o)); //["0","1","2","i","ii"]
```

`Object.keys()`方法返回的是可枚举的自身属性的属性名组成的数组，而`Object.getOwnPropertyNames()`返回的是所有自身属性的属性名组成的数组（含可枚举和不可枚举）。在这里我们没有看到`length`、`item()`、`namedItem()`三个属性的身影，由此断定他们不是HTMLCollection对象自身的属性，但既然能被`for-in`遍历到那就只能是来自HTMLCollection原型的可枚举属性。我们可以用`Object.getOwnPropertyDescriptor()`来验证其在原型上的可枚举性：

```javascript
    console.log(Object.getOwnPropertyDescriptor(o.__proto__, 'length').enumerable); //true
    console.log(Object.getOwnPropertyDescriptor(o.__proto__, 'item').enumerable); //true
    console.log(Object.getOwnPropertyDescriptor(o.__proto__, 'namedItem').enumerable); //true
```

## 关于问题2
解决了多出来的三个属性的来源，我们再回过头看看为什么会把有id的元素重复了两次。观察用`Object.keys()`方法返回的数组，这两次一次用下标做属性名、一次用id名作属性名。但其实两个属性名指向的是同一个对象：

```javascript
    o[0]===o['i'] //true
    o[1]===o['ii'] //true
```

可见之所以`for-in`会把id的元素重复遍历两次，不是因为有id的元素都添加进HTMLCollection对象两次，只是一个元素有了两个属性名而已，这是chrome的情况（我的版本是48.0.2564.116 m），但放到火狐和IE下结果却还有点所不同：

```javascript
    //FF
    console.log(Object.keys(o)); //["0", "1", "2"]
    console.log(Object.getOwnPropertyNames(o)); // ["0", "1", "2", "i", "ii"]
    o[0]===o['i'] //true
    o[1]===o['ii'] //true

    //IE11
    console.log(Object.keys(o)); //["i", "ii", "2"]
    console.log(Object.getOwnPropertyNames(o)); // ["i", "ii", "2"]
    o[0]===o['i'] //true
    o[1]===o['ii'] //true
```

可见虽然不同的浏览器返回的HTMLCollection对象都存在有id的子元素有两个属性名的情况，但从`Object.keys(o)`的结果看，火狐和IE对同一元素默认只取一个属性名。所以如果你在火狐或IE运行一开始那段代码，就会发现`for-in`遍历时火狐和IE也只对同一元素访问一次，不会像chrome那样重复遍历。然而我们还看到，这两个浏览器间选取的属性名也不同，火狐优先选择下标形式，而IE优先选择id形式，同时由`Object.getOwnPropertyNames(o)`的结果我们还可以窥探出火狐实现选取属性名的机制可能是通过将id形式的属性名设为不可枚举来实现的，至于IE就不清楚了。

## 小结
这下我们可以得出结论了：`children`个属性返回的HTMLCollection对象不止能遍历到子元素，还能遍历到来自其原型的`length`、`item()`、`namedItem()`三个属性。而且一旦遍历到的子元素有id，就存在HTMLCollection对象里一个元素会有两个属性名的问题，更让人蛋疼的是各浏览器对这两个属性名的选取各不相同。当然，最根本原因还是因为`children`属性现在还没被正式纳入标准，在使用这种非标准属性时我们难免遇到一些奇葩的状况。

所以这也告诫我们，如果对一个非标准属性的特点不是特别了解，还是不要轻易使用它，否则出现的问题往往是你难以控制的。但如果你还是觉得`children`使用起来方便，那在使用时就得谨记这些问题，比如在遍历子元素时最好放弃`for-in`循环，老老实实使用基本的for循环去遍历数字索引吧，这样就和遍历数组差不多，不会遍历到那些多出来的属性了。

而至于`for-in`，最好只用来遍历数组或简单的对象。既要防止那些添加、修改了原型属性的对象遍历出多余的的结果，也要防止类似`children`这种非标准属性返回一个属性的枚举性不可控的对象的坑。
