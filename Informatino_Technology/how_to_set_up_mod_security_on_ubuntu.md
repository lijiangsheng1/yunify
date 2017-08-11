# 在Ubuntu16.04下配置基于Apache的mod_security

## 前言

青云QingCloud WAF 产品正在紧锣密鼓的开发中，作为用户的你是不是已经迫不及待了？身为产品经理的我也是万般无奈，一方巴不得研发的兄弟快马加鞭，尽快出来，另一方又想满足客户，购买产品多多益善，于是左右为难，颇为惆怅。但是反过来一想，即要保障产品质量，又要保障项目进度。空闲之余，不如动手写写文档。于是，挑选了一款WAF 的开源项目——mod_security为例，让用户过过手瘾。

Mod_Security 是一款开源项目，是web应用防火墙的实现，可以在主流的Web服务器上运行，如Apache、Nginx以及IIS，Mod_Security 支持灵活的规则引擎来执行或简单、或复杂的操作，规则来自核心规则集（CRS），可以为各种破坏行为配置规则，如SQL注入、跨站点脚本、特洛伊木马、恶意的用户代理、会话劫持、以及其它的恶意行为。

本文就针对Apahce，对以模块的形式出现的mod_security的进行安装和配置。主要是配置，从而熟悉WAF 的工作原理和运行模式。

本文的前提是已经在Unbutn16.04操作系统中安装了[LAMP]()栈。

## 安装 mod_security

Ubuntu 默认的仓库是包含稳定版的mod_security的，直接安装即可：

```
:~$ sudo apt-get install libapache2-modsecurity
```
验证mod_security已经装载：

```
:~$ sudo apachectl -M | grep --color security
 security2_module (shared)

```

如果你的输出和上面是一致的，则说明一切是正常的。

mod_security 默认情况下安装的建议配置文件后缀是多余的，需要我们人工介入来重新命名：

```
sudo mv /etc/modsecurity/modsecurity.conf{-recommended,}
```

重新装载apache:

```
sudo systemctl reload apache2.services
```
此时，你会发现在Apache的默认日志目录下，多了一个文件：modsec_audit.log

## 配置 mod_security

将mod_security视为一个盒子的话，如果不给它一些规则的话，它其实是什么也做不了的。配置文件的默认设置是```DetectionOnly```，也就是说仅仅会记录到日志中，不会阻挠任何请求，我们会试着修改配置文件```modsecurity.conf```来改变一些行为。

```
:~$ sudo vim /etc/modsecurity/modsecurity.conf
```

找到下面一行内容：

```
SecRuleEngine DetectionOnly
```

然后更改为：

```
SecRuleEngine On
```

如果您在生产服务器上尝试使用此功能，请在测试完所有规则后再更改此指令。

另外一个需要修改的地方是```SecResponseBodyAccess```,此配置确定响应内容是否该缓冲（举例来说，由modsecurity来读取），这只有在需要数据泄露检测和保护时才需要。因此，保持开启将使用宝贵的资源并会增加日志文件大小。因此没有必要将之打开。

在配置文件中，找到下面一行内容：

```
SecResponseBodyAccess On
```

将之修改为：

```
SecResponseBodyAccess Off
```

接下来我们设置决定web应用能够post最大的数据量，需要修改两个配置项：

```
SecRequestBodyLimit 13107200
SecRequestBodyNoFilesLimit  131072
```

选项 ```SecRequestBodyLimit``` 指定最大的POST数据大小，来自客户端任何数据的大小若超过这个数据就会收到[ 413 Request Entity Too Large ](http://en.wikipedia.org/wiki/List_of_HTTP_status_codes#413)的错误，如果您的Web应用程序没有任何文件上传的功能，这个值可以设置的很小。

配置文件默认是 12.5 MB。

至于选项```SecRequestBodyNoFilesLimit```，它其实和```SecRequestBodyLimit``` 非常的相像，即限制了POST文件的最大数量，请尽可能的设置此项为低值。默认的是128KB。

接下来，我们再来看看另外一项会影响服务器性能的选项：```SecRequestBodyInMemoryLimit```，这个指令几乎不言自明;它指定多少“请求体数据”（POST后的数据）可以驻留内存。超出这个数字的则将被放在硬盘上（即SWAP），如果用户的硬盘是SSD 的话，这个值即使设置的很小也没有关系。尽管如此，如果由足够内存的话，还是尽可能的将此值设置的高些。

```
SecRequestBodyInMemoryLimit 131072
```

默认是128K。

## 测试 SQL 注入

在进一步配置规则之前，我们需要撰写一个有漏洞的脚本，能够被SQL注入的脚本。下面这个PHP登录脚本是最基本的，而且也没有会话保持，下面硬写的MySQL 密码请按照自己的环境进行更改：

```
<html>
<body>
<?php
    if(isset($_POST['login']))
    {
        $username = $_POST['username'];
        $password = $_POST['password'];
        $con = mysqli_connect('localhost','root','password','sample');
        $result = mysqli_query($con, "SELECT * FROM `users` WHERE username='$username' AND password='$password'");
        if(mysqli_num_rows($result) == 0)
            echo 'Invalid username or password';
        else
            echo '<h1>Logged in</h1><p>A Secret for you....</p>';
    }
    else
    {
?>
        <form action="" method="post">
            Username: <input type="text" name="username"/><br />
            Password: <input type="password" name="password"/><br />
            <input type="submit" name="login" value="Login"/>
        </form>
<?php
    }
?>
</body>
</html>
```

另存为```/var/www/html/login.php```，此脚本是一个用户登录的表单，输入正确的凭据就会显示一句：**“A Secret for you.”**

当然，我们要在数据库当中，创建相应的库、表、并插入用户名和密码：

```
create database sample;
connect sample;
create table users(username VARCHAR(100),password VARCHAR(100));
insert into users values('jesin','pwd');
insert into users values('alice','secret');
quit;
```

这个时候就可以利用浏览器来访问了：```http://yourwebsite.com/login.php```，然后将数据库的用户命和密码输入，比如用户名：```jesin```，密码```pwd```，浏览器会返回登录成功。如果输入不正确会看到信息：```Invalid username or password.```

我们是来玩SQL注入的，所以得验证脚本是否能够正常工作。接下来我们就使用SQL注入的方式，即输入特殊的字符串来越级，请在用户名的输入框内输入下面内容：

```
' or true --
```
一定要记得最后是有一个空格的，否则不会有效，密码框为空，然后直接点击login按钮。

不可思议，它竟然显示的是登录成功！！它是怎么办到的？因为SQL注入。


## 设置规则

mod_security 默认已经自带了很多规则了，足够你消化一阵子，也足够你应付一般的恶意行为了。而且它们还有一个正式的名称——CRS（核心规则集），它们在Ubuntu下存放的路径：

```
:~$ ls /usr/share/modsecurity-crs/ -l
total 44
drwxr-xr-x 2 root root  4096 Aug 10 18:13 activated_rules
drwxr-xr-x 2 root root  4096 Aug 10 18:13 base_rules
drwxr-xr-x 2 root root  4096 Aug 10 18:13 experimental_rules
drwxr-xr-x 2 root root  4096 Aug 10 18:13 lua
-rw-r--r-- 1 root root 13809 Oct 25  2014 modsecurity_crs_10_setup.conf
drwxr-xr-x 2 root root  4096 Aug 10 18:13 optional_rules
drwxr-xr-x 2 root root  4096 Aug 10 18:13 slr_rules
drwxr-xr-x 8 root root  4096 Aug 10 18:13 util
```

这些规则集的文档目录存放在：```/usr/share/doc/mod_security-crs ```下

要加载这些文件，我们需要告诉Apache到哪里找这些文件，编辑apahce 的mod_security配额文件```modsecurity.conf```:

```
sudo vim /etc/apache2/mods-enabled/security2.conf
```

在```<IfModule security2_module> </IfModule>```下添加如下两行内容：

```
Include "/usr/share/modsecurity-crs/*.conf"
Include "/usr/share/modsecurity-crs/activated_rules/*.conf"
```

目录```activated_rules```和apache的```mods-enabled```目录是一个道理，规则都是可用的：

```
/usr/share/modsecurity-crs/base_rules
/usr/share/modsecurity-crs/optional_rules
/usr/share/modsecurity-crs/experimental_rules
```

若是想要其它规则下的规则生效，需要在```activated_rules```目录下将某具体的规则做一个软链接，下面例子是将SQL注入的规则激活：

```
:~$ cd /usr/share/modsecurity-crs/activated_rules/
:/usr/share/modsecurity-crs/activated_rules$ ls
README
:/usr/share/modsecurity-crs/activated_rules$ sudo ln -s ../base_rules/modsecurity_crs_41_sql_injection_attacks.conf .
:/usr/share/modsecurity-crs/activated_rules$ ls -l
total 8
lrwxrwxrwx 1 root root   59 Aug 11 11:56 modsecurity_crs_41_sql_injection_attacks.conf -> ../base_rules/modsecurity_crs_41_sql_injection_attacks.conf
-rw-r--r-- 1 root root 5720 Oct 25  2014 README
```

让Apache 重新载入，以生效。

```
$ sudo systemctl reload apache2.service
```

现在打开我们刚才创建的login页面，然后再次尝试使用SQL注入，如果你已经更改过```SecRuleEngine```为```On```，那么就会看到 **403 Forbidden** 的错误；如果设置的仍然是```DetectionOnly```，那么就得去```modsec_audit.log```日志文件中查找响应的记录。

## 撰写自己的 mod_security 规则

在本节，我们会创建一个规则，只要用户在HTML表单中填写了```spammy```字符串，我们就会将之阻止。首先，我们创建一个PHP脚本，其可以让用户输入表单，并在用户提交后显示结果：

```
<html>
    <body>
        <?php
            if(isset($_POST['data']))
                echo $_POST['data'];
            else
            {
        ?>
                <form method="post" action="">
                        Enter something here:<textarea name="data"></textarea>
                        <input type="submit"/>
                </form>
        <?php
            }
        ?>
    </body>
</html>
```

保存到```/var/www/html/form.php```。

自定义的规则可以存放在系统中的任何地方，或增加到任意的配置文件中。这里我们另外建立一个文件：```/etc/modsecurity/modsecurity_custom_rules.conf```来进行测试。

在文件中添加如下内容：

```
SecRule REQUEST_FILENAME "form.php" "id:'400001',chain,deny,log,msg:'Spam detected'"
SecRule REQUEST_METHOD "POST" chain
SecRule REQUEST_BODY "@rx (?i:(pills|insurance|rolex))"
```

保存文件，重新装载apache，只要你的输入文本框中包含：```pills,insurance,rolex``` 三个词中的任何一个，就会看到后台将此提交成功的阻止。聪明的读者，可能已经学会规则的书写了，没错，它的书写格式就是：

```
SecRule VARIABLES OPERATOR [ACTIONS]
```
我们自己书写的规则，是利用了三个条件：

* REQUEST_FILENAME 访问的文件是form.php
* REQUEST_METHOD 的方法是POST
* REQUEST_BODY 表示的是匹配正则表达式：三个单词任选其一。

当成功匹配之后，mod_security就会阻止后续的访问。链动作模拟逻辑AND以匹配所有三个规则。

## 排除主机和目录

有的时候，我们需要将特定的目录或域名排除在外，尤其是运行特定的应用程序，比如phpMyAdmin,就应该阻止其执行SQL查询。比如排除CMS应用程序的管理后台也比较好，如WordPress。

要完全的针对VirtualHost禁用modsecurity，在```<VirtualHost>```一节下，请执行以下操作：

```
<IfModule security2_module>
    SecRuleEngine Off
</IfModule>
```

针对特定的目录：

```
<Directory "/var/www/wp-admin">
    <IfModule security2_module>
        SecRuleEngine Off
    </IfModule>
</Directory>
```

如果你不打算完全禁用ModSecurity的话，可以使用```SecRuleRemoveById```，通过指定特定的ID，来移除特定的规则或规则链。

```
<LocationMatch "/wp-admin/update.php">
    <IfModule security2_module>
        SecRuleRemoveById 981173
    </IfModule>
</LocationMatch>
```

981173的规则是这个样子的,防止SQL注入：

```
SecRule ARGS_NAMES|ARGS|XML:/* "([\~\!\@\#\$\%\^\&\*\(\)\-\+\=\{\}\[\]\|\:\;\"\'\´\’\‘\`\<\>].*?){4,}" "phase:2,t:none,t:urlDecodeUni,block,id:'981173',rev:'2',ver:'OWASP_CRS/2.2.9',maturity:'9',accuracy:'8',msg:'Restricted SQL Character Anomaly Detection Alert - Total # of special characters exceeded',capture,logdata:'Matched Data: %{TX.1} found within %{MATCHED_VAR_NAME}: %{MATCHED_VAR}',tag:'OWASP_CRS/WEB_ATTACK/SQL_INJECTION',setvar:tx.anomaly_score=+%{tx.warning_anomaly_score},setvar:tx.sql_injection_score=+1,setvar:'tx.msg=%{rule.msg}',setvar:tx.%{rule.id}-OWASP_CRS/WEB_ATTACK/RESTRICTED_SQLI_CHARS-%{matched_var_name}=%{tx.0}"
```


## 进一步阅读

本文仅供上手体验，对 WAF 有一个笼统的认识，更多配置内容，请参考[ModSecurity官方参考手册](https://github.com/SpiderLabs/ModSecurity/wiki/Reference-Manual)。
