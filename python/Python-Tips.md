# Tips

## 长字符串换行

- 1、相邻的两个或多个 *字符串字面值* （引号标注的字符）会自动合并

```
>>> ('Py' 
'thon')
'Pythonow'
```

- 2、长字符串换行，在行尾添加 `\` 即可

 ```
 """\
 Usage: thingy [OPTIONS]
 -h                        Display this usage message
 -H hostname               Hostname to connect to
 """)

# 输出如下
Usage: thingy [OPTIONS]
-h                        Display this usage message
-H hostname               Hostname to connect to
 ```

##   函数入参

主要分为三种：必选参数->默认参数->可选参数->关键字参数

特殊入参

`/`之前为位置参数

`*`之后为关键字参数

```
def f(pos1, pos2, /, pos_or_kwd, *, kwd1, kwd2):
      -----------    ----------     ----------
        |             |                  |
        |        Positional or keyword   |
        |                                - Keyword only
         -- Positional only
```

下面的函数定义中，`kwds` 把 `name` 当作键，因此，可能与位置参数 `name` 产生潜在冲突：

```
def foo(name, **kwds):
    return 'name' in kwds
```

调用该函数不可能返回 `True`，因为关键字 `'name'` 总与第一个形参绑定。例如：

\>>>

```
>>> foo(1, **{'name': 2})
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: foo() got multiple values for argument 'name'
>>>
```

加上 `/` （仅限位置参数）后，就可以了。此时，函数定义把 `name` 当作位置参数，`'name'` 也可以作为关键字参数的键：

```
def foo(name, /, **kwds):
    return 'name' in kwds
>>> foo(1, **{'name': 2})
True
```

## 文件编码失败的解决方法

在读取文件时，遇到文件中的字符超出编码格式本身，如：文件中含有一个超出`utf8`范围内的字符，而其他文件都可以正常以`utf8`编解码

提供两种解决方案：

1、保留超出编码格式的部分字符

-  利用`chardet`模块（第三方库，需安装）获取文件的编码格式，获取文件本身的编码格式解码，这样就可以忽略编码错误
- 拿到文件的编码格式后，在用指定的格式进行读取，就不容易

```
# 利用chardet获取文件的编码格式,根据文件本身的编码格式解码，这样就可以忽略编码错误
def get_ecnoding():
    with open(file, 'rb') as f:
        data = f.read()
    encoding = chardet.detect(data).get('encoding')
    return encoding

# 示例
>>> import chardet
>>> with open(r'./.bash_history','rb') as f:
...     data = f.read()
...
>>> chardet.detect(data)
{'encoding': 'ascii', 'confidence': 1.0, 'language': ''}
>>> chardet.detect(data).get('encoding')
'ascii'

# 拿到文件的编码格式后，在用指定的格式进行读取，就不会报编码错误了
```

2、利用正则表达式替换所有越界字符

```
re.sub(r'([\u0100-\uffff]+)', lamda x:x.group(0).encode().decode('utf8','ignore'), file_content )
```

## 写入`csv`文件时，中文乱码

通常写入`csv`文件的操作如下：

```
>>> with open(r'test.csv','w',encoding='utf-8') as f_csv:
...     csv_writer=csv.writer(f_csv)
...     csv_writer.writerow(['序号', '内容', '备注'])
		...
...
```

但是上面这种方式写入后，使用`excel`工具打开，中文字符全是乱码状态。原因时带有`BOM`格式的数据才能在`excel`中正常显示，所以`utf8`写入`csv`文件中文字符时，会显示乱码

解决方案如下：

以带`BOM`的`utf-8`格式写入数据即可, `python`没有自带这种编码格式，因此需要导入`codecs`

```
import codecs
>>> f = codecs.open(r'test.csv','w',encoding='utf-8-sig')
>>> csv_writer=csv.writer(f)
>>> csv_writer.writerow(['序号', '内容', '备注'])
>>> f.close()
>>>
#  再次打开乱码解决
```

# 写入`json`文件, 中文乱码

调用`json.dump`时`ensure_ascii`默认为True，这会使得所有非`ascii`自动转义。如果改为`False`即可原样输出中文字符

```
json.dump(obj, fp, ensure_ascii=False)
```

