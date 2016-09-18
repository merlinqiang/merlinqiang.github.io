---
layout:     post
title:      "ubuntu搭建Django"
subtitle:   "linux搭建lDjango"
date:       2016-07-05
author:     "MerlinFeng"
header-img: "img/post-bg-alitrip.jpg"
catalog: false
tags:
    - 服务器
---

> “Django”


最近看到python web 比较流行的django框架，所以研究下 搭个环境
磁盘初始化
1更新源：
sudo apt-get update

2装Apache、wsgi、Django、MySQL、MySQLdb

sudo apt-get install apache2 libapache2-mod-wsgi python-django mysql-server mysql-client python-mysqldb

中途需要输入数据库密码

3设置apache文件夹权限
cd /etc/apache2
vim apache2.conf
找到并更改：
<Directory />
        Options FollowSymLinks
        AllowOverride None
        #Require all denied
        Allow from all
</Directory>

4建立Django工程目录,并创建第一个django工程
mkdir /home/django
cd /home/django
django-admin startproject blog
执行完毕后会出现blog文件夹，并且里面有一些python文件

5建立wsgi
cd /etc/apache2
mkdir django
cd django
vim django.wsgi
填入下面内容：
import os
import sys
path = '/home/django/blog'
if path not in sys.path:
    sys.path.insert(0, '/home/django/blog')
os.environ['DJANGO_SETTINGS_MODULE'] = 'blog.settings'
import django.core.handlers.wsgi
application = django.core.handlers.wsgi.WSGIHandler()

cd /etc/apache2/sites-available
vim blog.conf

```
<VirtualHost *:80>
    #ServerName hello.djangoserver
    DocumentRoot /home/django/blog
    <Directory /home/django/blog>  
        Order allow,deny
        Allow from all
    </Directory>
    WSGIDaemonProcess mydjangosite processes=2 threads=15 display-name=%{GROUP}
    WSGIProcessGroup mydjangosite
    WSGIScriptAlias / /etc/apache2/django/django.wsgi
</VirtualHost>
```


6启用
sudo a2dissite 000-default
sudo a2ensite blog.conf
sudo service apache2 reload

访问本机IP，如果出现Congratulations on your first Django-powered page.字样，代表配置完成
python manage.py runserver