# logging

日志记录模块主要用来服务于程序调试，`logging`模块能够记录软件运行过程中的关键节点信息（追踪程序运行过程中的信息）。

相比于`print`方法的优点：

1. `print`仅打印在控制台，`logger`可以输出到日志文件；`logging`模块更加方便的显示一些提示信息，如：当前时间、文件名、线程ID等
2. 程序调试结束后不用像`print`方法一样逐条删除，改变处理器的`level`等级即可限制相关内容输出到控制台
3. 同一进程下所有`logging`模块、文件都可参与到日志输出

## `Logger.level`

日志等级`level`定义:

| 等级    | 源码的等级值 | 状态 |
| ------- | ---- | ---- |
| DEBUG   | 10 | 程序调试信息 |
| INFO    | 20 | 程序正常运行的打印信息 |
| WARNING | 30 | 程序运行中的提示消息，但不影响程序正常运行 |
| ERROR |40|程序中的某些功能发生错误|
|CRITICAL|50|程序崩溃，直接退出|

如上图所示：

从上到下`level`的等级逐渐增大，每个`logger`对象都有级别设置，日志对象仅追踪该级别及以上的事件（等级越高，限制越大，程序控制台输出的信息越少）

如：`logger`对象的等级设置为`INFO`，则所有等级值高于20的记录消息，都可以正常通过记录器，唯独`debug`会被排除在外

日志记录器主要分为三个部分：

- 记录器：提供给应用程序记录的接口如：`logger.debug()`；发送到`Handler`处理器

- 过滤器：比`setLevel`更复杂的规则，来过滤日志消息—–实际应用中很少使用

- 处理器：决定将日志消息输出的平台，如：`StreanHandler`和`FileHandler`分别将日志消息输出到控制器以及文件中

- 格式器：日志消息在最终呈现之前都要经过格式器处理，格式器决定了日志消息最终输出的样式

下图是：日志消息从创建`logger`对象到最终输出到指定平台的完整流程(本地`I:\流程图\`查找)

![logger对象的调用流程](I:\流程图\logger对象的调用流程.jpg)



# 记录器-logger

`logger`对象是线程安全的。也就是说，不同的模块中多次调用`getLogger(name)`（入参`name`值相同的情况下）获取的`logger`对象是相同的。

## 创建`logger`对象

记录器的两种创建方式：

1. `logger = logging.getLogger(name)`创建层级记录器

2. `logger.basicConfig()`创建root根记录器，并且可以方便的定义`FileHandler`、以及`StreamHandler`

第二种方式可以**快速**创建一个root记录器，然后在python模块的各处使用`logging.log_level()`即可输出程序调试信息,。但**可配置性**较差

```
# /bin/bash/python3
# -*- coding:utf8 -*-
# @Time: 2021/12/25 21:41

import logging

# logging.basicConfig()如果不指定`filename\filemode`两个参数，默认输出到控制台；
# 反之则输出到filename指定的文件中
logging.basicConfig(
    # filename='./test.log',
    # filemode='a',
    level=logging.DEBUG,
    format="[%(levelname)s %(asctime)s] %(message)s",
    datefmt="%Y-%m-%d %H:%M:%S",
)

def method1():
    logging.info("run in xxx method1")

def method2():
    logging.info("run in xxx method2")


if __name__ == '__main__':
    method1()
    method2()
```

```
console output
[INFO 2021-12-25 21:45:11] run in xxx method1
[INFO 2021-12-25 21:45:11] run in xxx method2
```



### 记录器的层级

使用`getLogger(name)`创建记录器时，`name`以句点作为层级结构的分隔符;  

如: `getLogger('a.b')`获取的logger对象，是`getLogger('a.b.c')`的父记录器。

`getLogger('')`获取的root根记录器，所有的记录器最上级记录器

## logger对象的常用属性

- **propagate**: `bool`类型，默认为**True**,  所有记录器会自动将当前`logger`对象传递给父记录器。

> propagate=True时，如果父/子记录器同时添加了同一个handler处理器，该handler会执行两次。
>
> tips: 常用的技巧是，在父记录器/根记录器添加handler处理器，子记录器只负责调用消息接口，由父记录器统一决定将消息发送给哪个handler处理。避免重复调用，输出重复信息

- **handlers**: list类型，当前`logger`添加的所有`handler`对象列表
- **level**: 获取当前记录器设置的等级

## logger对象的方法

1. 配置

`setLevel()` : 设置记录器的等级，所有小于当前等级的log会被过滤掉; 

logger的默认级别为`NOTSET`（如果logger是根记录器，则处理所有消息。反之则委托给父级）; 

root根记录器的默认级别为`WARNING`

> **委托给父级：**当logger 的级别为`NOTSET`时, 记录器就会向上寻找父记录器，直到找到不为`NOTSET`的记录器, 将此记录器的级别作为当前记录器的默认级别。

`setFilter()`：设置`Filter`对象

2. 消息记录


`logger.debug()`、`logger.info()`、`logger.warn()`、`logger.error()`、`logger.debug()`

对应不同的日志等级调用

3. add Handler/Filter

`addHandler(handler)`: 给`logger`添加处理器对象，logger会将记录的message传递给handler处理

`removeHandler(handler)`: 移除handler

`addFilter(handler)`: 给`logger`添加过滤器对象

`removeFilter(handler)`: 移除Filter

# 处理器-Handler

## 创建handler

logging模块针对不同的程序出口，已经封装了不同的Handler类，使用时直接实例化即可

- `logging.FileHandler`: 将日志写入文件的handler对象
- `logging.StreamHanlder`:将日志输出到控制台的handler对象
- … : 其他handler对象，详见[Python官网](https://docs.python.org/zh-cn/3/library/logging.handlers.html#module-logging.handlers)

## handler的常用方法

- `setLevel()`: 默认为`NOTSET`，将处理所有日志消息
`logger.level`决定了将哪些日志对象发送给handler，`handler.level`决定了将哪些日志对象输出到应用程序
- `setFormater()`: 设置格式器对象
- `setFilter()`： 设置过滤器对象
- **`close()`**：关闭记录器，停止向程序外部输出

# 格式器-Formatter

利用`LogRecord`的属性，格式化日志消息

## 创建

```
logging.Formatter(fmt="%(name)s %(levelname)s %(asctime)s %(message)s", datefmt="%Y-%m-%d %H:%M:%S, uuu")
```

实例将使用整个消息的格式字符串以及消息的日期/时间部分的格式字符串进行初始化。

 *fmt* 默认为 `'%(message)s'`。

*datefmt*默认为`%Y-%m-%d %H:%M:%S,uuu`

*style* 形参可以是 '%', '{' 或 '$' 之一，它决定格式字符串将如何与数据进行合并; 默认为%

*defaults* 形参可以是一个包含在自定义字段中使用的默认值的字典。 例如: `logging.Formatter('%(ip)s %(message)s', defaults={"ip": None})`

*在 3.8 版更改:* 加入*validate* 参数。不正确或不匹配的样式和格式将引发 `ValueError` 错误。例如: `logging.Formatter('%(asctime)s - %(message)s', style='{')`。

##  `class LogReocrd`的属性

| 属性名称        | 格式                         | 描述                                                         |
| :-------------- | :--------------------------- | :----------------------------------------------------------- |
| args            | 此属性不需要用户进行格式化。 | 合并到 `msg` 以产生 `message` 的包含参数的元组，或是其中的值将被用于合并的字典（当只有一个参数且其类型为字典时）。 |
| asctime         | `%(asctime)s`                | 表示 [`LogRecord`](https://docs.python.org/zh-cn/3/library/logging.html#logging.LogRecord) 何时被创建的供人查看时间值。 默认形式为 '2003-07-08 16:49:45,896' （逗号之后的数字为时间的毫秒部分）。 |
| created         | `%(created)f`                | [`LogRecord`](https://docs.python.org/zh-cn/3/library/logging.html#logging.LogRecord) 被创建的时间（即 [`time.time()`](https://docs.python.org/zh-cn/3/library/time.html#time.time) 的返回值）。 |
| exc_info        | 此属性不需要用户进行格式化。 | 异常元组（例如 `sys.exc_info`）或者如未发生异常则为 `None`。 |
| 文件名          | `%(filename)s`               | `pathname` 的文件名部分。                                    |
| funcName        | `%(funcName)s`               | 函数名包括调用日志记录.                                      |
| levelname       | `%(levelname)s`              | 消息文本记录级别（`'DEBUG'`，`'INFO'`，`'WARNING'`，`'ERROR'`，`'CRITICAL'`）。 |
| levelno         | `%(levelno)s`                | 消息数字的记录级别 (`DEBUG`, `INFO`, `WARNING`, `ERROR`, `CRITICAL`). |
| lineno          | `%(lineno)d`                 | 发出日志记录调用所在的源行号（如果可用）。                   |
| message         | `%(message)s`                | 记入日志的消息，即 `msg % args` 的结果。 这是在发起调用 [`Formatter.format()`](https://docs.python.org/zh-cn/3/library/logging.html#logging.Formatter.format) 时设置的。 |
| module -- 模块  | `%(module)s`                 | 模块 (`filename` 的名称部分)。                               |
| msecs           | `%(msecs)d`                  | [`LogRecord`](https://docs.python.org/zh-cn/3/library/logging.html#logging.LogRecord) 被创建的时间的毫秒部分。 |
| msg             | 此属性不需要用户进行格式化。 | 在原始日志记录调用中传入的格式字符串。 与 `args` 合并以产生 `message`，或是一个任意对象 (参见 [使用任意对象作为消息](https://docs.python.org/zh-cn/3/howto/logging.html#arbitrary-object-messages))。 |
| 名称            | `%(name)s`                   | 用于记录调用的日志记录器名称。                               |
| pathname        | `%(pathname)s`               | 发出日志记录调用的源文件的完整路径名（如果可用）。           |
| process         | `%(process)d`                | 进程ID（如果可用）                                           |
| processName     | `%(processName)s`            | 进程名（如果可用）                                           |
| relativeCreated | `%(relativeCreated)d`        | 以毫秒数表示的 LogRecord 被创建的时间，即相对于 logging 模块被加载时间的差值。 |
| stack_info      | 此属性不需要用户进行格式化。 | 当前线程中从堆栈底部起向上直到包括日志记录调用并引发创建当前记录堆栈帧创建的堆栈帧信息（如果可用）。 |
| thread          | `%(thread)d`                 | 线程ID（如果可用）                                           |
| threadName      | `%(threadName)s`             | 线程名（如果可用）                                           |

# 过滤器

过滤器在实际应用中很少用到，这里不做介绍。想了解的自行python官网

# 使用示例

```
def init_logger():
    logger = logging.getLogger("test")
    logger.setLevel(logging.DEBUG)
    fmt = logging.Formatter("%(name)s %(levelname)s %(asctime)s %(message)s")
    console = logging.StreamHandler()
    console.setLevel(logging.INFO)
    console.setFormatter(fmt)
    
    file_handler = logging.FileHandler(log_path)
    file_handler.setLevel(logging.DEBUG)
    file_handler.setFormatter(fmt)
    
    logger.addHandler(file_handler)
    logger.addHandler(console)
    return logger
```



END!!



