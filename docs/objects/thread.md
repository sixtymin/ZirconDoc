<!--
# Thread

## NAME

thread - runnable / computation entity

## SYNOPSIS

TODO
-->

# 线程

## 名字

thread - 可执行实体或计算实体

## 概要

待添加

<!--
## DESCRIPTION

The thread object is the construct that represents a time-shared CPU execution
context. Thread objects live associated to a particular
[Process Object](process.md) which provides the memory and the handles to other
objects necessary for I/O and computation.

### Lifetime
Threads are created by calling `zx_thread_create()`, but only start executing
when either `zx_thread_start()` or `zx_process_start()` are called. Both syscalls
take as an argument the entrypoint of the initial routine to execute.

The thread passed to `zx_process_start()` should be the first thread to start execution
on a process.
-->

## 描述

线程对象是表示一个分时 CPU 执行上下文的构件。线程对象生存于特定的 [进程对象](process.md)，它提供了内存和指向其他用于`I/O`和计算对象的句柄。

### 生命周期

线程是通过调用 `zx_thread_create()` 函数创建，并且只能在调用了  `zx_thread_start()` 或 `zx_process_start()` 系统调用是才能开始执行。两个系统调用豆浆初始例程的入口作为执行参数。

传递给 `zx_process_start()` 函数的线程应该是进程开始执行时的第一个执行线程。

<!--
A thread terminates execution:
+ by calling `zx_thread_exit()`
+ by calling `zx_vmar_unmap_handle_close_thread_exit()`
+ by calling `zx_futex_wake_handle_close_thread_exit()`
+ when the parent process terminates
+ by calling `zx_task_kill()` with the thread's handle
+ after generating an exception for which there is no handler or the handler
decides to terminate the thread.

Returning from the entrypoint routine does not terminate execution. The last
action of the entrypoint should be to call `zx_thread_exit()` or one of the
above mentioned `_exit()` variants.

Closing the last handle to a thread does not terminate execution. In order to
forcefully kill a thread for which there is no available handle, use
`zx_object_get_child()` to obtain a handle to the thread. This method is strongly
discouraged. Killing a thread that is executing might leave the process in a
corrupt state.

Fuchsia native threads are always *detached*. That is, there is no *join()* operation
needed to do a clean termination. However, some runtimes above the kernel, such as
C11 or POSIX might require threads to be joined.
-->

线程终止执行的情况有如下几种：

+ 调用 `zx_thread_exit()` 函数。
+ 调用 `zx_vmar_unmap_handle_close_thread_exit()` 函数
+ 调用 `zx_futex_wake_handle_close_thread_exit()` 函数
+ 当进程终止时
+ 使用线程句柄作为调用 `zx_task_kill()` 函数参数。
+ 发生异常后没有处理函数处理异常或者处理函数决定终止线程

从入口点函数返回并不终止执行。入口点的最后一个动作应该是调用 `zx_thread_exit()` 函数，或上述的带有 `_exit()` 的一个函数。

关闭最后一个指向线程的句柄并不终止线程执行。为了强制杀死没有可用句柄的线程，可以使用 `zx_object_get_child()` 函数获取指向线程的句柄。这个方法强烈建议不要使用。杀死正在执行的线程会导致进程进入崩溃状态。

Fuchsia 的本地线程总是处于 *分离状态*，也就是说，不需要执行 `join()` 操作来清理结束线程。但是，一些内核之上的运行时，例如 C11 或 POSIX 可能要求线程执行 `join()`。

<!--
## SYSCALLS

+ [thread_create](../syscalls/thread_create.md) - create a new thread within a process
+ [thread_exit](../syscalls/thread_exit.md) - exit the current thread
+ [thread_read_state](../syscalls/thread_read_state.md) - read register state from a thread
+ [thread_start](../syscalls/thread_start.md) - cause a new thread to start executing
+ [thread_write_state](../syscalls/thread_write_state.md) - modify register state of a thread

<br>

+ [task_bind_exception_port](../syscalls/task_bind_exception_port.md) - attach an exception
port to a task
+ [task_kill](../syscalls/task_kill.md) - cause a task to stop running
-->

## 系统调用

+ [线程创建](../syscalls/thread_create.md) - 在一个进程中创建一个新的线程。
+ [线程退出](../syscalls/thread_exit.md) - 退出当前线程。
+ [读线程状态](../syscalls/thread_read_state.md) - 从线程中读取寄存器状态。
+ [启动线程](../syscalls/thread_start.md) - 启动新的线程开始执行。
+ [写线程状态](../syscalls/thread_write_state.md) - 修改线程状态中的寄存器。

<br>

+ [任务绑定异常端口](../syscalls/task_bind_exception_port.md) - 将异常端口绑定到任务上。
+ [杀死任务](../syscalls/task_kill.md) - 停止任务运行。
