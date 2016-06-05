title: 使用github的pages服务建立项目主页的注意事项
date: 2015-06-03 00:08:52
tags: [github]
categories: 小探索
---
之前我对github的pages服务的使用一直只停留在建立博客而已，最近整理了上面的几个repo才使用了其建立项目主页功能。总体来讲还是很方便的，各位看看[官方文档](https://help.github.com/categories/github-pages-basics/)即可大致了解。而接下来我再说说期间遇到的一些问题和解决：

## 确保你的username.github.io不会跳到别的站点
首先github的pages服务给每个用户只分配了一个域名`username.github.io`；你也可以添加CNAME记录使其指向你自己其他域名，pages服务会为你自动跳转。而每个项目的`gh-pages`分支下其项目主页默认却只是`<username>.github.io/<projectname>`或`CNAME记录指向的其他域名/<projectname>`，就不能再自己设置域名了。所以这里千万要注意用户域名`username.github.io`的CNAME记录别跳到别的站上去，否则不能访问到各个项目的`gh-pages`分支。

由于我博客使用双repo同时在github和gitcafe上部署，为保证国内访问，`levonlin.github.io`的CNAME中，域名levonlin.info的CNAME记录值被我设为gitcafe.io，结果就导致建立的项目主页一直404。如果你和我一样的情况，那要么删除`username.github.io`的CNAME，要么保证CNAME指向的域名不会跳到别的站上，这样才能愉快地使用pages服务。

## 解决gh-pages分支clone到本地后出现push冲突
这就得理解git的工作原理了，local repo和remote repo上两个分支得现在有或者merge以前有相同的"base"才能用push实现local repo上branch的上传，如下图：

![push冲突](http://ww4.sinaimg.cn/large/85ad0d9cgw1esqtsf76xoj20ko0camyb.jpg)

### clone下来后保持同步
在github创建gh-pages后，由于clone到local repo上的只有master分支，而master分支与gh-pages分支的初始状态并不同步。可以先fetch一次gh-pages再checkout到它上面commit，就能实现正常push：

![fetch gh-pages](http://ww3.sinaimg.cn/large/85ad0d9cgw1esqtth7lfzj20ia08kwfa.jpg)
![commit并push](http://ww4.sinaimg.cn/large/85ad0d9cgw1esqtw0e5y9j20k4084gmf.jpg)
![push成功](http://ww1.sinaimg.cn/large/85ad0d9cgw1esqtwe1345j20kk09rgmg.jpg)

### 自建的分支保持同步
此外如果你在本地自建了gh-pages分支，则要再checkout到它上面pull后commit，便可以push啦：

![pull gh-pages](http://ww2.sinaimg.cn/large/85ad0d9cgw1esqtwv14ikj20hz09awfc.jpg)
![commit并push](http://ww4.sinaimg.cn/large/85ad0d9cgw1esqtx54i5tj20kz0ahq3y.jpg)
![push成功](http://ww3.sinaimg.cn/large/85ad0d9cgw1esqtxdpn1rj20le0cf3zn.jpg)
