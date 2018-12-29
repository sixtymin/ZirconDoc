<!--
# Zircon Kernel objects

[TOC]

Zircon is a object-based kernel. User mode code almost exclusively interacts
with OS resources via object handles. A handle can be thought of as an active
session with a specific OS subsystem scoped to a particular resource.

Zircon actively manages the following resources:

+ processor time
+ memory and address spaces
+ device-io memory
+ interrupts
+ signaling and waiting
-->

# Zircon 内核对象

[TOC]

Zircon是一个基于对象的内核。用户模式代码几乎所有与操作系统的交互都要通过对象句柄。句柄可以被当作一个活动会话，它包含了特定OS子系统内某个特定资源。

Zircon 管理如下的资源：

+ 处理器时间
+ 内存和地址空间
+ 设备I/O内存
+ 中断
+ 信号和等待事件

<!--
## Kernel objects for applications

### IPC
+ [Channel](objects/channel.md)
+ [Socket](objects/socket.md)
+ [FIFO](objects/fifo.md)

### Tasks
+ [Process](objects/process.md)
+ [Thread](objects/thread.md)
+ [Job](objects/job.md)
+ [Task](objects/task.md)

### Signaling
+ [Event](objects/event.md)
+ [Event Pair](objects/eventpair.md)
+ [Futex](objects/futex.md)

### Memory and address space
+ [Virtual Memory Object](objects/vm_object.md)
+ [Virtual Memory Address Region](objects/vm_address_region.md)
+ [bus_transaction_initiator](objects/bus_transaction_initiator.md)

### Waiting
+ [Port](objects/port.md)
-->

## 应用程序使用的内核对象

### IPC
+ [Channel](objects/channel.md)
+ [Socket](objects/socket.md)
+ [FIFO](objects/fifo.md)

### 任务
+ [Process](objects/process.md)
+ [Thread](objects/thread.md)
+ [Job](objects/job.md)
+ [Task](objects/task.md)

### 信号
+ [Event](objects/event.md)
+ [Event Pair](objects/eventpair.md)
+ [Futex](objects/futex.md)

### 内存和地址空间
+ [Virtual Memory Object](objects/vm_object.md)
+ [Virtual Memory Address Region](objects/vm_address_region.md)
+ [bus_transaction_initiator](objects/bus_transaction_initiator.md)

### 等待
+ [Port](objects/port.md)

<!--
## Kernel objects for drivers

+ [Interrupts](objects/interrupts.md)
+ [Resource](objects/resource.md)
+ [Log](objects/log.md)

## Kernel Object and LK
Some kernel objects wrap one or more LK-level constructs. For example the
Thread object wraps one `thread_t`. However the Channel does not wrap
any LK-level objects.

## Kernel object lifetime
Kernel objects are ref-counted. Most kernel objects are born during a syscall
and are held alive at refcount = 1 by the handle which binds the handle value
given as the output of the syscall. The handle object is held alive as long it
is attached to a handle table. Handles are detached from the handle table
closing them (for example via `sys_close()`) which decrements the refcount of
the kernel object. Usually, when the last handle is closed the kernel object
refcount will reach 0 which causes the destructor to be run.

The refcount increases both when new handles (referring to the object) are
created and when a direct pointer reference (by some kernel code) is acquired;
therefore a kernel object lifetime might be longer than the lifetime of the
process that created it.
-->

## 驱动使用的内核对象

+ [中断](objects/interrupts.md)
+ [资源](objects/resource.md)
+ [日志](objects/log.md)

## 内核对象和LK

一些内核对象是封装了一个或多个 LK层的构建。例如线程对象就是封装了`thread_t`。但是信道（Channel）就没有封装任何LK层的对象

## 内核对象生命周期

内核对象的生命周期是引用计数控制的。大部分内核对象在系统调用时生成，在引用计数为1时保持活着，它会被绑定到作为系统调用返回值的句柄上。只要对象挂到句柄表上，那么句柄对象就是持续处于活动状态。当关闭句柄（例如通过系统调用`sys_close`）时，句柄会从句柄表上删除，同时减少内核对象的引用计数。通常，当指向对象的最后一个句柄关闭时，内核对象的引用计数会变为0，这导致内核对象的析构函数执行。

当新句柄（指向对象）创建和当接受直接指针引用（通过一些内核代码）时，引用计数都会增加；因此，内核对象生命周期可能比创建它的进程的生命周期更长。

<!--
## Dispatchers
A kernel object is implemented as a C++ class that derives from `Dispatcher`
and that overrides the methods it implements. Thus, for example, the code
of the Thread object is found in `ThreadDispatcher`. There is plenty of
code that only cares about kernel objects in the generic sense, in that case
the name you'll see is `fbl::RefPtr<Dispatcher>`.

## Kernel Object security
In principle, kernel objects do not have an intrinsic notion of security and
do not do authorization checks; security rights are held by each handle. A
single process can have two different handles to the same object with
different rights.

## See Also
[Handles](handles.md)
-->

## 分发器

内核对象是作为 `C++` 类实现的，它们都派生自 `Dispatcher`，并且将它实现的方法进行了覆盖。因此，线程对象的代码可以在`ThreadDispatcher`中找到。有大量的代码只关心内核对象的通用外观，这种情况下看到的名字就是类似`fbl::RefPtr<Dispatcher>`形式。

## 内核对象

从原理上讲，内核对象没有内在的安全概念，并且也不做授权校验；每一个句柄保持有安全权限。一个进程可以有两个不同的句柄指向同一个对象，但是两个句柄可以有不同的权限。

## 参考内容
[Handles](handles.md)