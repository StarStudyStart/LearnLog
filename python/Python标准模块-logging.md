# lib/logging

日志记录模块主要用来服务于程序调试，`logging`模块能够记录软件运行过程中的关键节点信息（追踪程序运行过程中的信息）。

相比于`print`方法的优点：

1. `print`仅打印在控制台，`logger`可以输出到日志文件；`logging`模块更加方便的显示一些提示信息，如：当前时间、文件名、线程ID等

2. 程序调试结束后不用像`print`方法一样逐条删除，改变处理器的`level`等级即可限制相关内容输出到控制台

3. 同一进程下所有`logging`模块、文件都可参与到日志输出

日志记录的等级`level`:

| 等级    | 源码的等级值 | 状态 |
| ------- | ---- | ---- |
| DEBUG   | 10 | 程序调试信息 |
| INFO    | 20 | 程序正常运行的打印信息 |
| WARNING | 30 | 程序运行中的提示消息，但不影响程序正常运行 |
| ERROR |40|程序中的某些功能发生错误|
|CRITICAL|50|程序崩溃，直接退出|

如上图所示：

从上到下`level`的等级逐渐增大，每个`logger`对象都有级别设置，默认为`warning`，日志对象仅追踪该级别及以上的事件（等级越高，限制越大，程序控制台输出的信息越少）

如：`logger`对象的等级设置为`INFO`，则所有等级值高于20的记录消息，都可以正常通过记录器，唯独`debug`会被排除在外

日志记录器主要分为三个部分：

- 记录器：提供给应用程序记录的接口如：`logger.debug()`；发送`LogReocrd`到处理器

- 过滤器：比`setLevel`更复杂的规则，来过滤日志消息

- 处理器：决定将日志消息输出的平台，如：`StreanHandler`和`FileHandler`分别将日志消息输出到屏幕以及文件中

- 格式器：日志消息在最终呈现之前都要经过格式器处理，格式器决定了日志消息最终输出的样式

下图是：日志消息从创建`logger`对象到最终输出到指定平台的完整流程（来源：官方文档）

![clipboard.png](https://i.loli.net/2021/07/04/yNTprDa4sfPxWRn.png)

# 记录器

## 创建`logger`对象

### 记录器的层级

### 记录器的

## logger对象的方法

1. 配置

`setLevel()`

`setFilter()`

2. 消息记录

# 处理器

`setLevel()`

`setHandler()`

`setFormater()`

`setFilter()`

# 格式器

 `class LogReocrd`

