title: 多说评论框添加UA及其优化
date: 2015-05-31 23:08:47
tags: [多说,Tinnypp]
categories: 博客之路
---
前几天在[课件的博客](http://ppting.me)看到多说评论框上有个类似用户头衔、很酷炫的[User Agent](http://baike.baidu.com/link?url=QnADnFVYJdCfJu-QgXOsqqYXM3P-9rvk2ZqguYWRlwg43mfG7_m3iaVEeKQcOkphMKeJh8kpGaU0kteNtU4U-vQ2btSz4Lde4x2aloqfo7W1ouC-g64acbQXDvDyubyA_sWI526g-XUaQVvI19yE-K)，于是就动手把自己的博客也装了一下，效果各位可以在下面的评论框看到。实现方法可见[这篇文章](http://wsgzao.github.io/post/duoshuo/)，接下来我就以我的主题为例做一些补充说明：

在以前的博文里，我提到过pacman系主题的原装多说代码，其实那段在`layout\_partial\post\comment.ejs`里的代码只是一部分，还有一段js在`layout\_partial\after_footer.ejs`里。这次我们要修改的就是这段js，找到`ds.src`后改为
`ds.src = '//你的embed.js文件地址'`。如果想偷懒，可以更新一下Tinnypp，我把embed.js放到source/js里了，直接改为`ds.src = '<%- config.root %>js/embed.js'`，再在你的多说里添加那段自定义css就可以看到效果啦。但是千万记得把embed.js里的`e.user_id`改为你的多说ID,要不然显示不了你的‘博主’头衔:)

完成了这些，我发现原文里作者下载的font-awesome字体图标没有派上用场，查找总结出它的用法，就是添加往主题里添加css和字体文件。font-awesome字体文件Tinnypp在`source/font`就自带了，为了省事，我把`font-awesome.min.css`也放到`source/css`里然后把里面调用的fonts文件目录全改为'font'。最后再在`layout\_partial\head.ejs`里添加如下css调用，图标就加到评论UA上了，是不是很有逼格？以上这段的内容我都在新版主题里做好了，已经加了UA的和想加UA的亲们可以更新体验一下哦。

```
  <% if (theme.duoshuo.enable){ %>
    <link href="<%- config.root %>css/font-awesome.min.css" rel="stylesheet">
    <% } %>
```