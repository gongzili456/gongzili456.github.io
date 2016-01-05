title: Install Redis on Debian
permalink: install-redis-on-debian
id: 23
updated: '2015-06-19 17:00:18'
date: 2015-06-19 16:43:29
tags:
---

###准备环境：
我的本机环境
```
#lsb_release -a

No LSB modules are available.
Distributor ID:	Debian
Description:	Debian GNU/Linux 7.8 (wheezy)
Release:	7.8
Codename:	wheezy
```

1. 更新包
`sudo apt-get update`
2. 安装必要依赖
`sudo apt-get install build-essential tcl8.5`

###安装Redis

1. 下载redis安装包，版本自行选择。
`wget http://download.redis.io/releases/redis-3.0.2.tar.gz`

2. 解压安装包。
`tar -zxvf redis-3.0.2.tar.gz && cd redis-3.0.2`

3. 编译安装
`make && make install`

###配置Redis

1. `cd utils`
2. 执行配置脚本
`sudo ./install_server.sh`

这里会配置一些参数，都有默认值，可以一路回车到底。

3. 启动和关闭命令
```
sudo service redis_6379 start
sudo service redis_6379 stop
```

###客户端访问

1. `redis-cli -p 6379`
2. 你将会看到
`redis 127.0.0.1:6379>`

###最后

设置redis开机启动
`sudo update-rc.d redis_6379 defaults`

