# 常见的字符串操作

# 常用模块

常见的字符串集合

1、数字集合

`string.digits`

```
>>> string.digits
'0123456789'
```

2、打印标点符号

`string.punctuation`

```
>>> string.punctuation
'!"#$%&\'()*+,-./:;<=>?@[\\]^_`{|}~'
```

3、打印`ascii`字符

 `string.ascii_letters`

```
>>> import string
>>> string.ascii_letters
'abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ'
```

4、打印空白字符

`string.whitespace`

```
>>> string.whitespace
' \t\n\r\x0b\x0c'
>>>
```



# 格式化字符串

`str.format()`方法和`Formatter`类使用相同的格式字符串语法，但`string.Formatter`类，可以自定义字符串格式化方法。

## 1、替换字符串

格式化字符串将花括号`{}`里的内容视为`replacement_filed`, 花括号外的字段视为字面文本

语法如下：

```
replacement_filed ::= "{" [filed_name] ["!"conversion][":"format_sepc] "}"
filed_name        ::=  arg_name("."attribute_name|"["element"]").*
arg_name          ::=  [identifier | digit+]
attribute_name    ::=  identifier
element_index     ::=  digit+ | index_string
index_string      ::=  <any source character except "]"> +
coversion         ::= "r" | "s" | "a"
format_sepc       ::= <described in next section>
```

### `filed_name`

`filed_name`主要用来从`format()`方法中取出对应入参来填充字段，并可以调用特定的参数方法

1. ``arg_name`对应一个数字或者一个关键字，当`arg_name`为一个数字是对应`format()`方法中的位置参数（`arg_name`作为数字并且默认为依次递增0,1,2,3...时，可以省略）。当`arg_name`为一个关键字时对应的则是命名参数。

    ```
    >>> "run cmd {0!r},{1}".format("ls", "show all files in current dir")  # 位置参数
    "run cmd 'ls',show all files in current dir"
    >>> "run cmd {cmd!r},{sepc}".format(cmd = "ls", sepc = "show all files in current dir") # 关键字参数
    "run cmd 'ls',show all files in current dir"
    ```

2. 通过`.`可以调用入参的`__get_attr__()`方法获取入参对应的属性；

    ```
    >>> import string
    >>> "The digits letters: {0.digits}".format(string)
    'The digits letters: 0123456789'
    ```

3. 通过`[]`则可以调用入参的`__get_item__()`方法获取对应的元素，`[]`可以是数字或者字符串（`[]`里的字符串不需要单/双引号）

    ```
    # 单引号
    >>> "Name: {d[name]}, Sexy:{d[sexy]}".format(d=d)
    'Name: liyabin, Sexy:male'
    
    # 数字
    >>> "The third element is {0[2]} in list {0}".format(list)
    'The third element is 4 in list [1, 3, 4]'
    ```

### `conversion`

`conversion`在格式化字符串前，强制转化字符串。转换旗标如下

`a` ==> `ascii()`

`s` ==> `str()`

`r` ==> `repe()`

### `format_spec`

对字符串进行一系列格式化处理，使字符串按照预想的规则和格式呈现。如：宽度、填充字符、有效长度等，详细在下一章节

## 2、格式化说明`format_spec`

格式化规则及格式：用于替换字段的内部，用于定义单个值如何呈现

格式化语法如下：

```
format_spec     ::= [fill[align]][sign][#][0][width][group_option][.precsion][type]
fill            ::=  <any character>
align           ::=  "<" | ">" | "=" | "^"
sign            ::=  "+" | "-" | " "
width           ::=  digit+
grouping_option ::=  "_" | ","
precision       ::=  digit+
type            ::=  "b" | "c" | "d" | "e" | "E" | "f" | "F" | "g" | "G" | "n" | "o" | "s" | "x" | "X" | "%
```

1. `fill`:填充字符
2. `align`:对齐方式
3. `sign`: 标识位（仅用于数字）
4. `#`: 进制转换（仅用于数字）
5. `0`：官方文档未明确说明，应该是补0用的（仅用于数字）
6. `width`：指定最小的总字段宽度
7. `group_tion`:  千位分隔符 | 进制分隔符
8. `precsion`: 小数点后的有效长度
9. `type`: 指定呈现类型

这里的转换格式十分复杂，详细信息查阅官网资料

## 3、格式示例

对齐文本以及指定宽度:

```
>>> '{:<30}'.format('left aligned')
'left aligned                  '
>>> '{:>30}'.format('right aligned')
'                 right aligned'
>>> '{:^30}'.format('centered')
'           centered           '
>>> '{:*^30}'.format('centered')  # use '*' as a fill char
'***********centered***********'
```

替代 `%+f`, `%-f` 和 `% f` 以及指定正负号:

```
>>> '{:+f}; {:+f}'.format(3.14, -3.14)  # show it always
'+3.140000; -3.140000'
>>> '{: f}; {: f}'.format(3.14, -3.14)  # show a space for positive numbers
' 3.140000; -3.140000'
>>> '{:-f}; {:-f}'.format(3.14, -3.14)  # show only the minus -- same as '{:f}; {:f}'
'3.140000; -3.140000'
```

替代 `%x` 和 `%o` 以及转换基于不同进位制的值:

```
>>> # format also supports binary numbers
>>> "int: {0:d};  hex: {0:x};  oct: {0:o};  bin: {0:b}".format(42)
'int: 42;  hex: 2a;  oct: 52;  bin: 101010'
>>> # with 0x, 0o, or 0b as prefix:
>>> "int: {0:d};  hex: {0:#x};  oct: {0:#o};  bin: {0:#b}".format(42)
'int: 42;  hex: 0x2a;  oct: 0o52;  bin: 0b101010'
```

使用逗号作为千位分隔符:

```
>>> '{:,}'.format(1234567890)
'1,234,567,890'
```

表示为百分数:

```
>>> points = 19
>>> total = 22
>>> 'Correct answers: {:.2%}'.format(points/total)
'Correct answers: 86.36%'
```

使用特定类型的专属格式化:

```
>>> import datetime
>>> d = datetime.datetime(2010, 7, 4, 12, 15, 58)
>>> '{:%Y-%m-%d %H:%M:%S}'.format(d)
'2010-07-04 12:15:58'
```

# 格式化字面量

在字符串定义前加`f`，既是格式化字面的定义方法，其中所有`{...}`的表达式会根据一定的语法规则转换目标字符串

与格式化字符串不同的是，格式化字面量可以传入各种表达式，如

```var
>>> a = 1
>>> b = 2
>>> f'a + b = {a+b}'
'a + b = 3'
>>>
```

其他语法规则均与上一章节**格式化字符串**类似

# 模板字符串

```string.Template```是基于`$`字符的替换

- `$$` 转义字符，代表`$`本身

- `$identifier` 替换占位符，匹配一个`identifier`的映射键，直到下一个非标识符字符(相当于正则表达式中的`\W`)终结

- `${identifier}`明确替换占位符的边界， 如：`${non}ification`

`string.Template(template)`：传入模板字符串，实例化模板类

`Template.substitute(mapping={}`： 执行替换返回，替换后的字符串

```
>>> s = "I'm $who , ${years}years old"
>>> t = string.Template(s)
>>> t.substitute({"who":"yabin", "years":27})
"I'm yabin , 27 years old"
>>>
```

## 日常工作中，常用的第三方模板替换库：`jinja2`

