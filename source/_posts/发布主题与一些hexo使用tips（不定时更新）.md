title: 发布主题与一些hexo使用tips（不定时更新）
date: 2015-02-15 14:54:10
tags: Tinnypp
categories: 博客之路
---
已经半个月没写博客了，上次折腾得差不多之后，又添加了百度统计、rss、sitemap等等。然后参照[这个教程](http://blog.yuanbin.me/posts/2014/05/multi-deployment-with-hexo.html)，搞了Hexo多Repo部署，现在国内外有github和gitcafe双保险，至少不用担心博客因托管处挂掉而挂啦。

本来我只想把Tinny这个主题fork下来自己整些私人定制的修改，没想到缝缝补补地填了不少坑。现在有童鞋建议我把修改好的主题发布一下，考虑到原作者说Tinny is not pretty，已经在整新主题，那估计Tinny不会再更新了。所以我就厚着脸皮把我折腾出来的主题放github上了，c之后有cpp，Tinny之后有[Tinnypp](https://github.com/levonlin/Tinnypp)，各位亲们如果有什么问题欢迎给我发[issue](https://github.com/levonlin/Tinnypp/issues)哈。

最后，自己折腾博客以来，经历了不少教训也攒了些tips。因为来源复杂，除了直接百度或者以前文章提到的教程里有，还些需要花不少功夫才发现。为了方便自己，也方便各位hexoer，现在把它们贴出来share一下，需要者自取咯~

* 修改样式文件时，要注意保留某些空格与原来相同，要不然会报错。

* HexoError解决方法：将主题language下的有空格的项都加上双引号。

* 删除标签：先在文章里删除标签，然后执行：hexo clean和hexo g重新生成就好了。

* 主页文章显示摘要:编辑md文件的时候，在要作为摘要的文字后面添加<!--more-->即可。

* 插入图片的格式为`![图片名称](链接地址)`，有两种方式：
    - 使用本地路径：在hexo/source目录下新建一个img文件夹，将图片放入该文件夹下，插入图片时链接即为/img/图片名称。  
    - 使用[微博图床](http://weibotuchuang.sinaapp.com/)：将图片拖入区域中，会生成图片的URL，这就是链接地址。不过所需火狐插件貌似不能用，而chrome插件又非官方，会被chrome强制停用，要用需要[修改组策略模板](http://tieba.baidu.com/p/3091171066)或者使用其他云存储平台的图床。

强烈建议使用图床服务，本地路径加载不是一般地慢，而且每次要push这么多图也要push很久。最关键地，pages服务好像有空间限制的，要是不停地在git文件里传高清图，时间久了你懂的。

* 想在首页文章预览添加图片可以在文章开头添加photo参数，如下:
`photos:http://xxx.com/photo.jpg`
然而因为pacman主题的文章预览有字数限制，所以基于pacman的一系列主题一般是用不了这个功能滴。

* 从github切换到gitcafe的时候最好先把.deploy文件夹删除后重新hexo d一下，不然可能上传不到gitcafe。

* gitcafe-pages设置域名时大小写需与dns里的一致。

* 因为markdown中的html会被直接解析，所以用于展示的html标签要么用\`括起来；要么把`<`和`>`用`&lt;`和`&gt;`替代。

* 由于hexo升级等原因，导致hexo-renderer-marked和hexo-renderer-stylus与hexo2.x不兼容。现在装hexo2.x时`npm install`后还需执行`npm install hexo-renderer-marked@0.1`和`npm install hexo-renderer-stylus@0.1`才能正常使用。

* 目前使用的hexo版本中，使用markdown表格时必须在表格和上文间空有一行。此外如果表头、控制行（就是`---`+`|`）、表体缺了一个或者三者的`|`数目和位置不能对应上都会导致无法正常解析。在markdown语法中，控制行用来控制表头和标题内容的对齐方式，`---`和`:---`表示左对齐，`:---:`表示居中，`---:`表示右对齐。

* 使用默认代码高亮时，代码中不要有空白行，否则这些空白行会给挤到高亮块的开头和结尾。


