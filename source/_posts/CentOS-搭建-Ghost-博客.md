title: CentOS 搭建 Ghost 博客
permalink: install-ghost-on-centos
id: 22
updated: '2014-12-10 03:54:23'
date: 2014-12-10 03:52:22
tags:
---


至于为什么要写博客网上有很多大牛都写了自己的观点，我就不赘述了，既然你找到了这里，那就意味着你对博客感兴趣；至于为什么选择Ghost，网上也有一大堆的夸赞，但我只想说，它轻便快捷，最重要的是我能看懂Node.js的代码^_^；

下面就跟着我一块来弄个个人博客吧。


##基本的信息
* digitalocean旧金山5美元VPS
* CentOS 6.5 x32
* Node.js 0.10.33
* MySQL 5.1 //mysql> select version();查看
* Nginx 1.7.8
* ghost 0.5.6



##初始化VPS

1. `yum update` 更新yum源
2. `yum groupinstall "Development Tools"` 安装开发工具组包 
3. `yum install wget`

##yum install MySQL

1. `yum install mysql mysql-server ` //通过yum命令安装
2. `service mysqld start`	//启动MySQL
3. `chkconfig mysqld on`	//配置开机自启动
4. `mysql_secure_installation` //配置MySQL，以下是详细内容

```

//输入安装MySQL时的密码，此时为空，直接回车
Enter current password for root (enter for none):
OK, successfully used password, moving on...


Set root password? [Y/n]		//设置root密码
New password:
Re-enter new password:
Password updated successfully!
Reloading privilege tables..
 ... Success!

Remove anonymous users? [Y/n]	//删除匿名用户
 ... Success!
 
Disallow root login remotely? [Y/n]	//禁止root用户远程登录
 ... Success!

Remove test database and access to it? [Y/n] //删除默认的 test 数据库， 
 - Dropping test database...
 ... Success!
 - Removing privileges on test database...
 ... Success!
 
 
Reload privilege tables now? [Y/n]	//是否马上应用最新的设置
 ... Success!

Cleaning up...

All done!  If you've completed all of the above steps, your MySQL
installation should now be secure.

Thanks for using MySQL!
```

至此，MySQL安装成功。

为了正常使用MySQL，还得稍作配置才行。
####设置字符编码为UTF-8
编辑MySQL的配置文件

`vi /etc/my.cnf`

将下面内容填入响应位置即可支持中文，[详细参考](http://www.geeekr.com/change-mysql-character/)

```
[client]
default-character-set=utf8

[mysql]
default-character-set=utf8

[mysqld]
character-set-server=utf8 
```

####为Ghost程序设置专用用户

```
//登录MySQL
mysql -u root -p
//添加名为ghost的用户，并给ghost用户授予ghost数据库的所有权限，并且设置ghost用户密码
GRANT ALL PRIVILEGES ON ghost.* To 'ghost'@'127.0.0.1' IDENTIFIED BY '密码';
```



##编译安装Node.js

1. `wget http://nodejs.org/dist/v0.10.33/node-v0.10.33.tar.gz`
2. `tar zxvf node-v0.10.33.tar.gz`
3. `cd node-v0.10.33`
4. `./configure`
5. `make && make install`

##编译安装Nginx

Nginx需要依赖于`pcre-devel zlib zlib-devel`。
`yum install pcre-devel zlib zlib-devel`

1. `wget http://nginx.org/download/nginx-1.7.8.tar.gz`
2. `tar zxvf nginx-1.7.8.tar.gz`
3. `cd nginx-1.7.8`
4. `./configure`
5. `make && make install`


##安装Ghost


1. `mkdir /var/www`	//这里将`/var/www`目录作为博客根目录
2. `cd /var/www`
3. `wget https://ghost.org/zip/ghost-0.5.6.zip`
4. `unzip ghost-0.5.6.zip -d ghost`	//解压到ghost目录
5. `cd ghost`
6. `tree -L 1`	//使用`tree`命令查看目录数 [参考](http://www.geeekr.com/tree-command-on-mac/)

```
.
├── bower.json
├── config.example.js
├── content
├── core
├── Gruntfile.js
├── index.js
├── LICENSE
├── package.json
├── PRIVACY.md
├── README.md
└── testem.json
```

这里需要将`config.example.js`重命名为`config.js`，然后并对其配置。

```
mv config.example.js config.js
vi config.js
```

```
production: {
	url: 'http:geeekr.com',	//此处应该改成你自己的域名，如果暂时没有域名，那就写成IP地址。
    mail: {},
    database: {
        client: 'mysql',
        connection: {
            host     : '127.0.0.1',
            user     : 'ghost', //上面配置过
            password : 'ghost对应的密码', //上面配置过
            database : 'ghost', //我们前面为 Ghost 创建的数据库名称
            charset  : 'utf8'
        },

    server: {
            host: '127.0.0.1',
        	port: '2368'//若修改该端口记得在nginx中做相应改变
        }
    },
```

配置完成后使用`npm`命令安装依赖模块

`npm install --production`

`--production`的意思就是以production的配置信息来加载依赖模块。

为了让Ghost程序能够后台运行，我们还得再安装个工具，就是`forever`，使用`-g`参数就是在全局模式中安装，这样我们就可以再任何地方都能使用`forever`命令了。

`npm install forever -g`

启动命令是：

`NODE_ENV=production forever start index.js`

>注意：要在ghost目录下使用哦，如果要在其他目录下使用那就得给index.js文件加上完整路径了。


此时还没完，还要配置Nginx信息。
##配置Nginx
编辑Nginx配置文件

`vi /usr/local/nginx/conf/nginx.conf`

修改下面内容
```
server {
        listen      80;
        server_name  localhost;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location / {
            proxy_pass              http://localhost:2368;
            proxy_set_header        Host      $http_host;
            proxy_set_header        X-Real-IP $remote_addr;
            proxy_set_header        X-Forwarded-For $proxy_add_x_forwarded_for;
        }
}
```

这里只配置主要部分，具体详细的优化配置需要自己去学习。

##完结
如果不出意外，输入你的域名或IP地址就能看到 Ghost 的界面了。当然，一般都是会出现意外的，这个时候建议你查看如下内容：

nginx 错误日志，/var/log/nginx/error.log
Ghost 代码所在目录的权限
Ghost 的配置是否正确
数据库能否正常连接

