title: 【译】使用FormData对象
date: 2017-07-15 23:14:58
tags: [html,前端,javascript,翻译]
categories: 前端
---
> * [系列文章说明](http://levonlin.info/2016/04/28/%E3%80%90%E8%AF%91%E3%80%91HTML%E8%A1%A8%E5%8D%95%E6%8C%87%E5%8D%97-%E7%AC%AC%E4%B8%80%E4%B8%AAHTML%E8%A1%A8%E5%8D%95/)
> * [原文](https://developer.mozilla.org/en-US/docs/Web/API/FormData/Using_FormData_Objects)

[FormData](https://developer.mozilla.org/en-US/docs/Web/API/FormData)对象能让你生成一系列用于[XMLHttpRequest](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest)发送的键值对。它主要的目的在于发送表单数据，但也能独立用于传输有键形式的数据。其传输的数据格式和表单使用[submit()](https://developer.mozilla.org/en-US/docs/Web/API/HTMLFormElement/submit)方法、且编码类型为`multipart/form-data`所发送的数据格式相同。

## 从头开始创建一个FormData对象
你可以自己建立一个FormData对象，首先进行实例化、再通过[apppend()](https://developer.mozilla.org/en-US/docs/Web/API/FormData/append)方法来添加字段，如下所示：

```
var formData = new FormData();

formData.append("username", "Groucho");
formData.append("accountnum", 123456); // 数字123456会自动转为字符串"123456"

// 用户选择的HTML的文件提交
formData.append("userfile", fileInputElement.files[0]);

// JavaScript的类文件对象（Blob）
var content = '<a id="a"><b id="b">hey!</b></a>'; // 新文件的主体
var blob = new Blob([content], { type: "text/xml"});

formData.append("webmasterfile", blob);

var request = new XMLHttpRequest();
request.open("POST", "http://foo.com/submitform.php");
request.send(formData);
```

>**注意：**“userfile”字段和“webmasterfile”字段都包含了文件。“accountnum”字段中的数字会自动被FormData.append()方法自动转为字符串。（字段值可以是一个[Blob](https://developer.mozilla.org/en-US/docs/Web/API/Blob)、[File](https://developer.mozilla.org/en-US/docs/Web/API/File)或字符串，**若值不是Blob、File之一，就会被转为字符串**）

上面的例子建立了一个包含名为“username”、“accountnum”、“userfile”和“webmasterfile”字段的FormData对象。然后使用了XMLHttpRequest的[send()](https://developer.mozilla.org/en/DOM/XMLHttpRequest#send()方法来发送表单数据。“webmasterfile”字段是个Blob对象，该对象代表了一个由不可变的、原始数据组成的类文件对象，里面的的数据不必使用JavaScript的原生数据类型。File对象也是基于Blob对象的，它继承了Blob的基本功能并做了拓展以支持用户操作系统上的文件。要建立一个Blob对象，你可以使用[Blob构造函数](https://developer.mozilla.org/en-US/docs/Web/API/Blob/Blob)。

## 用HTML表单中创建一个FormData对象
要用现有的`<form>`元素建立一个FormData对象，可以在建立FormData对象时传入指定的`<form>`元素。

```
var formData = new FormData(someFormElement);
```

举个例子：

```
var formElement = document.querySelector("form");
var request = new XMLHttpRequest();
request.open("POST", "submitform.php");
request.send(new FormData(formElement));
```

在用`<form>`元素建立FromData对象之后、发送它之前，也可以给它添加额外的数据，如下：

```
var formElement = document.querySelector("form");
var formData = new FormData(formElement);
var request = new XMLHttpRequest();
request.open("POST", "submitform.php");
formData.append("serialnumber", serialNumber++);
request.send(formData);
```

这样一来我们就能在发送表单数据前进行配置，以添加一些不需用户编辑的信息。

## 用FormData对象发送文件
你也可以用FormData对象发送文件，只需在你的`<form>`标签间包含一个`file`类型的`<input>`元素：

```
<form enctype="multipart/form-data" method="post" name="fileinfo">
  <label>Your email address:</label>
  <input type="email" autocomplete="on" autofocus name="userid" placeholder="email" required size="32" maxlength="64" /><br />
  <label>Custom file label:</label>
  <input type="text" name="filelabel" size="12" maxlength="32" /><br />
  <label>File to stash:</label>
  <input type="file" name="file" required />
  <input type="submit" value="Stash the file!" />
</form>
<div></div>
```

然后你就能用如下代码发送文件了：

```
var form = document.forms.namedItem("fileinfo");
form.addEventListener('submit', function(ev) {

  var oOutput = document.querySelector("div"),
      oData = new FormData(form);

  oData.append("CustomField", "This is some extra data");

  var oReq = new XMLHttpRequest();
  oReq.open("POST", "stash.php", true);
  oReq.onload = function(oEvent) {
    if (oReq.status == 200) {
      oOutput.innerHTML = "Uploaded!";
    } else {
      oOutput.innerHTML = "Error " + oReq.status + " occurred when trying to upload your file.<br \/>";
    }
  };

  oReq.send(oData);
  ev.preventDefault();
}, false);
```

>**注意：**若你传入了一个表单的引用，那么表单指定的方法（译注：应指Post、Get）会覆盖open()方法调用时指定的。

你也可以直接添加File或Blob到FormData对象，如下：

```
data.append("myfile", myBlob, "filename.txt");
```

使用append()方法时，使用第三个可选的参数会指定一个文件名，用于请求头的`Content-Disposition`字段。若没有指定文件名（或者该参数不被支持），那就默认使用“blob”这个名字。

## 通过AJAX提交表单和发送文件而不使用FormData对象
若你想了解如何基于AJAX进行序列化和发送表单操作，而不使用FormData对象，可阅读[此文](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest/Using_XMLHttpRequest#Submitting_forms_and_uploading_files)。

## 参见
[Using XMLHttpRequest](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest/Using_XMLHttpRequest)
[HTMLFormElement](https://developer.mozilla.org/en-US/docs/Web/API/HTMLFormElement)
[Blob](https://developer.mozilla.org/en-US/docs/Web/API/Blob)
[Typed Arrays](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Typed_arrays)
