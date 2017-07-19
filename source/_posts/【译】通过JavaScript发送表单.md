title: 【译】通过JavaScript发送表单
date: 2017-06-21 15:40:19
tags: [html,前端,javascript,翻译]
categories: 前端
---
> * [系列文章说明](http://levonlin.info/2016/04/28/%E3%80%90%E8%AF%91%E3%80%91HTML%E8%A1%A8%E5%8D%95%E6%8C%87%E5%8D%97-%E7%AC%AC%E4%B8%80%E4%B8%AAHTML%E8%A1%A8%E5%8D%95/)
> * [原文](https://developer.mozilla.org/en-US/docs/Learn/HTML/Forms/Sending_forms_through_JavaScript)

在[发送表单数据](http://levonlin.info/2016/09/24/%E3%80%90%E8%AF%91%E3%80%91%E5%8F%91%E9%80%81%E8%A1%A8%E5%8D%95%E6%95%B0%E6%8D%AE/)一文中，HTML表单可以声明式地发送一个[HTTP](https://developer.mozilla.org/en-US/docs/HTTP)请求。但表单也可以用JavaScript来准备一个HTTP请求。本文将探索如何做到这点。

## 表单，不只是表单
随着[开放式Web应用](https://developer.mozilla.org/en-US/docs/Archive/B2G_OS/Quickstart/Intro_to_open_web_apps)的出现，现在提供[HTML表单](https://developer.mozilla.org/en-US/docs/HTML/Forms)、而不是文字表单供用户填写的做法，已经越发普遍了。而越来越多的开发者也获得了数据传输的控制权。

### 获得对数据传输的控制权
标准的HTML表单提交操作会加载数据发送到的URL，这就意味着浏览器的窗口中会进行整个页面的重新加载。而如果避免了页面的重新加载，就会避免页面的闪烁和网络延迟，进而提供更顺畅的用户体验。

在许多现代的UI设计中，HTML表单只是用来收集用户的输入。当用户要发送数据时，Web应用会进行控制，并在后台异步地发送数据，只更新UI中需要更改的部分。

能异步地发送任意数据的技术称为[AJAX](https://developer.mozilla.org/en-US/docs/AJAX)，表示“异步的JavaScript和XML”。

### 和传统表单处理的不同
AJAX使用了[XMLHttpRequest](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest)(XHR)DOM对象，它可以建立HTTP请求、发送请求并处理结果。

> **注意：** 老的AJAX技术可能不是用XMLHttpRequest。比如[JSONP](http://en.wikipedia.org/wiki/JSONP)和[eval()](https://developer.mozilla.org/en-US/docs/Learn/HTML/Forms/Sending_forms_through_JavaScript)函数结合起来使用。虽然该方法可行，但不推荐使用它，因为其存在严重的安全问题。所以除非为了兼容那些特别老旧、不支持XMLHttpRequest或[JSON](https://developer.mozilla.org/en-US/docs/JSON)的浏览器，还是**避免使用该技术**。

由于历史原因，XMLHttpRequest本是设计用来获取和发送交换格式为[XML](https://developer.mozilla.org/en-US/docs/XML)的数据的。但现在JSON取代了XML，有着更普遍的使用。

不过XML和JSON都不符合作为表单数据请求的编码。表单数据（`application/x-www-form-urlencoded`）是用于构造键值对的URL编码列表的，若是要传输二进制数据，HTTP请求会被重塑为` multipart/form-data`。

若你能掌控前端（运行在浏览器上的代码）和后端（运行在服务器上的代码），你就能发送JSON或XML、并随心所欲地处理它们。

但如果你使用的是第三方服务，这就没那么容易了，因为某些服务只接受表单数据。当然也有使用表单数据处理起来更方便的情况，比如数据是键值对或二进制数据时，用现成的后端工具就能处理它们、不需要额外的代码。

那么，具体该如何发送数据呢？

## 发送表单数据
目前有三种方式来发送表单数据，既有老旧的技术、也有新特性[FormData](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest/FormData)对象，接下来就来深入了解下它们。

### 在隐藏的iframe中构建DOM
发送表单数据最古老的方法，是用DOM API建立一个表单，然后发送数据到一个隐藏的`<iframe>`。为了访问你提交内容的处理结果，应检索下`<iframe>`的内容。

> **警告：避免使用该技术。**在使用第三方服务时，该技术有安全风险，因为它会导致你面临[脚本注入攻击](http://en.wikipedia.org/wiki/Cross-site_scripting)。若你使用HTTPS，使用`<ifrme>`发送表单还会影响[同源策略](https://developer.mozilla.org/en-US/docs/JavaScript/Same_origin_policy_for_JavaScript)，并导致内容被发送到一个无法访问的`<ifrme>`中。当然如果你要兼容很老旧的浏览器，这项技术可能就是你唯一的选择了。

下面是一个例子：

```
<button onclick="sendData({test:'ok'})">Click Me!</button>
```

```
// 创建一个iFrame来发送我们的数据
var iframe = document.createElement("iframe");
iframe.name = "myTarget";

// 将iFrame添加到文档流中
window.addEventListener("load", function () {
  iframe.style.display = "none";
  document.body.appendChild(iframe);
});

// 用来发送数据的函数
// 需要一个参数，是一个由键值对组成的对象
function sendData(data) {
  var name,
      form = document.createElement("form"),
      node = document.createElement("input");

  // 定义响应加载时的行为
  iframe.addEventListener("load", function () {
    alert("Yeah! Data sent.");
  });
    
  form.action = "http://www.cs.tut.fi/cgi-bin/run/~jkorpela/echo.cgi";
  form.target = iframe.name;

  for(name in data) {
    node.name  = name;
    node.value = data[name].toString();
    form.appendChild(node.cloneNode());
  }

  // 要发送数据，表单得添加到文档流中
  form.style.display = "none";
  document.body.appendChild(form);

  form.submit();

  // 表单一发送就移除它
  document.body.removeChild(form);
}
```

结果如下：
[效果](https://mdn.mozillademos.org/en-US/docs/Learn/HTML/Forms/Sending_forms_through_JavaScript$samples/Building_a_DOM_in_a_hidden_iframe?revision=1226811)

### 手动创建一个XMLHttpRequest
XMLHttpRequest是发送HTTP请求最安全和可靠的方式。要想用XMLHttpRequest发送表单数据，得先用URL编码要发送的数据，并遵循表单数据请求的规范。

> **注意：** 若想了解更多关于XMLHttpRequest，这几篇文章可能对你有用：[An introductory article to AJAX](https://developer.mozilla.org/en-US/docs/AJAX/Getting_Started)，以及一个关于[使用XMLHttpRequest](https://developer.mozilla.org/en-US/docs/DOM/XMLHttpRequest/Using_XMLHttpRequest)的高级教程。

来重构下我们先前的例子：

```
<button type="button" onclick="sendData({test:'ok'})">Click Me!</button>
```

如你所见，HTML部分并未真的有所改变，但JavaScript部分就完全不同了：

```
function sendData(data) {
  var XHR = new XMLHttpRequest();
  var urlEncodedData = "";
  var urlEncodedDataPairs = [];
  var name;

  // 将data对象转为一个URL编码的键值对数组
  for(name in data) {
    urlEncodedDataPairs.push(encodeURIComponent(name) + '=' + encodeURIComponent(data[name]));
  }

  // 将键值对组合成一个字符串，并把所有经过URL编码的空格替换为'+'号；以符合浏览器表单提交的行为
  // the '+' character; matches the behaviour of browser form submissions.
  urlEncodedData = urlEncodedDataPairs.join('&').replace(/%20/g, '+');

  // 定义成功的数据提交后会发生什么
  XHR.addEventListener('load', function(event) {
    alert('Yeah! Data sent and response loaded.');
  });

  // 定义失败的情况会发生什么
  XHR.addEventListener('error', function(event) {
    alert('Oups! Something goes wrong.');
  });

  // 配置请求
  XHR.open('POST', 'https://example.com/cors.php');

  // 添加表单数据POST请求所需的HTTP请求头
  XHR.setRequestHeader('Content-Type', 'application/x-www-form-urlencoded');

  // 最后，发送数据
  XHR.send(urlEncodedData);
}
```

结果如下：
[效果](https://mdn.mozillademos.org/en-US/docs/Learn/HTML/Forms/Sending_forms_through_JavaScript$samples/Building_an_XMLHttpRequest_manually?revision=1226811)

> **注意：** 若你想使用此处的XMLHttpRequest方法发送数据到第三方网站，也会受到同源策略的限制。要实现跨域请求，还需要[CORS和HTTP访问控制](https://developer.mozilla.org/en-US/docs/HTTP/Access_control_CORS)。

### 使用XMLHttpRequest和FormData对象
手动构建HTTP请求是挺麻烦的，幸运的是，最近的一项[XMLHttpRequest标准](http://www.w3.org/TR/XMLHttpRequest/)提供了一个处理表单请求的简便方法--FormData对象。

FormData对象可被用来建立要传输的表单数据，或者从表单元素中获取数据来管理如何发送。要注意FormData对象是“只写”的，这意味着你可以改变它们、但不能访问它们的内容。

使用FormData对象的方法会在[使用FormData对象](http://levonlin.info/2017/07/15/%E3%80%90%E8%AF%91%E3%80%91%E4%BD%BF%E7%94%A8FormData%E5%AF%B9%E8%B1%A1/)一文中详细介绍，下面有两个例子：

#### 使用独立的FormData对象
```
<button type="button" onclick="sendData({test:'ok'})">Click Me!</button>
```

现在你该很熟悉这个HTML的样子了。

```
function sendData(data) {
  var XHR = new XMLHttpRequest();
  var FD  = new FormData();

  // 将数据添加到FormData对象中
  for(name in data) {
    FD.append(name, data[name]);
  }

  // 定义成功的数据提交后会发生什么
  XHR.addEventListener('load', function(event) {
    alert('Yeah! Data sent and response loaded.');
  });

  // 定义失败的情况会发生什么
  XHR.addEventListener('error', function(event) {
    alert('Oups! Something went wrong.');
  });

  // 配置请求
  XHR.open('POST', 'https://example.com/cors.php');

  // 发送FormData对象，HTTP头会自动设置
  XHR.send(FD);
}
```

结果如下：
[效果](https://mdn.mozillademos.org/en-US/docs/Learn/HTML/Forms/Sending_forms_through_JavaScript$samples/Using_a_standalone_FormData_object?revision=1226811)

#### 将FormData绑到表单元素上使用
你也可以将FormData对象绑定到一个表单元素上，这样做会创建一个FormData对象来表示表单中的数据。

HTML是典型的表单：

```
<form id="myForm">
  <label for="myName">Send me your name:</label>
  <input id="myName" name="name" value="John">
  <input type="submit" value="Send Me!">
</form>
```

但JavaScript会接管表单的提交操作：

```
window.addEventListener("load", function () {
  function sendData() {
    var XHR = new XMLHttpRequest();

    // 绑定FormData对象和表单元素
    var FD = new FormData(form);

    // 定义成功的数据提交后会发生什么
    XHR.addEventListener("load", function(event) {
      alert(event.target.responseText);
    });

    // 定义失败的情况会发生什么
    XHR.addEventListener("error", function(event) {
      alert('Oups! Something goes wrong.');
    });

    // 配置请求
    XHR.open("POST", "https://example.com/cors.php");

    // 发送的数据是用户在表单中提供的
    XHR.send(FD);
  }
 
  // 访问表单元素
  var form = document.getElementById("myForm");

  // 并接管其submit事件
  form.addEventListener("submit", function (event) {
    event.preventDefault();

    sendData();
  });
});
```

结果如下：
[效果](https://mdn.mozillademos.org/en-US/docs/Learn/HTML/Forms/Sending_forms_through_JavaScript$samples/Using_FormData_bound_to_a_form_element?revision=1226811)

## 处理二进制数据
若你在一个含有`<input type="file">`组件的表单中使用FormData对象，那么数据会被自动处理。但要手动发送二进制数据的话，还有很多额外工作要做。

现代Web有很多二进制数据源：比如[FileReader](https://developer.mozilla.org/en-US/docs/Web/API/FileReader)、[Canvas](https://developer.mozilla.org/en-US/docs/Web/API/HTMLCanvasElement)、[WebRTC](https://developer.mozilla.org/en-US/docs/WebRTC/navigator.getUserMedia)。但不幸的是，某些老旧浏览器不能访问二进制数据或者需要复杂的方法才能实现。这些遗留的问题已不在本文讨论范围之内。若你想了解更多关于FileReader API，请阅读[Using files from web applications](https://developer.mozilla.org/en-US/docs/Using_files_from_web_applications)。

用FormData来发送二进制是很直接的，使用`append()`方法就好了。但要手动做到这点，就需要一些技巧了。

在下面的例子中，我们会用来FileReader API来访问二进制数据，然后手动建立多部分的表单数据请求。

```
<form id="myForm">
  <p>
    <label for="i1">text data:</label>
    <input id="i1" name="myText" value="Some text data">
  </p>
  <p>
    <label for="i2">file data:</label>
    <input id="i2" name="myFile" type="file">
  </p>
  <button>Send Me!</button>
</form>
```

如你所见，HTML用了标准的`<form>`，这没什么神奇的。“神奇”的部分在JavaScript里：

```
// 由于我们要访问DOM结点，所以得在页面加载完后才初始化脚本
window.addEventListener('load', function () {

  // 这些变量用来存储表单数据
  var text = document.getElementById("i1");
  var file = {
        dom    : document.getElementById("i2"),
        binary : null
      };
 
  // 使用FileReader API来访问文件内容
  var reader = new FileReader();

  // 由于FileReader是异步的，所以得在其完成文件读取后才存储结果
  reader.addEventListener("load", function () {
    file.binary = reader.result;
  });

  // 在页面加载时，若已经选择了文件就直接读取它
  if(file.dom.files[0]) {
    reader.readAsBinaryString(file.dom.files[0]);
  }

  // 否则在用户选择文件时再读取它
  file.dom.addEventListener("change", function () {
    if(reader.readyState === FileReader.LOADING) {
      reader.abort();
    }
    
    reader.readAsBinaryString(file.dom.files[0]);
  });

  // sendData是本例的主要函数
  function sendData() {
    // 若已经选择了文件，就等浏览器读取完
    // 否则就延迟本函数的执行
    if(!file.binary && file.dom.files.length > 0) {
      setTimeout(sendData, 10);
      return;
    }

    // 要构建多部分的表单数据请求，需要一个XMLHttpRequest实例
    var XHR = new XMLHttpRequest();

    // 需要一个分隔符来定义请求体的每部分
    var boundary = "blob";

    // 将请求体存为一个字符串
    var data = "";

    // 若用户选择了文件
    if (file.dom.files[0]) {
      // 开启请求体的新部分
      data += "--" + boundary + "\r\n";

      // 该部分描述为表单数据
      data += 'content-disposition: form-data; '
      // 定义表单数据的名字
            + 'name="'         + file.dom.name          + '"; '
      // 提供真实的文件名
            + 'filename="'     + file.dom.files[0].name + '"\r\n';
      // 提供文件的MIME类型
      data += 'Content-Type: ' + file.dom.files[0].type + '\r\n';

      // 元数据和真实数据部分间有一个空行
      data += '\r\n';
      
      // 往请求体里添加二进制数据
      data += file.binary + '\r\n';
    }

    // 文本数据的组织更加简单
    // 开启请求体的新部分
    data += "--" + boundary + "\r\n";

    // 描述为表单数据并命名
    data += 'content-disposition: form-data; name="' + text.name + '"\r\n';
    // 元数据和真实数据部分间有一个空行
    data += '\r\n';

    // 往请求体里添加文本数据
    data += text.value + "\r\n";

    // 完成了所有部分，就“闭合”请求体
    data += "--" + boundary + "--";

    // 定义成功的数据提交后会发生什么
    XHR.addEventListener('load', function(event) {
      alert('Yeah! Data sent and response loaded.');
    });

    // 定义失败的情况会发生什么
    XHR.addEventListener('error', function(event) {
      alert('Oups! Something went wrong.');
    });

    // 配置请求
    XHR.open('POST', 'https://example.com/cors.php');

    // 添加必要的HTTP请求头来处理多部分表单数据的POST请求
    XHR.setRequestHeader('Content-Type','multipart/form-data; boundary=' + boundary);

    // 最后，发送数据
    XHR.send(data);
  }

  // 访问表单
  var form = document.getElementById("myForm");

  // 接管submit事件
  form.addEventListener('submit', function (event) {
    event.preventDefault();
    sendData();
  });
});
```

结果如下：
[效果](https://mdn.mozillademos.org/en-US/docs/Learn/HTML/Forms/Sending_forms_through_JavaScript$samples/Dealing_with_binary_data?revision=1226811)

## 结论
浏览器的不同，导致通过JavaScript发送表单数据可以简单或很困难。FromData对象是通常的解决方案，而且我们应该毫不犹豫地在老旧浏览器上使用其polyfill：

* [这个gist](https://gist.github.com/3120320)用Web Workers来提供FromData
* [HTML5-formdata](https://github.com/francois2metz/html5-formdata)致力于提供FromData对象的polyfill，但这得依赖[File API](http://www.w3.org/TR/FileAPI/)
* [这个polyfill](https://github.com/jimmywarting/FormData)提供了FromData的大部分新方法（entries, keys, values和对for...of的支持）
