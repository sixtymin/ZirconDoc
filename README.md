<!--
# Zircon

Zircon is the core platform that powers the Fuchsia OS.  Zircon is
composed of a microkernel (source in kernel/...) as well as a small
set of userspace services, drivers, and libraries (source in system/...)
necessary for the system to boot, talk to hardware, load userspace
processes and run them, etc.  Fuchsia builds a much larger OS on top
of this foundation.
-->

# Zircon

Zircon 是支撑 Fuchsia 操作系统的核心平台。Zircon 是由多个部分组成，包括微内核（源码位于`kernel/..`目录下）以及用户空间服务，驱动和库（源码位于`system\..`目录）等小的程序集合，这些小集合用于系统引导，驱动硬件，加载用户空间进程并且执行它们所必须。在这些基础之上，Fuchsia 构建了一个更大的操作系统。

<!--
The canonical Zircon Git repository is located
at: https://fuchsia.googlesource.com/zircon

A read-only mirror of the code is present
at: https://github.com/fuchsia-mirror/zircon

The Zircon Kernel provides syscalls to manage processes, threads,
virtual memory, inter-process communication, waiting on object state
changes, and locking (via futexes).

Currently there are some temporary syscalls that have been used for early
bringup work, which will be going away in the future as the long term
syscall API/ABI surface is finalized.  The expectation is that there will
be about 100 syscalls.
-->

官方的 Zircon 的 Git 仓库位于地址：`https://fuchsia.googlesource.com/zircon`。代码的只读仓库可以在 github 上找到：`https://github.com/fuchsia-mirror/zircon`。

Zircon 内核提供了管理进程，线程，虚拟地址，进程间通信，等待对象状态变化，锁（通过 futexes）等系统调用。

当前有一些临时的系统调用用于早期的构建工作，这些系统调用在长期的系统调用`API/ABI`最终完成时会被删除掉。预期会有大约 100 个系统调用。

<!--
Zircon syscalls are generally non-blocking.  The wait_one, wait_many
port_wait and thread sleep being the notable exceptions.

This page is a non-comprehensive index of the zircon documentation.

+ [Getting Started](docs/getting_started.md)
+ [Contributing Patches](docs/contributing.md)
+ [Concepts Overview](docs/concepts.md)
+ [Kernel Objects](docs/objects.md)
+ [Kernel Scheduling](docs/kernel_scheduling.md)
+ [Process Objects](docs/objects/process.md)
+ [Thread Objects](docs/objects/thread.md)
+ [Handles](docs/handles.md)
+ [System Calls](docs/syscalls.md)
+ [Driver Development Kit](docs/ddk/overview.md)
+ [Testing](docs/testing.md)
+ [Hacking notes](docs/hacking.md)
+ [Memory usage analysis tools](docs/memory.md)
+ [Relationship with LK](docs/zx_and_lk.md)
+ [Micro-benchmarks](docs/benchmarks/microbenchmarks.md)
-->

Zircon 系统调用通常都是非阻塞的。`wait_one`，`wait_many`，`port_wait`和线程的`sleep`等函数则是例外。

如下的列表并非 Zircon 文档的完整索引。

+ [入门指南](docs/getting_started.md)
+ [贡献补丁](docs/contributing.md)
+ [概念概述](docs/concepts.md)
+ [内核对象](docs/objects.md)
+ [内核调度](docs/kernel_scheduling.md)
+ [进程对象](docs/objects/process.md)
+ [线程对象](docs/objects/thread.md)
+ [句柄](docs/handles.md)
+ [系统调用](docs/syscalls.md)
+ [驱动程序开发包（DDK）](docs/ddk/overview.md)
+ [测试](docs/testing.md)
+ [小工具说明](docs/hacking.md)
+ [内存使用情况分析工具](docs/memory.md)
+ [与LK的关系](docs/zx_and_lk.md)
+ [微型基准测试](docs/benchmarks/microbenchmarks.md)