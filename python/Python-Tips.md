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

###   函数入参

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