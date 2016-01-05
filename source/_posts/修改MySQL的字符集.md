title: 修改MySQL的字符集
tags: |-

  - MySQL
  - 字符
permalink: change-mysql-character
id: 8
updated: '2014-12-10 03:23:00'
date: 2014-06-16 14:19:50
---

编辑MySQL的配置文件

`vi /etc/my.cnf`

MySQL的配置文件分几个部分，修改字符集需要将一下三个部分修改（即中括号里的内容），其中一些部分可能默认没有，需要自己写入，这样client，mysql，mysqld三个部分的字符集都为utf8了。就不会出现中文乱码的问题了。

```mysql
[client]
default-character-set=utf8

[mysql]
default-character-set=utf8

[mysqld]
character-set-server=utf8
```