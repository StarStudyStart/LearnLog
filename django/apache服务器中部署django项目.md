# 安装apache2

## 安装apache和mod_wsgi

```
sudo apt-get install apache2

# Python 2
sudo apt-get install libapache2-mod-wsgi

# python3
sudo apt-get install libapache2-mod-wsgi-py3
```



## 确认安装的apache2版本号

```
apachectl -v
```

## `apache`新建一个网站配置文件

`/etc/apache2/sites-available`目录下新建一个apache的配置文件

`sudo vi /etc/apache2/sites-available/sitename.conf`

```
<VirtualHost *:8888>
    ServerName www.siteforlog.com
    ServerAlias otherdomain.com
    ServerAdmin 18792146966@163.com

    Alias /media/ /home/yabin/mysite/learning_log/learning_log/media/
    Alias /static/ /home/yabin/mysite/learning_log/static/

    <Directory /home/yabin/mysite/learning_log/media/>
        Require all granted
    </Directory>

    <Directory /home/yabin/mysite/learning_log/static>
        Require all granted
    </Directory>
	# wsgi文件位置
    WSGIScriptAlias / /home/yabin/mysite/learning_log/learning_log/wsgi.py
    # 以后台守护进程方式启动wsgi
    # python-home 虚拟环境位置
    # python-python 项目位置
    WSGIDaemonProcess siteforlog.com python-home=/home/yabin/mysite/ll_env  python-path=/home/yabin/mysite/learning_log
    WSGIProcessGroup siteforlog.com
    
    <Directory /home/yabin/mysite/learning_log/learning_log/>
    <Files wsgi.py>
        Require all granted
    </Files>
    </Directory>
</VirtualHost>
```

如果apache版本号是 2.2.x，用下面的代替  `Require all granted`

```
Order deny,allow``Allow from all
```

## 修改端口号

修改监听端口的配置文件，新增监听端口`8888`

`sudo vim /etc/apache2/ports.conf`

```

Listen 80
# 新增端口
Listen 8888

<IfModule ssl_module>
        Listen 443
</IfModule>

<IfModule mod_gnutls.c>
        Listen 443
</IfModule>
```

## 设置文件目录权限

```
cd /home/tu/
sudo chmod -R 644 project
sudo find project -type d | xargs chmod 755
```

在 Linux 服务器上，用户上传目录还要设置给 www-data 用户的写权限，下面的方法比较好，不影响原来的用户的编辑。

假如上传目录为 zqxt/media/uploads 文件夹,进入media文件夹，将 uploads 用户组改为www-data，并且赋予该组**写权限**:

```
cd media/ 
# 进入media文件夹
sudo chgrp -R www-data uploads 
sudo chmod -R g+w uploads
```

## 激活新的网站配置

```
sudo a2ensite sitename 
或
sudo a2ensite sitename.conf
```

## 重启apache2的服务

```
# 重启apache2的服务
service apache2 restart

# 查看启动状态
systmectl status apache2.service

#　查看apache2报错日志
cat /var/log/apache2/error.log
```

