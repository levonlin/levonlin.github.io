title: css的样式优先级
date: 2016-04-25 17:48:39
tags: [前端,css]
categories: 前端
---
前几天做百度的笔试题，第一题就是关于css优先级的，虽然知道一些规则，但碰上把各条规则相互组合就懵逼了，所以还是得来好好总结一下的。

## 首要原则
就两条：
1. 优先级高的样式覆盖优先级低的样式
2. 同一优先级的样式，后定义的覆盖先定义的，即后来居上

而至于样式的优先级如何确定，就是接下来要讨论的问题：

## 多重样式间的优先级
[w3school](http://www.w3school.com.cn/css/css_intro.asp)给出的优先级顺序从低到高是：

>1.浏览器缺省设置
>2.外部样式表
>3.内部样式表（位于 <head> 标签内部）
>4.内联样式（在 HTML 元素内部）

但其实外部样式表若放在内部样式表后面其实是会覆盖内部样式表的，举个例子：

```html
    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>多重样式优先级</title>
        <style type="text/css">
            #box {
                width: 100px;
                height: 100px;
                background-color: #000;
            }
        </style>
        <style type="text/css">
            @import url(higher.css);
        </style>
        <link rel="stylesheet" type="text/css" href="wider.css">
    </head>
    <body>
        <div id="box" style="background-color: #f00;"></div>
    </body>
    </html>

---highter.css
    #box {
        height: 200px;
    }

---wider.css
    #box {
        width: 200px;
    }
```

最终得到的是一个宽高都为200px的红色元素，很明显，内部样式被放在后面的外部样式覆盖了。所以我更倾向于认为外部样式表和内部样式表具有相同的优先级，结论如下：

**浏览器缺省设置 < 外部样式表 = 内部样式表 < 内联样式**

## 同一样式表内部的优先级
同一样式表内，样式的优先级主要是由选择器的权重和权重的累加决定的。

首先，参考[这篇文章](http://www.standardista.com/css3/css-specificity/)可知选择器的权重排序为：

>1.通用选择器* 0-0-0
>2.标签选择器，伪元素 0-0-1
>3.类选择器，属性选择器，伪类 0-1-0
>4.ID 选择器 1-0-0

而对于组合上面几种情况的选择器式而言，就需要将所有出现的权重进行相加，取相加后的结果作为权重，注意相加时：

1. **不进位**。因为每位权重间相差的数量级是很大的，在正常使用时不会出现低级选择器靠数量的累加提升为高级权重（虽然在某些情况下确实会发生，比如[256个class选择器可以干掉1个id选择器](http://www.zhangxinxu.com/wordpress/2012/08/256-class-selector-beat-id-selector/)。。。）
2. **无视`+`,`>`,`~`等组合选择器的符号**
3. **`not`伪类不作为伪类计算权重，而是直接计算其参数的权重**。比如下面的代码中`not`伪类的权重要高于类选择器：
```html
    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>not伪类的优先级</title>
        <style type="text/css">
            div:not(#box) {
                width: 100px;
                height: 100px;
                background-color: #0f0;
            }
            div.box {
                width: 100px;
                height: 100px;
                background-color: #f00;
            }
        </style>
    </head>
    <body>
        <div class="box"></div>
    </body>
    </html>
```

除了选择器，样式自身还可以继承和提升优先级，规则如下：

1. 从祖先元素继承来的样式优先级低于通用选择器；甚至低于浏览器的缺省设置，比如最常见的，重置链接的默认样式时必须写在链接元素上，放在祖先元素中是没有卵用滴
2. 使用大杀器`!important`可将样式提升到最高等级，不管这个样式在哪个样式表或选择器中；如果在同一样式中出现了多个`!important`，就得看上面的权重规则进行pk了

是不是看起来有点晕，附上[参考文章](http://www.standardista.com/css3/css-specificity/)里的大鱼吃小鱼的图来辅助理解吧：

![](http://ww1.sinaimg.cn/large/85ad0d9cgw1f398xi1j5yj20xc16iwo1.jpg)

## 最终结论
结合上面的讨论，我们可以得出元素的最终样式是由什么决定的：

* 多重样式间遵循：**继承来的样式 < 浏览器缺省设置 < 外部样式表 = 内部样式表 < 内联样式**
* 而在同一样式表中存在：**通用选择器* 0-0-0 < 标签选择器，伪元素 0-0-1 < 类选择器，属性选择器，伪类 0-1-0 < ID 选择器 1-0-0 << `!important`**，其中对于组合选择器还要用上面提到的方法进行权重累加后才能判断
* 首要原则：高优先级覆盖低优先级，同一优先级则后来居上

最后，要知道了解样式的优先级，最重要的不是为了用来酷炫和装逼，而是能让我们写出更简洁高效的代码，保证在写代码时就知道一定会出来什么样式，而不是等到堆上一堆选择器后样式还出不来时，再一脸懵逼地去排查。
