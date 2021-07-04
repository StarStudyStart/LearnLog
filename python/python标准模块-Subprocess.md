# subprocess- 子进程管理

> 主要用于开启新的线程，并且连接<u>它们</u>的输入、输出、管道，从而获取返回值。
>
> 常用于在python内部执行`cmd`命令行，对于测试框架、执行各种系统命令有较好的应用场景

## run函数

pass

## Popen 构造函数：执行子程序

```
class subprocess.Popen(args, bufsize=-1, executable=None, stdin=None, stdout=None, stderr=None, preexec_fn=None, close_fds=True, shell=False, cwd=None, env=None, universal_newlines=None, startupinfo=None, creationflags=0, restore_signals=True, start_new_session=False, pass_fds=(), *, encoding=None, errors=None, text=None)
```

常用入参说明：

---

>  *args* 被所有调用需要，应当为一个字符串，或者一个程序参数序列。提供一个参数序列通常更好，它可以更小心地使用参数中的转义字符以及引用（例如允许文件名中的空格）。**如果传递一个简单的字符串，则 *shell* 参数必须为 [`True`](https://docs.python.org/zh-cn/3/library/constants.html#True)** （见下文）或者**该字符串中将被运行的程序名必须用简单的命名而不指定任何参数**。

>  *stdin*， *stdout* 和 *stderr* 分别指定了执行的程序的标准输入、输出和标准错误文件句柄。合法的值有 [`PIPE`](https://docs.python.org/zh-cn/3/library/subprocess.html#subprocess.PIPE) 、 [`DEVNULL`](https://docs.python.org/zh-cn/3/library/subprocess.html#subprocess.DEVNULL) 、 一个现存的文件描述符（一个正整数）、一个现存的文件对象以及 `None`。 [`PIPE`](https://docs.python.org/zh-cn/3/library/subprocess.html#subprocess.PIPE) 表示应该新建一个对子进程的管道。 [`DEVNULL`](https://docs.python.org/zh-cn/3/library/subprocess.html#subprocess.DEVNULL) 表示使用特殊的文件 [`os.devnull`](https://docs.python.org/zh-cn/3/library/os.html#os.devnull)。当使用默认设置 `None` 时，将不会进行重定向，子进程的文件流将继承自父进程。另外， *stderr* 可能为 [`STDOUT`](https://docs.python.org/zh-cn/3/library/subprocess.html#subprocess.STDOUT)，表示来自于子进程的标准错误数据应该被 *stdout* 相同的句柄捕获。

> 如果 *encoding* 或 *errors* 被指定，或者 *text* （也名为 *universal_newlines*）为真，则文件对象 *stdin* 、 *stdout* 与 *stderr* 将会使用在此次调用中指定的 *encoding* 和 *errors* 以文本模式打开或者为默认的 [`io.TextIOWrapper`](https://docs.python.org/zh-cn/3/library/io.html#io.TextIOWrapper)。

> 当构造函数的 *newline* 参数为 `None` 时。对于 *stdin*， 输入的换行符 `'\n'` 将被转换为默认的换行符 [`os.linesep`](https://docs.python.org/zh-cn/3/library/os.html#os.linesep)。对于 *stdout* 和 *stderr*， 所有输出的换行符都被转换为 `'\n'`。更多信息，查看 [`io.TextIOWrapper`](https://docs.python.org/zh-cn/3/library/io.html#io.TextIOWrapper) 类的文档。

> 如果文本模式未被使用， *stdin*， *stdout* 和 *stderr* 将会以二进制流模式打开。没有编码与换行符转换发生。



---



#### args

------

**args** 是程序参数的序列、单独的字符串、path-like object的任意一个

args为序列：运行的程序就是args的第一项，**默认情况下更推荐此种方式传入**

args为字符串：则依赖具体平台则其解读依赖于具体平台(shell,等参数)

-----



#### shell=False

------

> 如果 *shell* 设为 `True`,则使用 shell 执行指定的指令。如果您主要使用 Python 增强的控制流（它比大多数系统 shell 提供的强大），并且仍然希望方便地使用其他 shell 功能，如 shell 管道、文件通配符、环境变量展开以及 `~` 展开到用户家目录，这将非常有用

shell =True时，指定`/bin/sh -c`运行`args`命令；**推荐将args作为字符串传入**；

此时如果传入args序列，args第一项指定了命令，另外的项目将作为传递给 shell （而非命令） 的参数对待，Poen等同于：

```
Popen(["/bin/sh", "-c", args[0],arg[1],)
```



shell=Fasle时，**系统默认解释器**运行args命令，第一个为相当于可执行程序路径，Poen等同于

```
Popen(["/usr/bin/git", "commit", "-m","xxx"])
```

**小结：**

shell参数近指定运行程序时的shell解释器，`shell=False`时，默认情况为系统的`$SHELL`变量；shell=True时指定shell为`/bin/sh`；

大部分情况下两者并无区别，但目前遇到过`sheel=False`时，得不到`SegmentFault（core dump）`的报错信息。

-----



#### buffsize=-1

------

> 将在 `open()` 函数创建了 stdin/stdout/stderr 管道文件对象时作为对应的参数供应

buffsize 创建了 stdin/stdout/stderr 管道文件对象时，用来设置是否启用缓冲。

- `0` 表示不使用缓冲区 （读取与写入是一个系统调用并且可以返回短内容）
- `1` 表示行缓冲（只有 `universal_newlines=True` 时才有用，例如，在文本模式中）
- 任何其他正值表示使用一个约为对应大小的缓冲区
- 负的 *bufsize* （默认）表示使用系统默认的 io.DEFAULT_BUFFER_SIZE。
---



#### close_fds=True

---

如果 *close_fds* 为真，所有文件描述符除了 `0`, `1`, `2` 之外都会在子进程执行前关闭。而当 *close_fds* 为假时，文件描述符遵守它们继承的标志，如 [文件描述符的继承](https://docs.python.org/zh-cn/3/library/os.html#fd-inheritance) 所述。



#### start_new_session=False

---

`start_new_session=True`，调用`linux`的系统方法`setid()`开启子进程执行程序时，脱离父进程组独立存在；并且**`Popen`的`pid`作为新会话的领头进程**（重要）



#### env =None

---

> 添加自定义环境变量，一般对应`os.environ()`方法

如果 `env` 不是 `None`, 它必须是一个字典, 为新的进程设置环境变量; 它用于替换继承的当前进程的环境的默认行为. 它将直接被传递给 [`Popen`](https://docs.python.org/zh-cn/3/library/subprocess.html#subprocess.Popen)

---



## `Popen`对象的主要函数

### `Popen.wait()`

等待子进程被终止，有阻塞风险；建议使用`Popen.communicate()`方法

### `Popen.communicate(*input=None*, *timeout=None*)`

> 常用于获取`Popen()`子进程的执行结果

与进程交互：将数据发送到 `stdin`。 从 `stdout` 和`stderr` 读取数据，直到抵达文件结尾。 等待进程终止并设置`returncode` 属性。 可选的 *input* 参数应为要发送到下级进程的数据，或者如果没有要发送到下级进程的数据则为 `None`。 如果流是以文本模式打开的，则 *input* 必须为字符串。 在其他情况下，它必须为字节串。

`communicate()` 返回一个 `(stdout_data, stderr_data)` 元组。如果文件以文本模式打开则为字符串；否则字节。

注意如果你想要向进程的 `stdin` 传输数据，你需要通过 `stdin=PIPE` 创建此 `Popen` 对象。类似的，要从结果元组获取任何非 `None` 值，你同样需要设置 `stdout=PIPE` 或者 `stderr=PIPE`。

如果进程在 *timeout* 秒后未终止，一个 `TimeoutExpired` 异常将被抛出。捕获此异常并重新等待将不会丢失任何输出。

如果超时到期，子进程不会被杀死，所以为了正确清理一个行为良好的应用程序应该杀死子进程并完成通讯。

```
proc = subprocess.Popen(...)
try:
    outs, errs = proc.communicate(timeout=15)
except TimeoutExpired:
    proc.kill()
    outs, errs = proc.communicate()
```

### `Popen.pid`

子进程的进程号。注意如果你设置了 *shell* 参数为 `True`，则这是生成的子 shell 的进程号。

### `Popen.returncode`

此进程的退出码，由 `poll()` 和 `wait()` 设置（以及直接由 `communicate()` 设置）。一个 `None` 值 表示此进程仍未结束。一个负值 `-N` 表示子进程被信号 `N` 中断 (仅 `POSIX`).

# 实际使用过程中的总结

## 1、`shell=True`配合`start_new_session=True`使用，效果更佳

在实际应用中`shell=True`调用`Popen.kill()` 或`terminate()`，仅能杀死shell进程，shell启动的`cmd`命令进程则无法杀死，成为永久运行的孤立进程

```python
cmd = "ping 127.0.0.1"
process = subprocess.Popen(cmd,shell=True)
process.kill()  # shell关闭，但ping 1127.0.0.1命令一直在持续运行
```

```
yabin@yabin-VirtualBox:~$ ps -aux |grep ping
yabin     2475  0.0  0.0   2376   612 pts/4    S+   21:59   0:00 /bin/sh -c ping 127.0.0.1
yabin     2476  0.0  0.0   2340   612 pts/4    S+   21:59   0:00 ping 127.0.0.1
yabin     2478  0.0  0.0   6876   804 pts/11   S+   21:59   0:00 grep --color=auto ping
yabin@yabin-VirtualBox:~$

# process.kiil()杀死后，可以看到/bin/sh被杀死, ping命令还在
yabin@yabin-VirtualBox:~$ ps -aux |grep ping
yabin     2476  0.0  0.0   2340   612 pts/4    S+   21:59   0:00 ping 127.0.0.1
yabin     2489  0.0  0.0   6876   804 pts/11   S+   22:03   0:00 grep --color=auto ping
yabin@yabin-VirtualBox:~$ 
```



`shell=True`配合`start_new_session=True`可以杀死整个`shell`的进程组，如下

```
Popen(cmd, shell=True, start_new_seesion=True)
os.killpg(Poend.pid, signals.SIGTERM)
```

---

```
yabin@yabin-VirtualBox:~$ ps -aux |grep ping
yabin     2521  0.0  0.0   2376   620 ?        Ss   22:06   0:00 /bin/sh -c ping 127.0.0.1
yabin     2522  0.0  0.0   2340   624 ?        S    22:06   0:00 ping 127.0.0.1
yabin     2524  0.0  0.0   6876   820 pts/11   S+   22:06   0:00 grep --color=auto ping
yabin@yabin-VirtualBox:~$ ps -aux |grep ping
yabin     2526  0.0  0.0   6876   840 pts/11   S+   22:06   0:00 grep --color=auto ping
yabin@yabin-VirtualBox:~$
```

## 2、windows下使用`Popen`示例

```
	process_command = subprocess.Popen(
        cmd,
        cwd=work_dir,
        stdout=subprocess.PIPE,
        stderr=subprocess.PIPE,
        start_new_session=True,
        universal_newlines=True,
        encoding='utf-8'
    )
    LOGGER.debug("Run command ==> {}".format(cmd))
    com_out = com_err = return_code = None
    try:
        com_out, com_err = process_command.communicate(timeout=timeout)
    except subprocess.CalledProcessError:
        pass
    except subprocess.TimeoutExpired:
        pass
    else:
        pass
    finally:
        process_command.kill()
        handle = ctypes.windll.kernel32.OpenProcess(1, False, process_command.pid)
        ctypes.windll.kernel32.TerminateProcess(handle, -1)
```



## `Popen`实战

1、`Popen`执行超时命令 `os.kill`

```

```



2、`Popen`执行单一命令 shell=True

```

```



3、常用的`Poen`执行命令方法

```

```

