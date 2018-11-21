---
title: 'tomcat on mac '
date: 2017-06-26 20:34:27
tags: tomcat
category: tomcat
---

# Mac 下安装配置Tomcat

## 安装

去[Apache Tomcat](http://tomcat.apache.org/download-70.cgi)上下载一个Tomcat。
我下载的是7.0.78版本

![tom](/tomcat20170622.png)

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
;;
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
3. tomcat restart

## 管理Mac OS自带的Apache

###Mac OS X 内置了Apache 和 PHP

> 管理方法一：

打开“系统设置偏好（System Preferences）” -> “共享（Sharing）” -> “Web共享（Web Sharing）”

> 管理方法二：

1. 启动Apache：运行“sudo apachectl start”，再输入root帐号密码
2. 停止Apache：运行“sudo apachectl stop”，
3. 查看Apache：版本：运行“sudo apachectl －v”，
4. 重启Apache：运行“sudo apachectl restart”

### Mac OS中Apache文件默认存放位置

Mac OS 的Apache2的配置文件（httpd.config）保存在/etc/apache2

Mac OS 的Apache2的程序文件（httpd, ab） 保存在/usr/sbin/

Mac OS 的Apache2的默认根目录：/Library/WebServer/Documents

修改Apache2的配置文件，在终端运行“sudo vi /etc/apache2/httpd.conf”，打开Apche的配置文件进行修改。


