<!--
# Zircon Kernel Concepts

## Introduction

The kernel manages a number of different types of Objects. Those which are
accessible directly via system calls are C++ classes which implement the
Dispatcher interface. These are implemented in
[kernel/object](../kernel/object). Many are self-contained higher-level Objects.
Some wrap lower-level [lk](../../docs/glossary.md#lk) primitives.
-->

# Zircon 内核概念

## 介绍

内核管理着大量的不同种类的对象。这些对象可以直接通过系统调用访问，它们都是实现了分发接口的`C++`类。它们的实现位于[kernel/object](../kernel/object)目录。其中很多是自包含的高层对象，一些封装了更底层的[lk](../../docs/glossary.md#lk)原语。

<!--
## [System Calls](syscalls.md)

Userspace code interacts with kernel objects via system calls, and almost
exclusively via [Handles](handles.md).  In userspace, a Handle is represented as
32bit integer (type zx_handle_t).  When syscalls are executed, the kernel checks
that Handle parameters refer to an actual handle that exists within the calling
process's handle table.  The kernel further checks that the Handle is of the
correct type (passing a Thread Handle to a syscall requiring an event handle
will result in an error), and that the Handle has the required Rights for the
requested operation.

System calls fall into three broad categories, from an access standpoint:

1. Calls which have no limitations, of which there are only a very few, for
example [*zx_clock_get()*](syscalls/clock_get.md)
and [*zx_nanosleep()*](syscalls/nanosleep.md) may be called by any thread.
2. Calls which take a Handle as the first parameter, denoting the Object they act upon,
which are the vast majority, for example [*zx_channel_write()*](syscalls/channel_write.md)
and [*zx_port_queue()*](syscalls/port_queue.md).
3. Calls which create new Objects but do not take a Handle, such as
[*zx_event_create()*](syscalls/event_create.md) and
[*zx_channel_create()*](syscalls/channel_create.md).  Access to these (and limitations
upon them) is controlled by the Job in which the calling Process is contained.

System calls are provided by libzircon.so, which is a "virtual" shared
library that the Zircon kernel provides to userspace, better known as the
[*virtual Dynamic Shared Object* or vDSO](vdso.md).
They are C ELF ABI functions of the form *zx_noun_verb()* or
*zx_noun_verb_direct-object()*.

The system calls are defined by [syscalls.abigen](../system/public/zircon/syscalls.abigen)
and processed by the [abigen](../system/host/abigen/) tool into include files and glue
code in libzircon and the kernel's libsyscalls.
-->

## [系统调用](syscalls.md)

用户空间代码通过系统调用与内核对象交互，几乎所有的对象交互都要通过[句柄](handles.md)。在用户空间，句柄用32位的证书表示（类型为`zx_handle_t`）。当系统调用执行时，内核检查它指向的存在于调用进程的句柄表中真正的句柄参数。内核进一步还需要检查句柄是正确的类型（将线程句柄传递给需要事件句柄的系统调用会导致错误），以及句柄具有请求操作的权限。

系统调用从访问的角度可以分为三大类：

1. 没有限制的调用，只有非常少的系统调用属于这类，例如 [*zx_clock_get()*](syscalls/clock_get.md)
和 [*zx_nanosleep()*](syscalls/nanosleep.md) 可以从任何线程调用。
2. 将句柄作为系统调用的第一个参数，用于表示它们要作用的对象，这种类型包含了大部分的系统调用，例如 [*zx_channel_write()*](syscalls/channel_write.md) 和 [*zx_port_queue()*](syscalls/port_queue.md)。
3. 创建新对象的系统调用，它们不使用句柄作为参数，例如 [*zx_event_create()*](syscalls/event_create.md) 和 [*zx_channel_create()*](syscalls/channel_create.md)。访问这些系统调用（以及这些系统调用的限制）是由包含调用进程的任务控制的。

系统调用是由 `libzircon.so` 模块提供的，它是 Zircon 内核提供的给用户空间的一个虚拟的共享库，更好的称呼是 [*虚拟动态共享对象* or vDSO](vdso.md)。它们是*zx_noun_verb()* 或 *zx_noun_verb_direct-object()* 形式的 C ELF ABI函数。

系统调用是由 [syscalls.abigen](../system/public/zircon/syscalls.abigen) 定义，用工具程序 [abigen](../system/host/abigen/) 处理成 libzircon 和 libsyscalls 中的文件和胶水代码。

<!--
## [Handles](handles.md) and [Rights](rights.md)

Objects may have multiple Handles (in one or more Processes) that refer to them.

For almost all Objects, when the last open Handle that refers to an Object is closed,
the Object is either destroyed, or put into a final state that may not be undone.

Handles may be moved from one Process to another by writing them into a Channel
(using [*zx_channel_write()*](syscalls/channel_write.md)), or by using
[*zx_process_start()*](syscalls/process_start.md) to pass a Handle as the argument
of the first thread in a new Process.

The actions which may be taken on a Handle or the Object it refers to are governed
by the Rights associated with that Handle.  Two Handles that refer to the same Object
may have different Rights.

The [*zx_handle_duplicate()*](syscalls/handle_duplicate.md) and
[*zx_handle_replace()*](syscalls/handle_replace.md) system calls may be used to
obtain additional Handles referring to the same Object as the Handle passed in,
optionally with reduced Rights.  The [*zx_handle_close()*](syscalls/handle_close.md)
system call closes a Handle, releasing the Object it refers to, if that Handle is
the last one for that Object. The [*zx_handle_close_many()*](syscalls/handle_close_many.md)
system call similarly closes an array of handles.
-->

## [句柄](handles.md) 和 [权限](rights.md)

内核对象有多个句柄（在一个或多个进程中）指向它们。

对于几乎所有的内核对象来说，当最后一个指向它的打开句柄关闭时，对象也就被销毁掉了，或者设置最终状态。

句柄可以从一个进程移动到另外一个进程中，需要使用 [*zx_channel_write()*](syscalls/channel_write.md)) 将它写到IPC通信的信道中，或者使用[*zx_process_start()*](syscalls/process_start.md) 系统调用将句柄作为新进程第一个线程的参数传入。

在一个句柄或句柄指向的对象上可以发生的操作是由句柄相关的权限控制的，指向同一个对象的两个句柄可能有不同的权限。

系统调用 [*zx_handle_duplicate()*](syscalls/handle_duplicate.md) 和 [*zx_handle_replace()*](syscalls/handle_replace.md) 当将句柄作为参数传递给它们时，可以用于获取指向同一个对象的另外一个句柄，有些情况下可以减少权限。  系统调用 [*zx_handle_close()*](syscalls/handle_close.md) 用于关闭句柄，如果它是最后一个指向对象的句柄，那系统会释放它指向的这个对象。系统调用 [*zx_handle_close_many()*](syscalls/handle_close_many.md) 作用类似，它可以关闭一组句柄。

<!--
## Kernel Object IDs

Every object in the kernel has a "kernel object id" or "koid" for short.
It is a 64 bit unsigned integer that can be used to identify the object
and is unique for the lifetime of the running system.
This means in particular that koids are never reused.

There are two special koid values:

*ZX_KOID_INVALID* Has the value zero and is used as a "null" sentinel.

*ZX_KOID_KERNEL* There is only one kernel, and it has its own koid.

Kernel generated koids only use 63 bits (which is plenty).
This leaves space for artificially allocated koids by having the most
significant bit set.
Artificial koids exist to support things like identifying artificial objects,
like virtual threads in tracing, for consumption by tools.
How artificial koids are allocated is left to each program,
this document does not impose any rules or conventions.
-->

## 内核对象 ID

内核中的每一个对象都有一个“内核对象 ID”，或简称“okid”。它是一个64位的无符号整数，可以用于标识对象，并且在系统运行期间唯一。这意味着 koids 不会进行重用。

有两种特殊的 koid 值：

*ZX_KOID_INVALID* 它的值为0，用于表示“null”语义。

*ZX_KOID_KERNEL* 只有一个内核，它有自己的 koid。

内核使用63位（这已经非常丰富了）用于生成 koid，这就保留了用于人工分配koid的空间，通过设置最高位来人工分配 koid。人工设置的 koid 的存在用于支持比如人造的对象，追踪中的虚拟线程等，它们是工具使用的标识。人工设置 koids 如何分配则留给了每个程序自己，这个文档没有添加任何规则和限制。

<!--
## Running Code: Jobs, Processes, and Threads.

Threads represent threads of execution (CPU registers, stack, etc) within an
address space which is owned by the Process in which they exist.  Processes are
owned by Jobs, which define various resource limitations.  Jobs are owned by
parent Jobs, all the way up to the Root Job which was created by the kernel at
boot and passed to [`userboot`, the first userspace Process to begin execution](userboot.md).

Without a Job Handle, it is not possible for a Thread within a Process to create another
Process or another Job.

[Program loading](program_loading.md) is provided by userspace facilities and
protocols above the kernel layer.

See: [process_create](syscalls/process_create.md),
[process_start](syscalls/process_start.md),
[thread_create](syscalls/thread_create.md),
and [thread_start](syscalls/thread_start.md).
-->

## 执行代码：任务，进程和线程

在包含线程的进程地址空间中，`Threads`代表了执行的线程（CPU 寄存器，栈等）。Jobs 包含 Processes，进程中定义了各种资源限制。任务是由父任务包含，所有的任务依赖关系最终追溯到在引导期间内核创建的根任务，它被传递给 [`userboot` 第一个开始执行的用户空间进程](userboot.md)。

没有任务句柄，进程中的线程就不可能创建另外一个进程或另外一个任务。

[程序加载](program_loading.md) 是由内核层之上的用户空间设施和协议提供。

参考：[进程创建](syscalls/process_create.md)，[进程启动](syscalls/process_start.md)，[线程创建](syscalls/thread_create.md)，和 [线程启动](syscalls/thread_start.md)。

<!--
## Message Passing: Sockets and Channels

Both Sockets and Channels are IPC Objects which are bi-directional and two-ended.
Creating a Socket or a Channel will return two Handles, one referring to each endpoint
of the Object.

Sockets are stream-oriented and data may be written into or read out of them in units
of one or more bytes.  Short writes (if the Socket's buffers are full) and short reads
(if more data is requested than in the buffers) are possible.

Channels are datagram-oriented and have a maximum message size given by **ZX_CHANNEL_MAX_MSG_BYTES**,
and may also have up to **ZX_CHANNEL_MAX_MSG_HANDLES** Handles attached to a message.
They do not support short reads or writes -- either a message fits or it does not.

When Handles are written into a Channel, they are removed from the sending Process.
When a message with Handles is read from a Channel, the Handles are added to the receiving
Process.  Between these two events, the Handles continue to exist (ensuring the Objects
they refer to continue to exist), unless the end of the Channel which they have been written
towards is closed -- at which point messages in flight to that endpoint are discarded and
any Handles they contained are closed.

See: [channel_create](syscalls/channel_create.md),
[channel_read](syscalls/channel_read.md),
[channel_write](syscalls/channel_write.md),
[channel_call](syscalls/channel_call.md),
[socket_create](syscalls/socket_create.md),
[socket_read](syscalls/socket_read.md),
and [socket_write](syscalls/socket_write.md).
-->

## 消息传递：套接字和信道

套接字和信道都是 IPC 对象，它们都是双向和双端的对象。创建一个套接字或信道会返回两个句柄，每一个句柄指向对象的一端。

套接字是面向流式的，数据写入和读出都是以一个或多个字节为单位。短写入（如果套接字的缓存已满）和短读取（如果缓存中数据小于请求的数据）也是可以的。

信道是面向数据报的，它**ZX_CHANNEL_MAX_MSG_BYTES**宏指定最大的消息大小，并且会只有最多**ZX_CHANNEL_MAX_MSG_HANDLES**个句柄挂接到消息。它们不支持短读或短写 —— 即消息大小满足或不满足。

当句柄被写入信道中时，它们就会从发送进程移除，当带有句柄的消息从信道读取时，句柄会被添加到接收进程中。在两个事件之间，句柄持续存在（确保它们指向的对象持续存在），除非信道的接收消息一端被关闭了 —— 这时信道中传递的消息就会被删除，包含在消息中的句柄也被关闭。

参考：[信道创建](syscalls/channel_create.md)，[信道读取](syscalls/channel_read.md)，[信道写入](syscalls/channel_write.md)，[信道调用](syscalls/channel_call.md)，[套接字创建](syscalls/socket_create.md)，[套接字读取](syscalls/socket_read.md)，和 [套接字写入](syscalls/socket_write.md)。

<!--
## Objects and Signals

Objects may have up to 32 signals (represented by the zx_signals_t type and the ZX_*_SIGNAL_*
defines) which represent a piece of information about their current state.  Channels and Sockets,
for example, may be READABLE or WRITABLE.  Processes or Threads may be TERMINATED.  And so on.

Threads may wait for signals to become active on one or more Objects.

See [signals](signals.md) for more information.
-->

## 对象和信号

对象可以最多有32个信号（信号由 `zx_signals_t` 类型表示，由 `ZX_*_SIGNAL_*` 形式定义），这代表了对象当前的状态信息。例如，信道和套接字可能是 `READABLE` 或 `WRITABLE` 状态。进程和线程可能是 `TERMINATED`状态，等等。

线程可能在等待一个或多个对象上的信号有效。

参考 [signals](signals.md) 获取更多信息。

<!--
## Waiting: Wait One, Wait Many, and Ports

A Thread may use [*zx_object_wait_one()*](syscalls/object_wait_one.md)
to wait for a signal to be active on a single handle or
[*zx_object_wait_many()*](syscalls/object_wait_many.md) to wait for
signals on multiple handles.  Both calls allow for a timeout after
which they'll return even if no signals are pending.

If a Thread is going to wait on a large set of handles, it is more efficient to use
a Port, which is an Object that other Objects may be bound to such that when signals
are asserted on them, the Port receives a packet containing information about the
pending Signals.

See: [port_create](syscalls/port_create.md),
[port_queue](syscalls/port_queue.md),
[port_wait](syscalls/port_wait.md),
and [port_cancel](syscalls/port_cancel.md).
-->

## 等待：等待一个信号，等待多个信号，以及端口

线程可以使用 [*zx_object_wait_one()*](syscalls/object_wait_one.md) 等待一个信号有效，或者调用 [*zx_object_wait_many()*](syscalls/object_wait_many.md) 等待多个句柄上的信号有效。两个调用都允许超时，超时后它们会返回，即使有信号处于未决状态。

如果线程想要在一个大的句柄集合上等待，那使用端口会更加有效。端口也是一个对象，其他的对象可以绑定到这个对象，当这些对象的信号出现时，端口会受到一个数据包，其中包含了关于未决信号的信息。

参考：[端口创建](syscalls/port_create.md)，[端口排队](syscalls/port_queue.md)，[端口等待](syscalls/port_wait.md)，和 [端口取消](syscalls/port_cancel.md)。

<!--
## Events, Event Pairs.

An Event is the simplest Object, having no other state than its collection of active Signals.

An Event Pair is one of a pair of Events that may signal each other.  A useful property of
Event Pairs is that when one side of a pair goes away (all Handles to it have been
closed), the PEER_CLOSED signal is asserted on the other side.

See: [event_create](syscalls/event_create.md),
and [eventpair_create](syscalls/eventpair_create.md).
-->

## 事件和事件对

事件是一个最简单的对象，除了它自己的活动信号集合信息之外就没有其他的状态了。

事件对是一对事件，它们可以相互通知对方。事件对一个有用的属性是当事件对的一方关闭（所有指向它的句柄都关闭了），`PEER_CLOSED` 信号就会通知给关联的一方。

参考：[event_create](syscalls/event_create.md) 和 [eventpair_create](syscalls/eventpair_create.md)。

<!--
## Shared Memory: Virtual Memory Objects (VMOs)

Virtual Memory Objects represent a set of physical pages of memory, or the *potential*
for pages (which will be created/filled lazily, on-demand).

They may be mapped into the address space of a Process with
[*zx_vmar_map()*](syscalls/vmar_map.md) and unmapped with
[*zx_vmar_unmap()*](syscalls/vmar_unmap.md).  Permissions of
mapped pages may be adjusted with [*zx_vmar_protect()*](syscalls/vmar_protect.md).

VMOs may also be read from and written to directly with
[*zx_vmo_read()*](syscalls/vmo_read.md) and [*zx_vmo_write()*](syscalls/vmo_write.md).
Thus the cost of mapping them into an address space may be avoided for one-shot operations
like "create a VMO, write a dataset into it, and hand it to another Process to use."
-->

## 共享内存：虚拟内存对象（VMOs）

虚拟内存对象代表了一组内存物理页面，或者一组预留页（它们会在稍后按需创建和填充）。

使用系统调用 [*zx_vmar_map()*](syscalls/vmar_map.md) 可以映射到进程的地址空间中，使用 [*zx_vmar_unmap()*](syscalls/vmar_unmap.md) 系统调用取消映射。映射页面的权限可以使用 [*zx_vmar_protect()*](syscalls/vmar_protect.md) 系统调用进行调整。

VMOs 也可以使用系统调用 [*zx_vmo_read()*](syscalls/vmo_read.md) 和 [*zx_vmo_write()*](syscalls/vmo_write.md)直接进行读取和写入。这在临时操作中可以避免将它们映射到地址空间的性能消耗，比如，创建一个VMO，在其中写入数据，然后将它交给另外一个进程使用。

<!--
## Address Space Management

Virtual Memory Address Regions (VMARs) provide an abstraction for managing a
process's address space.  At process creation time, a handle to the root VMAR
is given to the process creator.  That handle refers to a VMAR that spans the
entire address space.  This space can be carved up via the
[*zx_vmar_map()*](syscalls/vmar_map.md) and
[*zx_vmar_allocate()*](syscalls/vmar_allocate.md) interfaces.
[*zx_vmar_allocate()*](syscalls/vmar_allocate.md) can be used to generate new
VMARs (called subregions or children) which can be used to group together
parts of the address space.

See: [vmar_map](syscalls/vmar_map.md),
[vmar_allocate](syscalls/vmar_allocate.md),
[vmar_protect](syscalls/vmar_protect.md),
[vmar_unmap](syscalls/vmar_unmap.md),
and [vmar_destroy](syscalls/vmar_destroy.md),
-->

## 地址空间管理

虚拟内存地址区块（VMAR）提供管理进程地址空间的抽象。在进程创建时，指向根 VMAR的句柄会传给进程创建者。这个句柄指向一个 VMAR，这个 VMAR 跨越整个地址空间。这个空间可以使用系统调用[*zx_vmar_map()*](syscalls/vmar_map.md) 和 [*zx_vmar_allocate()*](syscalls/vmar_allocate.md) 接口进行划分。[*zx_vmar_allocate()*](syscalls/vmar_allocate.md) 系统调用用于生成新的 VMAR（被称为子区块或孩子区块），它们用于组成地址空间的各个部分。

参考：[vmar映射](syscalls/vmar_map.md)，[vmar分配](syscalls/vmar_allocate.md)，[vmar权限设置](syscalls/vmar_protect.md)，[vmar反映射](syscalls/vmar_unmap.md) 以及 [vmar销毁](syscalls/vmar_destroy.md)。

<!--
## Futexes

Futexes are kernel primitives used with userspace atomic operations to implement
efficient synchronization primitives -- for example, Mutexes which only need to make
a syscall in the contended case.  Usually they are only of interest to implementers of
standard libraries.  Zircon's libc and libc++ provide C11, C++, and pthread APIs for
mutexes, condition variables, etc, implemented in terms of Futexes.

See: [futex_wait](syscalls/futex_wait.md),
[futex_wake](syscalls/futex_wake.md),
and [futex_requeue](syscalls/futex_requeue.md).
-->

## 快速用户空间互斥（Futexes）

Futexes是内核原语，它用于用户空间的原子操作，实现有效的同步原语 —— 比如互斥量，在使用时只需要在竞争情况下做一个系统调用即可。通常，它们只对标准库实现有兴趣。Zircon 的 libc 和 `libc++` 提供了 C11，`C++` 以及线程 API，它们用于互斥量，条件变量等，这些都是用 Futexes 来实现。

参考: [futex等待](syscalls/futex_wait.md)，[futex唤醒](syscalls/futex_wake.md)，以及[futex请求](syscalls/futex_requeue.md)。