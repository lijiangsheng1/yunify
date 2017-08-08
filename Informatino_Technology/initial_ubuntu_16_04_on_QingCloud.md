# 正确的初始化 Ubuntu 16.04

## 介绍

当你在 QingCloud 中创建了一台安装Ubuntu 16.04 服务器操作系统的时候，在进行进一步部署、配置之前，应该要对其进行一些必要的配置，以对系统的安全性和可用性进行增强，并为未来的使用过程中有一个不错的基础。

## 步骤一：以Root身份登录系统

想要登录你在 QingCloud 所创建的系统，你必须为之[申请一个公网IP](https://docs.qingcloud.com/guide/compute_network/topology.html#id3)，并下载由 QingCloud 控制台提供的个人私钥，（具体步骤请参考：[快速上手指南](https://docs.qingcloud.com/guide/quick_start.html)连接主机方法二。）假设你的客户端是Linux或MacOS，在终端执行如下命令：

```
chmod 400 kp-e0nghc3a
```

其中 ```kp-e0nghc3a```是刚刚下载的私钥。

然后就可以使用ssh客户端，登录到主机了：

```
~]$ ssh -i kp-e0nghc3a root@139.198.190.113
```
敲完回车，首次登录会有一个IP地址的认证，询问你是否信任这个主机。直接回车即可。

### 关于Root用户

在Linux操作系统中，Root用户表示的是超级用户，此用户拥有的权限极为的大，比如可以删除系统文件等。也正是因为它的权限过大，为了避免造成人为的一些错误而导致的系统损坏，QingCloud 并不建议使用Root来做一些日常的工作。

> 常见的root执行：``` rm -rf /* ```，或者是变相的环境变量错误：``` userdel -rf ual``` 而ual根本不存在，相当于```rm -rf /* ```

类似的悲剧不胜枚举，比如今年早些时候，[GitLab事故之技术详叙：](http://www.infoq.com/cn/news/2017/02/Technical-details-accident-GitLa)

## 步骤二：创建新普通用户

如果你在步骤一中已经顺利以root登录了系统，那么可以执行下面步骤，创建一个叫做zhangsan的用户：

```
~# adduser zhangsan
Adding user `zhangsan' ...
Adding new group `zhangsan' (1002) ...
Adding new user `zhangsan' (1002) with group `zhangsan' ...
Creating home directory `/home/zhangsan' ...
Copying files from `/etc/skel' ...
Enter new UNIX password:
Retype new UNIX password:
passwd: password updated successfully
Changing the user information for zhangsan
Enter the new value, or press ENTER for the default
	Full Name []: zhangsan      
	Room Number []: 21
	Work Phone []: 21
	Home Phone []: 21
	Other []: nothing else.
Is the information correct? [Y/n] y
```
由输出可以看到，adduser命令帮助你创建了用户名、加入了用户组、创建了主目录、继承了其它条件，并会要求你设置密码，以及回答一些用户属性的问题。

## 步骤三： 赋予新用户可执行特权程序

上述步骤中，我们所创建的用户是以普通用户，还不能执行一些系统管理任务，对于系统文件的操作是没有权限的。

为了避免发生我们使用su,从普通用户切换到root用户，然后再切回的时间浪费和可能的操作失误，比如关机、误删除等。

还好，早已有人发明了```sudo```这个超级棒的程序，它使得普通用户仍然可以执行执行超级用户的命令，但前提是约定好使用那些，并明确在每次使用时都在命令前执行```sudo```。

要为新的用户赋予执行超级用户的某些命令，我们需要将此新用户加入到```sudo```的用户组当中，因为，Ubuntu 16.04 默认情况下,用户若是属于```sudo```用户组，即可执行```sudo```的命令。

执行以下命令，将用户zhangsan添加到```sudo```用户组：

```
# usermod -aG sudo zhangsan
```

## 步骤四：放弃root登录，以新用户登录

为了彻底解决以root登录的方式，建议使用新用户登录环境，因为新用户已经是```sudo```的成员了。

我们知道，在本地以私钥登录，那么服务器一定是有公钥存放的。那么我们就移动root的```authorized_keys ```到zhangsan的主目录下，并改变属性和相应的权限，具体命令如下：

```
~# su - zhangsan
~$ mkdir .ssh
~$ sudo mv /root/.ssh/authorized_keys .ssh/
~$ sudo chown zhangsan:zhangsan .ssh/authorized_keys
```

退出登录，重新从本地以zhangsan的身份登录。

```
$ ssh -i kp-e0nghc3a zhangsan@139.198.190.113
```
此时，以root身份已经无法登录：

```
$ ssh -i kp-e0nghc3a root@139.198.190.113
Permission denied (publickey).
```

## 步骤五：禁止密码登录

QingCloud 下的 Ubuntu 16.04 的 OpenSSH 服务默认是不允许用户以密码的方式登陆系统的，例如我们以zhangsan的身份登陆，不是用私钥：

```
]$ ssh zhangsan@139.198.190.113
Permission denied (publickey).
```

如果你想要使用密码登陆，请修改相应的配置文件，不过我们强烈建议你不要那么做。

## 步骤六：设置基本的防火墙

QingCloud 默认在IaaS 平台提供单独的防火墙服务，打开禁用端口，随心所欲：请参考文档：[快速上手指南](https://docs.qingcloud.com/guide/quick_start.html) 。

但是，Ubuntu 16.04 是提供一个基于应用的简易防火墙功能的，等于是你的第二道防线，以下是配置步骤，我们以 OpenSSH 为例说明。

安装：

```
sudo aptitude install -y ufw
```

列出现有的应用列表：

```
sudo ufw app list
```
允许OpenSSH应用通过：

```
$ sudo ufw allow openssh
Rules updated
Rules updated (v6)
```
启用，生效：
```
$ sudo ufw enable
```

查看现有状态：
```
$ sudo ufw status
Status: active

To                         Action      From
--                         ------      ----
OpenSSH                    ALLOW       Anywhere                  
OpenSSH (v6)               ALLOW       Anywhere (v6)             

```
## 万事俱备

有了上述最为基本的配置，禁止使用root直接登陆系统，这是安全的第一步，也是一个系统管理员应该做的第一步。接下来就可以安装其它软件了。
