title: Mac如何更改maven的java版本
permalink: fix-maven-java-version-mac-osx
id: 21
updated: '2014-11-26 07:38:00'
date: 2014-11-21 10:06:24
tags:
---

```shell
[ERROR] Failed to execute goal org.apache.maven.plugins:maven-compiler-plugin:3.2:compile (default-compile) on project api: Fatal error compiling: invalid target release: 1.7 -> [Help 1]
```

根据错误提示`invalid target release: 1.7`，无效的目标版本。

###查看pom.xml文件
```xml
<plugin>
	<artifactId>maven-compiler-plugin</artifactId>
          <version>3.2</version>
          <configuration>
            <source>1.7</source>
            <target>1.7</target>
          </configuration>
	</plugin>
```

根据配置可以看出`maven-compiler-plugin`的目标版本是JDK 1.7；

###查看JDK版本
```shell
➜  ~  java -version
java version "1.7.0_71"
Java(TM) SE Runtime Environment (build 1.7.0_71-b14)
Java HotSpot(TM) 64-Bit Server VM (build 24.71-b01, mixed mode)
```
根据上面信息可以看出我的jdk版本也是1.7；

那么问题出在哪里了呢？

###查看Maven信息

```shell
➜  ~  mvn -v
Apache Maven 3.2.1 (ea8b2b07643dbb1b84b6d16e1f08391b666bc1e9; 2014-02-15T01:37:52+08:00)
Maven home: /usr/local/Cellar/maven/3.2.1/libexec
Java version: 1.7.0_71, vendor: Oracle Corporation
Java home: /System/Library/Java/JavaVirtualMachines/1.6.0.jdk/Contents/Home
Default locale: zh_CN, platform encoding: UTF-8
OS name: "mac os x", version: "10.10", arch: "x86_64", family: "mac"
```

终于真想大白了，原来maven引用的是1.6的版本，那么如何修改配置为1.7版本呢？

原来，maven读两个配置文件，`/etc/mavenrc` 和 `~/.mavenrc`。两个文件默认是没有的，可以任意选择一个做修改，我选择我用户目录下的`~/.mavenrc`，将下面代码写入。

```xml
JAVA_HOME=`/usr/libexec/java_home`
```
> 
如果你在Mac上安装了多个版本的JDK，而又不想改变默认的JDK版本，那么你只需要在配置后面加上版本号即可，比如:
```xml
JAVA_HOME=`/usr/libexec/java_home -v 1.7`
```

在Mac中`/usr/libexec/java_home`表示`java_home`的一个连接文件。

```shell
➜  ~  ll /usr/libexec/java_home
lrwxr-xr-x  1 root  wheel    79B 11 14 16:39 /usr/libexec/java_home -> /System/Library/Frameworks/JavaVM.framework/Versions/Current/Commands/java_home
```

然后在执行

```shell
➜  ~  mvn -v
Apache Maven 3.2.1 (ea8b2b07643dbb1b84b6d16e1f08391b666bc1e9; 2014-02-15T01:37:52+08:00)
Maven home: /usr/local/Cellar/maven/3.2.1/libexec
Java version: 1.7.0_71, vendor: Oracle Corporation
Java home: /Library/Java/JavaVirtualMachines/jdk1.7.0_71.jdk/Contents/Home/jre
Default locale: zh_CN, platform encoding: UTF-8
OS name: "mac os x", version: "10.10", arch: "x86_64", family: "mac"
```

然后在install项目就没有这个错误了。

