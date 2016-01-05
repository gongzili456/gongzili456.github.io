title: 替换Google字体服务，加速ghost
tags: |-

  - google
  - ghost
permalink: overwrite-google-api
id: 7
updated: '2014-09-23 14:50:58'
date: 2014-06-15 14:43:45
---

用上了强大的ghost以后，又装修了一下门面。
安装了[Ghostium](https://github.com/oswaldoacauan/ghostium)主题。
![](http://geeekr.qiniudn.com/images/1/30/e8cd2a93ec3e76c399321dbbedfe6.png)

可是问题来了，Ghostium主题使用Google的字体服务，导致访问博客的时候打开非常慢，这对于网站来说是不能忍受的。
```css
<link href="//fonts.googleapis.com/" rel="dns-prefetch">
<link href="//fonts.googleapis.com/css?family=Droid+Serif:400,700,400italic|Open+Sans:700,400&subset=latin,latin-ext" rel="stylesheet">
```
经过一番搜索，终于找到替代Google fonts API的替代方法了。

> 把fonts.googleapis.com替换为fonts.useso.com就OK了。

据说这是360的服务，修改之后速度确实提升了。


还有一种方式比较粗暴，就是直接把使用googleapis的服务暴力的注释掉。
```css
<!-- <link href="//fonts.googleapis.com/" rel="dns-prefetch"> -->
```
但是这种方式有个问题，就是如果没有备用的字体设置，会被浏览器默认的字体替换，可能会影响网站的显示效果。