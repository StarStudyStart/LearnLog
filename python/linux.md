# 1、Ubuntu安装时报Unable to acquire the dpkg frontend lock

```shell
sudo rm /var/lib/apt/lists/lock
sudo rm /var/cache/apt/archives/lock
sudo rm /var/lib/dpkg/lock* (亲测仅这一条命令即可使用)
然后重新配置软件包。在终端中运行：
sudo dpkg --configure -a 和 sudo apt update
```

