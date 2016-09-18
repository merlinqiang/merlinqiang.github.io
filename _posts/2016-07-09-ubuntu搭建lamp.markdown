---
layout:     post
title:      "ubuntu搭建LAMP"
subtitle:   "linux搭建lamp环境以及phpmyadmin和rewrite等等"
date:       2016-07-05
author:     "MerlinFeng"
header-img: "img/post-bg-2015.jpg"
catalog: false
tags:
    - LAMP
    - 服务器
---

> “第一篇github.io 试水之作”




首先一条命令：apt-get install apache2 mysql-server mysql-client php5 php5-gd php5-mysql

设置Ubuntu文件执行读写权限

LAMP 组建安装好之后，PHP网络服务器根目录默认设置是在：/var/www。由于Linux系统的安全性原则，改 目录下的文件读写权限是只允许root用户操作的，所以我们不能在www文件夹中新建php文件，也不能修改和删除，必须要先修改/var/www目录的 读写权限。在界面管理器中通过右键属性不能修改文件权限，得执行root终端命令：sudo chmod 777 /var/www。然后就可以写入html或php文件了。如果对777表示的文件权限不是很清楚，可以到这篇文章中看看：文件权限666是什么意思。

如何安装phpmyadmin-Mysql 数据库管理
命令：sudo apt-get install phpmyadmin 安装开始。

phpmyadmin设置：

在 安装过程中会要求选择Web server：apache2或lighttpd，选择apache2，按tab键然后确定。然后会要求输入设置的Mysql数据库密码连接密码 Password of the database’s administrative user。

然后将phpmyadmin与 apache2建立连接，以我的为例：www目录在/var/www，phpmyadmin在/usr/share/phpmyadmin目录，所以就用 命令：sudo ln -s /usr/share/phpmyadmin /var/www 建立连接。

phpmyadmin测试：在浏览器地址栏中打开http://localhost/phpmyadmin。

Ubuntu LAMP 如何配置Apache

1. 启用 mod_rewrite 模块

终端命令：sudo a2enmod rewrite

重启Apache服务器：sudo /etc/init.d/apache2 restart

Apache 重启后我们可以测试一下，在/var/www目录下新建文件test.php，写入代码：   保存，在地址栏输入http://127.0.0.1/test.php 或 http://localhost/test.php ，如果正确出现了php 配置信息则表明LAMP Apache已经正常工作了(记得重启Apache服务器后再测试)。

2.设置Apache支持.htm .html .php

sudo gedit /etc/apache2/apache2.conf

或sudo gedit /etc/apache2/mods-enabled/php5.conf

在打开的文件中加上

AddType application/x-httpd-php .php .htm .html 即可。

LAMP配置之Mysql测试

上面php,Apache 都已经测试过了，下面我们再测试一下Mysql 数据库是否已经正确启用。

在/var/www目录下新建 mysql_test.php：

    $link = mysql_connect("localhost","root","020511");

    if (!$link)

    {

    die('Could not connect: ' . mysql_error());

    }

    else echo "Mysql已经正确配置";

    mysql_close($link);

    ?>



保存退出，在地址栏输入http://127.0.0.1/mysql_test.php，显示”Mysql 已经正确配置”则表示OK了，如果不行，重启Apache服务器后再试一下。

解决Firefox浏览器显示中文乱码等问题

上面在FireFox浏览器中打开mysql_test.php或phpmyadmin测试时，如果出现了中文乱码，则是默认语言设置问题，解决方法如下：

打开apache配置文件： udo gedit /etc/apache2/apache2.conf，在最后面加上：AddDefaultCharset UTF-8，如果还是乱码的，再将UTF-8改用gb2312。

重启Apache：sudo /etc/init.d/apache2 restart  再刷新mysql_test.php 中文乱码没有了。

如果要人工启动mysql：mysql -u root -p，根据提示输入密码。

如果重启Apache时出现：

* Restarting web server apache2

apache2: Could not reliably determine the server’s fully qualified domain name, using 127.0.1.1 for ServerName

apache2: Could not reliably determine the server’s fully qualified domain name, using 127.0.1.1 for ServerName

则还是修改apache配置文件：sudo gedit /etc/apache2/apache2.conf，在文件最后设置：ServerName 127.0.0.1

LAMP组件经常使用的几个终端命令

重启 apache：sudo /etc/init.d/apache2 restart

重启mysql：sudo /etc/init.d/mysql restart

配置 php.ini：sudo gedit /etc/php5/apache2/php.ini

配置 apache2.conf：sudo gedit /etc/apache2/apache2.conf

配置 my.cnf：sudo gedit /etc/mysql/my.cnf

PHP CGI ：sudo /var/www/cgi-bin/




