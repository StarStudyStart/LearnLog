# `pip freez`环境迁移

项目迁移环境后，需要重新安装项目依赖库

## 在原有环境下生成安装列表

```
pip freeze > requirements.txt
```

## 新环境下一键安装

```
pip install -r requirements.txt
```

# `pip install mysqlclient`时`NOT FOUND msyql_conf`报错

```
# 解决方案
python -m pip install --upgrade setuptools
python -m pip install --upgrade pip

sudo apt-get install libmysqlclient-dev
```

