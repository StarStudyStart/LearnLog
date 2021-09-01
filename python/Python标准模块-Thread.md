# Thread-线程

线程：操作系统的最小执行单元，简单来说就是内存中的一个代码片段（例如：可执行函数）。

每个进程中都至少有一个主线程，一个进程可以由多个线程构成

`GIL`全局解释器锁，`CPython`中为了保重并发的安全性以及易用型，增加了一个全局解释器锁，这使得一个进程中同时仅能有一个进程在执行，因此多线程不能充分的利用系统的多核多处理器资源。但在执行`I/O`、哈希算法、以及压缩等操作时，会释放掉GIL.因此多线程常用于执行`I/O`密集的操作

# threading模块方法

`threading.current_thread()`获取调用处的线程对象

`threading.enumerate()` 获取当前存活的线程对象

# Thread对象

`Thread(target, args, kwargs, daemon=False)`

`start()`:启动线程，在一个独立控制的进程中调用`run`方法

`joint()`:等待线程执行结束，会阻塞调用该方法的进程，直到所有当前线程结束

`run()`： 代表线程活动的方法，调用target对象

`name`：线程名称

`daemon`:是否为守护线程



常用示例：

```
import threading

def multi_io_task(flag):
	for expr:
		single_io_task()
		
th = threading.Thread(target=multi_io_task, args=(flag,))
th.start()
th.join()
```





## Lock 锁对象

线程之间共享资源，因此多线程之间很容易修改同一个资源变量时很容易产生资源变量。线程锁在可以很好的解决这个问题。

`acquire`

`reslease()`



# Condition 条件对象

线程间的同步机制，多个线程之间可以共享状态。

锁本身就是条件对象的本身，不用单独管理

`wait()`: 释放锁，并阻塞当前线程被`notify()`重新唤醒，然后重新获取当前线程的锁。

`wait_for(predicate )`:释放锁，等待直到`predicate `为True时，重新获取锁

`notify()`：不会释放锁，唤醒一个被阻塞的线程。被唤醒的线程并不会立即执行，而是等到调用`notify()`方法的线程释放锁后，才会继续执行。

`notify_all()`:唤醒所有等待的线程，`notify()`方法只能唤醒一个线程

`acquire`：同Lock对象

`reslease()`：同Lock对象

无线缓冲区的生产者-消费者模型

```
# consumer
with cv: 
	while not item_is_available():
		cv.wait()
		
with cv:
    make_item_available()
    cv.notify()
```

```
```



