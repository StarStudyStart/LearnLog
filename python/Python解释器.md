# Python解释器

### 预加载命令--Python交互式解释器

设置`PYTHONSTARTUP`环境变量，指向配置文件

```
# 配置文件
import os
import sys

sys.ps1 = '-->>'
sys.ps2 = '***'

# 启动解释器，预加载一些python命令
$ Pyhon3
```

### 预加载命令-Python解释器

```
# 查看site-packages路径
python3 -m site --user-site

# 新建usercustomize.py文件，填写预加载的命令
print("Hello world!")

python解释器每次运行都会打印"Hello world！"(包含交互式解释器)

# 消除预加载
python -s  # 不将site-packages加到sys.path中

```

### 不启动解释器,直接运行python代码

```
python3 -c command # 不必编辑文件，直接运行python代码
python3 -c "import datetime;print(datetime.datetime,now())"
```

### 交互模式下，上次输出的表达式会赋给变量 `_`