---
title: 'tomcat on mat '
date: 2017-06-26 20:34:27
tags: tomcat
category: tomcat
---

#Mac 下安装配置Tomcat

## 安装

去[Apache Tomcat](http://tomcat.apache.org/download-70.cgi)上下载一个Tomcat。
我下载的是7.0.78版本

{% asset_img tomcat20170622.png This is an image %}


下载完，放到根目录下；/Library/Tomcat

设置运行脚本权限

```
sudo chmod 755  /Library/Tomcat/bin/*.sh
```

## 启动

```
sudo sh /Library/Tomcat/bin/startup.sh
```

成功的话会出现：

```
➜  ~ tomcat start
Using CATALINA_BASE:   /Library/Tomcat
Using CATALINA_HOME:   /Library/Tomcat
Using CATALINA_TMPDIR: /Library/Tomcat/temp
Using JRE_HOME:        /Library/Java/JavaVirtualMachines/jdk1.8.0_91.jdk/Contents/Home
Using CLASSPATH:       /Library/Tomcat/bin/bootstrap.jar:/Library/Tomcat7078/bin/tomcat-juli.jar
Tomcat started.
```

<!-- more -->

打开浏览器输入：localhost8080 ,就可以看见tom猫这个页面
![tomcat home page](/tomcat201706221.png)

## 配置快捷命令

这样每次启动太麻烦了，没次都要跑到library去启动tomcat的shel脚本

在终端的包含路径下（如/usr/local/bin），下新建一个tomcat的文件：

```
➜ /usrlocal/bin >sudo touch tomcat
```

打开这个文件，配置shell脚本,然后保存

```
#!/bin/bash
case $1 in
start)
sh /Library/Tomcat/bin/startup.sh
;;
stop)
sh /Library/Tomcat/bin/shutdown.sh
;;/Users/huangzhixiong/Desktop/tomcat-note/tomcat-on-mac.md
restart)
sh /Library/Tomcat/bin/shutdown.sh
sh /Library/Tomcat/bin/startup.sh
;;
*)
echo “Usage: start|stop|restart”
;;
esac
exit 0
```


快捷命令如下：

1. tomcat start
2. tomcat stop
- tomcat restart
