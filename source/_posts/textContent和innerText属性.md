title: textContent和innerText属性
date: 2016-04-27 19:22:10
tags: [javascript,前端]
categories: 前端
---
在DOM操作中，我们如果要获取和修改节点的的文本，就离不开`textContent`或`innerText`属性。其中`textContent`作为标准方法，但无奈要IE8+；而`innerText`属性最近也被火狐支持了，已经成了不是标准的标准（[参考](http://caniuse.com/#search=innerText)）。也就是说，如果只考虑最新浏览器的话这两个方法都是可以使用的，那他们到底使用时有何不同呢？先上代码：

```html
    <div id="test" class="test">test<div style="display:none"><span>test</span></div><style>hi</style></div> 
    <script type="text/javascript">
        var testDiv = document.getElementById('test');
        console.log(testDiv.textContent);
        console.log(testDiv.innerText);
    </script>
```

运行上述代码，结果为：

可看到存在两个差异：
1. `textContent`会获取`style`元素里的文本（若有`script`元素也是这样），而`innerText`不会
2. `textContent`会获取`display:none`的节点的文本；而`innerText`好像会感知到节点是否呈现一样，不作返回

也就是说，`textContent`能够获取元素的所有子节点上的文本，不管这个节点是否呈现；而`innerText`只返回呈现到页面上的文本。

当然，除了获取文本，这两个元素也可以设置元素的文本内容，不过要注意设置文本时被替换的可不只是文本了；这时，两个属性作用相当于`innerHTML`属性，会把指定节点下的所有子节点也一并替换掉。



