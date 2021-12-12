# 获取当前sh文件所在目录

```
# 无论脚本在何处执行，总能获取脚本所在的目录位置
CUR_DIR=$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)
```

```
[root@192 /]# pwd
/
[root@192 /]# source root/shell_script/test.sh
/root/shell_script
```

