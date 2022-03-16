# re-正则表达式

# 概述

> 正则表达式：使用特定的规则、模式，匹配一系列符合约束的字符串，从而快速得到结果、多用于字符串查找，文本处理
>
> 定位：文件处理

正则表达式是一种微小专业化的语言，`python`中通过`re`模块提供正则表达式引擎的入口，(`re`
模块是`python`的扩展`c`库)

# `re`模块的使用

`re`模块的使用主要分为三个部分

1. 正则表达式：正表达式的规则，==> 定义正则表达式

2. 正则对象 : `re.compile(pattern, flag)`   将正则表达式传入`compile()`方法中即可生成正则对象

3. 匹配对象：正则对象调用`search()`、`match()`方法的返回对象，既是匹配对象`Match object`，`match.group()`获取匹配值

```
>>> pattern = re.compile(r"\d+", re.A) # 正则对象  其中"\d+"就是代表的正则表达式
>>> pattern.search("tel number: 123456789")	
<re.Match object; span=(12, 21), match='123456789'> # 匹配对象
>>> _.group()  # 获取匹配值
'123456789'
>>>
```

## 正则表达式

`python`的`re`库：主要支持两种字符`bytes`字符，`unicode`字符

正则表达式的匹配机制，主要分为两类字符：

- 普通字符

- 特殊字符

### 普通字符

普通字符仅代表字符本身，如：`a`仅代表字符本身

### 特殊字符

`.`可以代表除换行符外所有字符，使用特殊的`flag: re.S`可以使得`.`匹配任意字符

`^`匹配字符串开头，`^`后跟的就是字符串的头一个字符。如：`^[a-z]`仅匹配a到z开头的字符串，其他字符串开头则无法匹配，`re.M`多行模式下，匹配字符串开头，以及下一个换行符之后的首字符

`$`匹配字符串结尾, `$`前的就是字符结尾的最后一个字符。如：`.*?a$`仅匹配`a`结尾的字符串  如：`"ba"`则能正常匹配，`"ab"`则匹配不到结果

```
>>> re.search(r".*?a$", "ba")
<re.Match object; span=(0, 2), match='ba'>
>>> re.search(r".*?a$", "ab")
>>>
```

代表数量的字符

`*` 前一个字符重复0次或者多次，说明前一个字符可能不存在或者存在很多次

`+` 前一个字符重复1次或者多次，前一个字符至少重复一次，或者存在很多次

`?` 前一个字符存在0次或者1次，前一个字符不存在或者存在

`{m,n}`前一个字符至少存在`m`次，最多存在`n`次;`m`或`n`可以省略，省略m代表字符最少为0次，省略n代表字符最多为无穷次；

```
>>> re.search(r"^a{2,3}b", "aab")
<re.Match object; span=(0, 3), match='aab'>
>>> re.search(r"^a{2,3}b", "aaab")
<re.Match object; span=(0, 4), match='aaab'>
>>> re.search(r"^a{2,3}b", "aaaab")
>>> re.search(r"^a{2,3}b", "ab")
>>>
```

`+?`、`.*?`、`{m,n}?`、`??`

**贪婪匹配**：`*` `{m,n}` `+`这些字符，在满足条件的前提下尽可能的向后匹配

通过在这些字符后增加字符`?`来限制字符的贪婪匹配；如：`a+?`、`a.*?`、`a{1,2}?`，这样在满足条件的前提下，就会尽可能缩小匹配范围
```
>>> re.search(r"<.*>", "<html>abc</html>")
<re.Match object; span=(0, 16), match='<html>abc</html>'>  # 贪婪匹配
>>> re.search(r"<.*?>", "<html>abc</html>")
<re.Match object; span=(0, 6), match='<html>'>		# 非贪婪匹配
>>>


>>> re.search(r"a{1,2}", "aa")
<re.Match object; span=(0, 2), match='aa'>  # 贪婪匹配
>>> re.search(r"a{1,2}?", "aa")
<re.Match object; span=(0, 1), match='a'> # 非贪婪匹配
```

#### 字符集`[]`

字符集主要用来表示一个字符范围，匹配时仅取中一个字符匹配

- 表示字符选集，如：`[abc]`表示匹配`a` `b` `c`三个字符中得一个

- 表示字符范围`-` ，如：`[a-zA-z]`表示从`a`到`z`所有字符都可匹配

- 表示非字符范围`^`,  如：`[^abc]`表示所有所有非`a`字符都可成功匹配

- `.*?|`等特殊正则字符，在字符集中出现时仅代表字符本身，不表示正则语法规则

    ```
    >>> re.search(r"[^abc]", "d")  # 匹配所有非 a\b\c字符
    <re.Match object; span=(0, 1), match='d'>
    >>> re.search(r"[^abc]", "a")	# 如果字符仅是a则匹配返回None
    >>>
    ```

`|`  表示或者，在所有正则表达式语法中`|`的优先级最低，用来同时匹配多个正则表达式

```
>>> re.search(r"a|\d", "a")
<re.Match object; span=(0, 1), match='a'>
>>> re.search(r"a|\d", "b")
>>> re.search(r"a|\d", "2")
<re.Match object; span=(0, 1), match='2'>
```



#### 正则组合`(...)`: 取出匹配的字符串

匹配括号内的任意正则表达式，匹配成功后可以捕获`()`内匹配的字符

#### 命名组合

`(?P<name>...)` ：给*正则组合*命名，方便取出以及后续正则表达式调用

`(?P=name)`反向引用一个命名组合，匹配前面name组合相同的规则字符串。`\number`可以达到同样的效果

```
>>> pattern = re.compile(r"(?P<grade>\d+)", re.A)
>>> pattern.search("grade: 78")
<re.Match object; span=(7, 9), match='78'>
>>> _.group("grade")
'78'
>>>
```



#### 扩展语法

`(?aiLmsux)`：一些特殊作用的标记参数，标记应该在表达式字符串首位表示。每个标记字符都代表特殊的flag规则 如：`(?a)` ==> `re.ASCII`

`(?:...)`: 非捕获，匹配括号中的表达式时，不会捕获匹配内容，`group()`返回空值.

配合`|`操作符，形成表达式`(?:A|B)...other`,  可以指定B的边界，`other`是公共部分

```
>>> re.findall(r'ab|bc\d+', 'ab123ab345bc456')  # 匹配ab后跟数字或者bc后跟数字
['ab', 'ab', 'bc456']
>>> re.findall(r'(?:ab|bc)\d+', 'ab123ab345bc456')	# 匹配ab后跟数字或者bc后跟数字
['ab123', 'ab345', 'bc456']
>>>
```



`(?#...)`：在正则表达式中提供注释

`（?=...）`: 前视匹配，在原有匹配字符串你的基础上，向前多匹配字符

```
>>> re.search(r"abc(?=def)", "abcdef")
<re.Match object; span=(0, 3), match='abc'>  # 仅匹配后跟'def'的字符abc
>>> re.search(r"abc(?=def)", "abc")  # 单独的abc字符不满足匹配，因为后面没有跟def
>>>
```

`(?!...)`：前视取反

```
>>> re.search(r"abc(?!\d)", "abcd")  # 仅匹配不跟数字的，字符串abc
<re.Match object; span=(0, 3), match='abc'>
>>> re.search(r"abc(?!\d)", "abc1")	# abc1则不满匹配需求
>>>
```

`（?<=...）`: 后视匹配

`(?<!...)`：后视取反

`(?(id/name)yes-pattern|no-pattern)` : 条件选择



#### 转义字符`\`组成的特殊序列

`\b`： 字符边界，表示空字符串,但只在单词开始或结尾的位置,  通常 `\b` 定义为 `\w` 和 `\W` 字符之间，或者 `\w` 和字符串开始/结尾的边界， 意思就是 `r'\bfoo\b'` 匹配 `'foo'`, `'foo.'`, `'(foo)'`, `'bar foo baz'` 但不匹配 `'foobar'` 或者 `'foo3'`。

`\w`: 匹配`unicode`的大部分词语的字符，匹配`ascii`的数字、字母和下换线`[a-zA-Z-0-9_]`

`\s`：所有空白字符，如`[\t\n\r\f\v]`

`\d`: 数字

`\D`表示非数字，`\S`表示所有空字符，`\W`表示所有非常规字符

`\number`： 匹配数字代表的**正则组合**

**转义冲突：**

`python`并未提供单独的正则表达式语法表示，所以用**字符串**来代替对应的正则表达式。正则表达式和`python`字符串中均有关于转义的定义，所以涉及到转义字符需要格外小心处理。

比如匹配`'\section'`这个字符串，正则表达式需要这样定义`\\section`，python的字符串字面量对应的就是`\\\\section`。`python`的字符串字面量，首先经过解释器解析转义后得到`"\\section"`然后将这个字符串作为正则表达式传递给`c`引擎

更简便的方式是在python字面量前加`r`，表示原始字符无需转义，这样解释器解释字符串时，就不会对内容进行转义处理，`r\\section`就可以匹配`\section`了

## 正则对象

`pattern.search(string)`：从字符串中查找首个满足正则表达式条件的字符串，返回**match对象**

`pattern.match(string)`: 从字符串开头匹配，返回**match对象**或None

`pattern.findall(string)`: 匹配所有满足正则表达式的字符串，并返回匹配成功的**字符串**列表。如果正则表达式中包含多个*捕获组*合，则返回一个元组组成的列表，元组中为捕获的具体内容

```
>>> pattern = re.compile(r"(\d+)")	# 一个组合
>>> pattern.findall("12 drummers drumming, 11 pipers piping, 10 lords a-leaping")
['12', '11', '10']		 # 返回字符串列表
```

```
>>> pattern = re.compile(r"(\d+) (\b\w+\b)")  # 包含两个组合
>>> pattern.findall("12 drummers drumming, 11 pipers piping, 10 lords a-leaping")
[('12', 'drummers'), ('11', 'pipers'), ('10', 'lords')]  # 匹配返回元组组成的列表
```

`pattern.finditer(string)`：用法同`finadall`，不过这个方法返回的是**match对象的迭代器** (可以捕获匹配内容)

```
>>> iterator = pattern.finditer("12 drummers drumming, 11 pipers piping, 10 lords a-leaping")
>>> for match in iterator:
...     print(match.group())
...
12 drummers
11 pipers
10 lords
>>>
```

**需要注意的是**，`re`也提供模块级别的函数，即使无需创建`re.compile()`模式对象，也可直接调用以上几种方法。如：`re.search(pattern,string,flag)`,  不过使用中多加了一个正则样式参数

两种方式均可使用，一般情况下由于缓存机制（模块函数会将编译对象存储在缓存中，下次调用不会再次解析相同的正则模式），二者性能基本相同。但在循环使用预编译编译 `re.compile()`后使用效率更高，函数调用较少。

## match对象

`group()`：传入组合名`name`或索引`id`，取出正则表达式中对应组合的匹配字符串；

如果传入空参或0，则返回所有正则表达式匹配的字符串

如果一个组匹配成功多次，就只返回最后一个匹配

```
>>> m = re.match(r"(..)+", "a1b2c3")  # Matches 3 times.
>>> m.group(1)                        # Returns only the last match.
'c3'
```

`start()`：开始位置

`end()`：结束位置

`span()`：取值范围

## 文本处理

- `re.sub(pattern，replacement，string, count=0)`: 

    `pattern`: 匹配所有满足该`pattern`的字符串

    `replacement`：指定替换成后的字符串

    - `replacement`指定为字符串，则可以替换所有

    - `replacement`可以指定为一个可调用对象，用以区分不同`pattern`下替换为不同的字符串。该对象仅接受一个`matchobj`作为参数，并且要求必须返回字符串类型

        ```
        >>> import re
        >>> def diff_result(match_obj):
        ...     if match_obj.group(0) == '--':
        ...         return '2'
        ...     else:
        ...         return '1'
        
        >>> re.sub(r'-{1,2}', diff_result, 'one:- tow:--')
        'one:1 tow:2'
        ```

        

     `count`指定替换的次数，默认值为0替换所有

- `subn()` 方法完成相同的工作，但返回一个包含新字符串值和已执行的替换次数的 2 元组:

    ```
    >>> p = re.compile('(blue|white|red)')
    >>> p.subn('colour', 'blue socks and red shoes')
    ('colour socks and colour shoes', 2)
    >>> p.subn('colour', 'no colours at all')
    ('no colours at all', 0)
    ```

- `re.split(pattern，string, maxspilt=0`：以指定的正则样式，分割字符串；`maxsplit`指定分隔次数，到分割次数后剩余的字符串，全部放到最后一个元素中；

    `re.split(r'a', '')`分割空字符会返回一个`['']`数组，**注意**这个数组不是空的。

# 示例-词法分析器

```
# /bin/bash/python3
# -*- coding:utf8 -*-
# @Time: 2021/6/6 15:41
import re
from typing import NamedTuple


class Token(NamedTuple):
    type: str
    value: str
    line: int
    column: int


def tokenize(code: str):
    """解析token"""
    keywords = {'func', 'var', 'return', 'Int64'}
    token_specification = [
        ("NUMBER", r"\d+(\.\d*)?"),
        ("ID", r"[a-zA-z][a-zA-z0-9_]*"),
        ("SKIP", r"[ \t\r]+"),
        ("NEWLINE", r"\n"),
        ("ASSIGN", r"="),
        ("OP", r"==|[:(){}+-/*]"),
        ("MISMATCH", r".")
    ]  # 其中的先后顺序决定了匹配结果，如果多个正则表达式匹配同一个字符串，则顺序靠前的先匹配

    line_num = 1
    line_start = 0
    token_regex = "|".join(["(?P<%s>%s)" % pair for pair in token_specification])
    for mo in re.finditer(token_regex, code):
        kind = mo.lastgroup
        value = mo.group()
        column = mo.start() - line_start
        if kind == "NUMBER":
            value = float(value) if '.' in value else int(value)
        elif kind == "ID" and value in keywords:
            kind = value
        elif kind == "NEWLINE":
            line_start = mo.end()
            line_num += 1
        elif kind == "SKIP":
            continue
        elif kind == "MISMATCH":
            raise RuntimeError(f'{value!r} unexpected on line {line_num}')
        yield Token(kind, value, line_num, column)


code_fragment = """func run(){
        var a: Int64 = 3 + 1 - 2 / 10.2 
        var b: Int64 = 4
        a==b
        return a
    }
"""
if __name__ == "__main__":
    for tok in tokenize(code_fragment):
        print(tok)

```

输出结果如下：

```
Token(type='func', value='func', line=1, column=0)
Token(type='ID', value='run', line=1, column=5)
Token(type='OP', value='(', line=1, column=9)
Token(type='OP', value=')', line=1, column=10)
Token(type='OP', value='{', line=1, column=11)
Token(type='NEWLINE', value='\n', line=2, column=12)
Token(type='var', value='var', line=2, column=8)
Token(type='ID', value='a', line=2, column=12)
Token(type='OP', value=':', line=2, column=13)
Token(type='Int64', value='Int64', line=2, column=15)
Token(type='ASSIGN', value='=', line=2, column=21)
Token(type='NUMBER', value=3, line=2, column=23)
Token(type='OP', value='+', line=2, column=25)
Token(type='NUMBER', value=1, line=2, column=27)
Token(type='OP', value='-', line=2, column=29)
Token(type='NUMBER', value=2, line=2, column=31)
Token(type='OP', value='/', line=2, column=33)
Token(type='NUMBER', value=10.2, line=2, column=35)
Token(type='NEWLINE', value='\n', line=3, column=40)
Token(type='var', value='var', line=3, column=8)
Token(type='ID', value='b', line=3, column=12)
Token(type='OP', value=':', line=3, column=13)
Token(type='Int64', value='Int64', line=3, column=15)
Token(type='ASSIGN', value='=', line=3, column=21)
Token(type='NUMBER', value=4, line=3, column=23)
Token(type='NEWLINE', value='\n', line=4, column=24)
Token(type='ID', value='a', line=4, column=8)
Token(type='ASSIGN', value='=', line=4, column=9)
Token(type='ASSIGN', value='=', line=4, column=10)
Token(type='ID', value='b', line=4, column=11)
Token(type='NEWLINE', value='\n', line=5, column=12)
Token(type='return', value='return', line=5, column=8)
Token(type='ID', value='a', line=5, column=15)
Token(type='NEWLINE', value='\n', line=6, column=16)
Token(type='OP', value='}', line=6, column=4)
Token(type='NEWLINE', value='\n', line=7, column=5)
```



# 总结

​    这里仅总结常用的正则表达式使用方法，具体请移步[官方文档](https://docs.python.org/zh-cn/3/library/re.html),

1. 正则表达式仅作为**常规字符串处理**的扩充使用，用作复杂情况下的字符串处理。如果在`str`类型中有相应简单的方法替代，不建议使用正则表达式
2. 熟悉常用的正则语法规则，可以在常见的`IDE`中，搜索替换使用。还是很方便快捷的 ^_^

