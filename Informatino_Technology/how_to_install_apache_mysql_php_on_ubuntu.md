# 在Ubuntu16.04下安装Apahce、MySQL和PHP环境

## 介绍

“LAMP” 所代表的意思是一组开源软件，在上个世界风靡互联网的web应用的技术栈，其实到现在也很流行，最起码仍然是web相关人员的入门必修，这个术语实际上是一个缩写，它代表了Linux操作系统，Apache Web服务器。站点数据存储在MySQL数据库中，动态内容由PHP处理。

在本指南中，我们将在Ubuntu 16.04 Droplet上安装一个 LAMP 栈。

## 先决条件

在开始本指南之前，请参考另外一篇文章：[如何初始化Ubuntu环境]()

## 步骤一：安装Apahce，且允许防火墙通过

Apahce web服务器几乎这目前、也是历史上最为流行的web服务器，它拥有大量优质的文档，首选Apache搭建站点，益处多多。

在Ubuntu下安装Apache非常的简单，执行下面命令即可：

```
:~$ sudo apt-get install -y apache2
```

中间需要输入用户的密码。

### 设置全局服务名称，省得被警告

其实这个无伤大雅，但是青云QingCloud的用户很多是“强迫症”/技术洁癖患者，一点警告都不允许有的。可以在启动Apache之前，作一个简单的测试，输出如下：

```
:~$ sudo apache2ctl configtest
AH00558: apache2: Could not reliably determine the server's fully qualified domain name, using 127.0.1.1. Set the 'ServerName' directive globally to suppress this message
Syntax OK
```
按照提示修改即可。编辑文件：

```
sudo vim /etc/apache2/apache2.conf
```

在文件的最后，追加```ServerName IP```，一行内容，在其后面添加虚拟机的IP地址，或者是可解析的主机名即可。类似如下：

```
ServerName  10.120.7.40
```

然后再执行配置测试，输出就没有警告了。为了让配置生效，请重新启动Apahce服务。

```
:~$ sudo systemctl restart apache2.service
```
### 调整防火墙以允许web流量访问

我们在上一篇文章中，以工具UFW为例配置了Ubuntu的防火墙，本文依照这个惯例，同样使用UFW来配置，让HTTP和https的流量可以通过，首先要确保UFW拥有Apache的profile：

```
:~$ sudo ufw app list
Available applications:
  Apache
  Apache Full
  Apache Secure
  OpenSSH
```

其中 ```apache full```就是我们需要配置的，它可以同时激活端口80和443：

```
:~$ sudo ufw app info "Apache Full"
Profile: Apache Full
Title: Web Server (HTTP,HTTPS)
Description: Apache v2 is the next generation of the omnipresent Apache web
server.

Ports:
  80,443/tcp
```

将此规则应用到操作系统：

```
:~$ sudo ufw allow in "Apache Full"
Rule added
Rule added (v6)
```
### 在QingCloud平台配置防火墙

要记得，您使用的是QingCloud云平台中的资源，上述创建的虚拟机是无法从互联网上直接访问的，需要访问QingCloud的公有IP，然后做NAT映射方可。请移步QingCloud控制台，设置Ubuntu所在虚拟机的防火墙规则，增加下行规则两条，允许80和443端口访问。

### 测试Apache服务

在客户端浏览器中输入QingCloud提供的IP地址，例如：

```
http://139.198.190.113/
```
看到Ubuntu默认页面：![](http://assets.digitalocean.com/articles/how-to-install-lamp-ubuntu-16/small_apache_default.png)

现在我们已经拥有一个能够正常运行web的服务器了，接下来我们来安装持久存储数据的数据库管理系统——MySQL。

## 安装MySQL

执行如下命令即可：

```
:~$ sudo apt-get install mysql-server
```

中间Ubuntu的安装程序会询问你设置MySQL的超级用户:root的密码，这是MySQL的管理账号，拥有特权。请认真的设置密码，最好使用强密码。

如果有安全意识，笔者强烈建议采用安全安装脚本，将MySQL默认的设置重新配置为强制安全。

```
mysql_secure_installation
```

## 安装PHP

PHP 能够处理代码以让web显示一些动态的内容。它可以执行脚本，连接到后端的MySQL数据库，并对数据库进行读取和写入操作，PHP会并将处理的内容交给我们的Web服务器进行显示。

执行下面的命令，来安装php及其周边，让其可以和Apahce、MySQL协同工作。

```
:~$ sudo apt-get install php libapache2-mod-php php-mcrypt php-mysql
```
安装不出任何意外的话，很快就会完成。稍后我们对其进行测试。

在大多数情况下，我们将要修改Apache在请求目录时提供文件的方式。默认情况下，如果客户端用户访问某个目录的方式访问的话，是无法访问到*.php的，因为Apache默认是解析index.html的，所以我们需要修改Apache的配置文件，以让其能够默认识别index.php文件。

执行如下命令，编辑文件：

```
:~$ sudo vim /etc/apache2/mods-enabled/dir.conf
```
将如下内容：

```
<IfModule mod_dir.c>
    DirectoryIndex index.html index.cgi index.pl index.php index.xhtml index.htm
</IfModule>
```

更改为：

```
<IfModule mod_dir.c>
    DirectoryIndex index.php index.html index.cgi index.pl index.xhtml index.htm
</IfModule>
```

即将index.php优先级提高。保存、推出。重新启动apache 服务：

```
sudo systemctl restart apache2
```

### 安装PHP 的一些实用的模块

如果你是php开发者，熟悉php库的话，那么肯定对于php的模块有着重度的依赖，以下命令是搜索Ubuntu16.04所支持的所有的php相关的包：

```
aptitude search php- | less
```

根据实际需要安装相应的包即可，以下是笔者查看和安装一包名为php-cli的命令：

```
aptitude show php-cli
```
```
sudo apt-get install php-cli
```


### 测试PHP环境

为了测试我们的php环境是正确工作的，我们需要创建基本的脚本。

即info.php文件，创建php的info文件：
```
sudo vim /var/www/html/info.php
```
添加如下内容：

```
<?php
phpinfo();
?>
```
然后，在浏览器中访问URL ：http://IP/info.php  成功看到PHP信息的返回页面就说明，php工作正常。现在可以添加PHP程序了。

完成后，请删除此文件，若有需要可以随时创建，但是给恶意用户看到可不是一件好事。

```
sudo rm /var/www/html/info.php
```

## 总结

好了，你现在已经完成了在QingCloud下安装和配置最基本的LAMP 软件栈了，接下来可以做更多的事情了。
