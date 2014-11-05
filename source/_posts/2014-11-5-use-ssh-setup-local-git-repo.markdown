---
layout: post
title: "使用ssh搭建本地git仓库"
comments: true
categories: 
---

最近由于工作需要，笔者准备在本地局域网中搭建git库，经过授权的本地用户就可以在该库上进行提交，展开工作。

搭建git库很简单，选一台机器作为本地git服务器，建好文件夹，运行

	git init

现在问题来了，如何进行授权。

我们都知道文件传输需要文件传输协议，git pull，git push本质上也都是在进行文件操作，这也是一种文件传输。

常用的传输协议有 http, ssh 等，笔者本次使用了ssh作为传输协议。

在本地ssh目录下有一些成对的key，选择其中一个公钥（以.pub结尾的文件），将文件中的内容append到服务器的ssh目录下，一个叫authticate的文件中。

顾名思义，这个文件是用来授权用户的，加进公钥后表明服务器跟clint机器建通过ssh协议建立了连接。但是这还不够。ssh协议的使用是通过一对秘钥进行工作的，公钥是锁，私钥则相当于钥匙。上一步的操作是把锁给了服务器，那么下一步就要指定开这把锁的钥匙。

client机器上，同样是.ssh目录下，在config文件（如果没有就touch一个）里配置

	Hostname 10.29.*.**
	IdentityFile ~/.ssh/local_git

Hostname是服务器的IP，IdentityFile是每次跟这台服务器通信时需要的私钥。

至此，就可以在client机器上git clone 服务器上的代码了。







