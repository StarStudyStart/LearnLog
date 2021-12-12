# mysql

## mysql的安装

```
sudo apt install mysql-client mysql-server
```

## mysql8.0 首次安装后修改密码

### 以管理管身份登陆 root账户

```
# 不用输入密码，直接回车即可登入
sudo mysql -u root -p 
```

### 将authentication_string置空

```
# 切换数据库
> use mysql; 
> update user set authentication_string='' where user='root';
# 退出mysql
> exit
```

### 修改密码

```
# 重新连接mysql
sudo mysql -u root -p 

# 切换数据库
> use mysql; 

# 修改密码
> alter user 'root'@'localhost' identified by 'newpassword';
```
### 重启mysql服务

`service mysql restart`

>**注：在mysql8.0以上版本，**
>
> **update mysql.user set password='newpassword' where user='root';**
>
> **和**
>
> **update mysql.user set password=PASSWORD('newpassword') where User='root'; **
>
>**这两条命令已经不起作用了。**

## mysql的卸载

```
# 删除mysql的数据文件
sudo rm /var/lib/mysql/ -R

# 删除mysql的配置文件
sudo rm /etc/mysql/ -R

# 自动卸载mysql（包括server和client）
sudo apt-get autoremove mysql* --purge
sudo apt-get remove apparmor

# 然后在终端中查看MySQL的依赖项
dpkg --list|grep mysql
```

## mysql本地登录命令

```
mysql -h hostname -u username -p password
```

## `mysql`授权远程登录

1. 以root账户登录数据库管理系统

2. 切换到名为`mysql`数据库

3. `select host, user from user;`查看当前账户及其登录权限

4. 授权root或其他账户远程登录

    > 从mysql 8.0开始grant命令不会默认创建user账户了，必须手动创建

    ```
    > create user 'root'@'%' identified by 'password';
    > grant all privileges on *.* to 'root'@'%' with grant option;
    > flush privileges;
    ```

5. 修改文件`sudo vim /etc/mysql/mysql.conf.d/mysqld.cnf`，将监听地址改为`0.0.0.0`

    ```
    # 原本监听地址为127.0.0.1，仅监听来自本机的端口访问
    yabin@yabin:~$ sudo grep -nr "bind-add" /etc/mysql/
    [sudo] password for yabin:
    /etc/mysql/mysql.conf.d/mysqld.cnf:31:bind-address              = 127.0.0.1
    /etc/mysql/mysql.conf.d/mysqld.cnf:32:mysqlx-bind-address       = 127.0.0.1
    # 将监听地址改为 0.0.0.0, 监听所有ip地址的访问
    sudo vim /etc/mysql/mysql.conf.d/mysqld.cnf
    bind-address            = 127.0.0.1 改为
    bind-address            = 0.0.0.0
    ```



## 数据库导出

```
mysqldump -h 192.168.124.40 -u user -p databases_name table_name > table.sql
```

## 数据库导入

```
# 第一种方式 mysql cli内部
use database xxx;
source table.sql

# 第二种方式  命令行
msyql -u root -p databases_name < table.sql
```

