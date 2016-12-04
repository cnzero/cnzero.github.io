---
title: "多个SSH Key的config文件管理"
layout: post
date: 2016-12-02 22:48
image: /assets/images/markdown.jpg
headerImage: false
tag:
- markdown
- components
- extra
blog: true
author: cnzero
description: Summary of SSH keys management.
---



[TOC]
最近由于出掉了手里的MacBook Pro，并台式机重装系统到了x64 Windows10，所以重新对个人Github与课题组的gitolite进行管理。由于最初对多个SSH Key管理得相当混乱，此时就顿时陷入了泥潭。
花费了大半天时间，对原来的SSH Key组织推到重来。在这个过程中对config文件等有了更深入地理解，所以在此恢复一篇个人博客。


### 资源
1. 目前个人有一个Github账号，也就是这个Github Pages所相关的账号；
2. 在课题组，由师兄搭建了一个内网里的gitolite在维护与管理；
3. 手里有一个台式机电脑，目前新装Windows10 操作系统；
4. 偶尔会用下手里的一台ThinkPad 笔记本，装有双个系统： Windows 10 与 Ubuntu系统。


### 生成SSH Key
基本代码 ` ssh-keygen -t rsa -C "your_mail_address" ` ， 然后可以一路默认，直接回车就行。最终能够在（Windows 系统）` /c/Users/username/.ssh/ ` 目录中看到产生的两个文件 ` id_rsa ` 和 `id_rsa.pub ` 公钥私钥对。


为了能够尽量平滑地在各个电脑及系统上进行使用的切换，所写的代码尽量都需要在Github与gitolite上进行备份保存，并且简化代码复制传输的过程。
其中一种比较偷懒的方式去维护 `公钥-私钥`， 就是，在每个系统上（注意不是电脑）上近产生一个默认的 `id_rsa `的SSH Key，并同一个Key应用于一个系统上的所有服务——同一个系统涉及的Github管理，gitolite管理，以及其他潜在的SSH Key相关的服务。
![](file:///tmp/WizNote/ecf28fb4-c791-4a11-b961-da4a79354b5a/index_files/126a2a66-72c4-4199-baa3-bcdf22233c2d.png)

如图所示，每个默认的 ` id_rsa ` 管理所有的服务。因此，这就存在一个安全隐患，如果一台电脑上的 SSH Key配破解，也就是所有的服务都被“偷窥”。或者此处打一个比喻，每个电脑上仅仅只有一个“篮子”，这个篮子里盛放了所有的东西，并仅有一个锁-钥匙。俗话说，所有的鸡蛋不能都放到一个篮子里去。那就多造几个篮子吧。



修改上图，考虑到更深的安全性，应达到如下图所示的效果。
![](file:///tmp/WizNote/ecf28fb4-c791-4a11-b961-da4a79354b5a/index_files/66762425-4844-4418-8f8f-f9566bf2011a.png)

如图，虽然看起来更复杂些，但是相对而言就安全了许多——当然这种简单的添加修改方式相对于SSH加密根本不是一个量级上的保密措施。但是能想到了，就实现吧。
所以，问题就变成了，在一台电脑上如何指定针对不同的服务去查询不同的SSH Key公钥以进行匹配。

### 创建及编辑 ` config ` 文件
首先，这是一个plain text，并没有格式后缀，这应该在Linux系统上很常见了。直接用git bash里面的vim进行编辑，或者在Ubuntu的终端中完成。
之前之所以混乱，就是对这个config文件理解混乱，理解不深。现在终于“头撞南墙”，撞多了，就理解深刻了一些。
然后，现在直接放出config文件的代码，然后稍作其中的内容进行解释说明。
```
# -----------------two styles for github.com-----------
# ***normal style***
# --- connection testing with debug information ---
#     ssh -vT git@github.com
# --- git clone command ---
#     git clone git@github.com:cnzero/repo.git
Host github.com
    HostName github.com
    User git
    IdentityFile ~/.ssh/zeroWin10PC4cnzeroGithub
 
# ***abbreviation style***
# --connection testing with debug information--
#   ssh -vT github
# -- git clone command --
#   git clone github:cnzero/repo.git
Host github
    HostName github.com
    User git
    IdentityFile ~/.ssh/zeroWin10PC4cnzeroGithub
 
# --------------two styles for 210.72.140.166-----------
# ***normal style***
# -- connection testing with debug information--
#    ssh -vT git@210.72.140.166
# -- git clone command --
#    git clone git@210.72.140.166:repo.git
Host 210.72.140.166
    HostName 210.72.140.166
    User git
    IdentityFile ~/.ssh/zeroWin10PC4Server409Superuser
 
# ***abbreviation style***
# -- connection testing with debug information--
#    ssh -vT s409
# -- git clone command --
#    git clone s409:repo.git
Host s409
    HostName 210.72.140.166
    User git
    IdentityFile ~/.ssh/zeroWin10PC4Server409Superuser
```
Host 可以理解为一种助记符或简化符号，方便以后进行ssh链接。为了省事儿，才在config文件里面描述了两种方式： ` normal style ` 和 ` abbreviation style ` 。并且在此config文件中做了很好的注释。作为探索的结果，不再赘述。
当然config文件里面，不仅仅有以上的关键词， ` man ssh config ` 能够看到更多关键词及其赋值含义，值得探索。

### SSH 与 HTTPS 两种链接方式
最开始并没有注意两者的不同之处，Github在每个repository下面默认提示使用HTTPS链接，` git clone https://github.com/username/repo.git ` 。但是在Windows的git bash里面再进行 `push` 的时候又一次提示输出用户名与密码。这就疑惑了——如果是没有提前添加SSH Key，这样很正常；可是当添加了SSH Key之后，仍然需要验证，好像就是哪里出现了错误。同时，我进行了 ` ssh -vT git@github.com ` 进行ssh链接测试（添加关键参数 `v' 能够提示debug 信息）。结果提示正常，能够正常通过验证并显示用户名。这一步测试，至少可以用来检验SSH Key公钥私钥匹配无误。
然后，哪里出现了问题呢？
通过Google搜索，最后发现是SSH 与 HTTPS的链接方式不同。然后，再次通过SSH 的方式clone 一些repository（在每个代码库的Github页面，download部分会有HTTPS与SSH的选择），再上传，结果就没有问题了。
师兄说，SSH的通信方式可以研究研究。这个是后话了，留个白。

### 再进一步的问题
既然在config文件中有 __abbreviation style__ ，那就对比下Github与gitolite在这一点上的“简化结果”，如上代码提示：
 ` git clone s409:repo.git `
 ` git clone github:cnzero/repo.git `
我觉得对Github“简化”得还不够彻底，最好的形式应该如第一个gitolite的方式——简洁明了，省键盘。
就这个问题，在完全不理解SSH通信方式的前提下进行了尝试，结果都没有成功。
后来跟师兄讨论这件事情，进一步理解，实际上Github相当于有两层用户，一层通用git，一层个人用户username。而如果实际上使用了gitolite，实际上只有一层username即可。所以针对Github目前掌握的知识与理解的程度，只能简化与此了。
但如上，SSH通信方式，` man ssh config ` 以及此问题，基本上同源，值得去探索。