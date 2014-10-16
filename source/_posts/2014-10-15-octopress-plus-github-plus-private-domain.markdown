---
layout: post
title: "使用Octopress搭建博客的那些坑"
date: 2014-10-15 17:55:54 +0800
comments: true
categories: 
---

最近用octopress和github page搭建了一个简单的个人博客，跟自己的域名绑定起来。
搭建方法google一抓一大把，这里就简要赘述一下这个过程中遇到的几个坑吧。

---
<br>

###一号坑: 企图手动去改octopress的模板发现改错了

[octopress](http://octopress.org/)是用rails写的一款快速搭建博客的工具.
源码使用markdown和scss编写，在部署前用以下命令自动生成html，css等静态网页：

	rake generate

这里就有两个概念了：**源代码** & **部署代码**。
clone过[octopress的代码](https://github.com/imathis/octopress)到本地后，cd到该目录下

{% img /images/octopressDir.png %}

会发现有一个source和_deploy的目录，这两个目录下分别存放着上面提到的两个概念中的代码。

上面提到了，octopress中需要手动编写的代码是源码，所以需要改动代码的时候，或者发表博文，所有的修改都应该在这个目录下进行。
而非修改_deploy目录下的html，css等.

修改结束用，在本地使用

	rake generate

生成部署所需的静态网页代码后，

	rake preview

运行以上命令，就可以在本地localhost：4000 查看修改结果了。


###二号坑： 绑定域名的时候CNAME无法提交到github

我们都知道，使用github page来搭建博客，当需要绑定独立域名时，<br>
只要在该repo下添加CNAME文件，并将独立域名写进去再提交，就绑定成功了。<br>

笔者按照常用方法添加CNAME后，却发现无法提交到github上。<br>
那么问题来了，如果说部署所需的所有代码都是先放到source目录下，再由rake generate生成部署代码，那这个CNAME文件怎么办？<br>

既然所有部署代码都来源于source目录，那当然是把CNAME文件也放在source目录下了！<br>
此时rake generate后，会发现CNAME文件已经乖乖到了_deploy目录下。<br>
rake deploy 部署后就可以在自己的独立域名下看到github上的博客啦

最后，不要忘记提交代码到github的repo上，记住是提交到source这个branch的。
因为默认master是存放部署代码的，那么source就用来存放我们手写的源码。

---

<br>
笔者接触octopress时日不多，隐约感觉rakefile是一个很重要的东西，但由于时间关系还未完全吃透其中的命令。
理解上存在偏差的地方，欢迎指正。







