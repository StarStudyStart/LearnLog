# Collections-容器数据类型

> 为了实现特定目标的一系列容器集合

## `namedtuple()`-命名元组的工厂函数

命名元组赋予每个元素一个名称，具有可读性和自文档性

可以像字典一样通过‘名字‘访问一个元组中的任意元素，适用于`csv、excel`等文件的写入及数据读取

### `collections.namedtuple(typename, fields_names, *， rename=False, ...)`

 命名元组的工厂函数，返回一个名为`typename`的元组子类；既可以像普通元组一样通过索引值访问元组的元素，也可以通过字段名访问指定元素

`field_names`: 字段名序列。如：`['x', 'y', 'z']`，或者以空格字符分割的完整字符串，如：`"x y z"`

- 命名元组实例不用为每条数据单独标记‘键-值’（通过索引方式绑定名称），所以比字典更轻量、占用内存更小；

- 命名元组定义时已经规定了每条元组的长度以及每个元素对应的名称，命名元组实例化时必须与字段一一对应；这样就是为什么命名元组更适用于一些固定长度数据的原因，如`数据库`、`csv`、`excel`文件等数据的读取/写入；

- 为了方便封存数据，习惯将命名元组类赋值给一个`typename`的变量

命名元组尤其有用于赋值 [`csv`](https://docs.python.org/zh-cn/3/library/csv.html#module-csv) [`sqlite3`](https://docs.python.org/zh-cn/3/library/sqlite3.html#module-sqlite3) 模块返回的元组

```
EmployeeRecord = namedtuple('EmployeeRecord', 'name, age, title, department, paygrade')

import csv
for emp in map(EmployeeRecord._make, csv.reader(open("employees.csv", "rb"))):
    print(emp.name, emp.title)

import sqlite3
conn = sqlite3.connect('/companydata')
cursor = conn.cursor()
cursor.execute('SELECT name, age, title, department, paygrade FROM employees')
for emp in map(EmployeeRecord._make, cursor.fetchall()):
    print(emp.name, emp.title)
```

### 命名实例的方法

#### `somenametuple._make(iterable)`

将可迭代对象的元素直接实例化为命名元组实例，相当于`somenametuple(*iterable)`

```
>>> Point._make([1,2])
Point(x=1, y=2)
>>> Point(*[1,2])
Point(x=1, y=2)
```

#### `somenametuple._replace(**kwargs)`

替换指定字段的值并返回一个新的元组实例

```
>>> p = Point(*[1,2])
>>> p_updated = p._replace(y=2, x=1)
>>> _
Point(x=2, y=1)
```

```
>>> p_updated = p._replace(y=2, x=1,z=3)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "E:\Python-3.7.9\lib\collections\__init__.py", line 416, in _replace
    raise ValueError(f'Got unexpected field names: {list(kwds)!r}')
ValueError: Got unexpected field names: ['z']
>>>
```

文档字符串可以自定义，通过直接赋值给 `__doc__` 属性:	

```
>>> Book = namedtuple('Book', ['id', 'title', 'authors'])
>>> Book.__doc__ += ': Hardcover book in active collection'
>>> Book.id.__doc__ = '13-digit ISBN'
>>> Book.title.__doc__ = 'Title of first printing'
>>> Book.authors.__doc__ = 'List of authors sorted by last name'
```

