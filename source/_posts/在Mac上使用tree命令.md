title: 在Mac上使用tree命令
tags: |-

  - Mac
  - shell
permalink: tree-command-on-mac
id: 13
updated: '2014-12-10 03:26:01'
date: 2014-08-29 11:20:47
---

以树的形式来显示目录信息可读性非常高，就像下面这样，在网上也经常看到下面这种格式，其实实现起来非常简单，Unix系统里面都会有一个命令`tree`(需要安装)，功能就是用来以树的形式来遍历目录的。

```shell
ROOT/
└── WEB-INF
    └── web.xml
```

在Mac上默认是没有`tree`命令的，据说在Ubuntu上也没有。

###安装
```shell
brew install tree
```
###使用


```shell
tree dir	//查看某个目录
```

```
tree -L 2	//查看当前目录，但只看2个层级
```