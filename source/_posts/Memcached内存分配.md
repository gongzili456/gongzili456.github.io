title: Memcached内存分配
tags: |-

  - 内存
  - Memcached
  - 缓存
permalink: memcachednei-cun-fen-pei
id: 15
updated: '2014-09-23 14:55:03'
date: 2014-09-04 10:33:15
---

Memcached是在项目中常使用的分布式缓存服务。很好的解决了MySQL数据库的访问压力。所以我们要懂它，用好它。

Memcached有三个概念：page，slabs，chunk，要理解Memcached是如何来存储数据的，那就要理解这三个概念是怎么一回事。

###Page
Memcached的内存分配是以page为单位的，默认情况下一个page是1M大小。当需要申请内存时，memcached会划分一个page给需要的slabs区域。

###slabs
Memcached不是将所有大小的数据都存在一块的，而是预先划分出不同的区域将不同大小的数据分别存放，这就是slabs。每个slabs只负责存储一定范围大小的数据(由chunk决定)。
![](http://blog-geeekr-com.qiniudn.com/images%2F1%2Fdd%2F7eb83f3bdbc5cd9a51ce1976bd22e.png)

###chunk
chunk是memcached实际存放数据的地方，chunk的大小就是管理它的slabs的最大值，所以分配给当前chunk的数据都能被存下，如果数据小于当前chunk的大小，那么剩余的空间将被闲置，这是防止内存碎片划而设计的。
![](http://geeekr.qiniudn.com/images/e/b8/bbf982ff8eb8979a43ded88d8bbfc.png)

###内存分配
Memcached在启动的时候会开辟一块内存(可以通过-m参数修改)，这些内存是按需分配给slabs的。当一个缓存数据需要被存放时，memcached首选要确定对应的slabs，如果此slabs没有足够空间，那么就要申请空间，申请一个page大小的空间，然后按照当前slabs的size(也就是chunk的大小)切分成若干个chunk，然后再将数据存入某一个chunk中。
![](http://geeekr.qiniudn.com/images/f/3b/4b8e69e628b00824a5b1b269bab7f.png)

####slbas内存分配实例
![](http://geeekr.qiniudn.com/images/1/29/8553a97db78c1805a86e08e40aeca.jpg)