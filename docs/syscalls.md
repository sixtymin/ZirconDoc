<!--
# Zircon System Calls

## Handles
+ [handle_close](syscalls/handle_close.md) - close a handle
+ [handle_close_many](syscalls/handle_close_many.md) - close several handles
+ [handle_duplicate](syscalls/handle_duplicate.md) - create a duplicate handle (optionally with reduced rights)
+ [handle_replace](syscalls/handle_replace.md) - create a new handle (optionally with reduced rights) and destroy the old one

## Objects
+ [object_get_child](syscalls/object_get_child.md) - find the child of an object by its koid
+ [object_get_cookie](syscalls/object_get_cookie.md) - read an object cookie
+ [object_get_info](syscalls/object_get_info.md) - obtain information about an object
+ [object_get_property](syscalls/object_get_property.md) - read an object property
+ [object_set_cookie](syscalls/object_set_cookie.md) - write an object cookie
+ [object_set_property](syscalls/object_set_property.md) - modify an object property
+ [object_signal](syscalls/object_signal.md) - set or clear the user signals on an object
+ [object_signal_peer](syscalls/object_signal.md) - set or clear the user signals in the opposite end
+ [object_wait_many](syscalls/object_wait_many.md) - wait for signals on multiple objects
+ [object_wait_one](syscalls/object_wait_one.md) - wait for signals on one object
+ [object_wait_async](syscalls/object_wait_async.md) - asynchronous notifications on signal change
-->

# Zircon 系统调用

## 句柄
+ [handle_close](syscalls/handle_close.md) - 关闭一个句柄。
+ [handle_close_many](syscalls/handle_close_many.md) - 关闭多个句柄。
+ [handle_duplicate](syscalls/handle_duplicate.md) - 复制句柄（可减少新句柄权限）。
+ [handle_replace](syscalls/handle_replace.md) - 创建新的句柄（可减少新句柄权限）且关闭老句柄。

## 对象
+ [object_get_child](syscalls/object_get_child.md) - 通过 koid 查找对象的子对象。
+ [object_get_cookie](syscalls/object_get_cookie.md) - 读取对象的 cookie。
+ [object_get_info](syscalls/object_get_info.md) - 获取对象信息。
+ [object_get_property](syscalls/object_get_property.md) - 读取对象属性。
+ [object_set_cookie](syscalls/object_set_cookie.md) - 写入对象的 cookie。
+ [object_set_property](syscalls/object_set_property.md) - 修改对象的属性。
+ [object_signal](syscalls/object_signal.md) - 设置或清除对象上的用户信号。
+ [object_signal_peer](syscalls/object_signal.md) - 设置或清除对象另外一端上的用户信号。
+ [object_wait_many](syscalls/object_wait_many.md) - 等待多个对象上的信号出现。
+ [object_wait_one](syscalls/object_wait_one.md) - 等待一个对象上的信号。
+ [object_wait_async](syscalls/object_wait_async.md) - 信号变化的异步通知。

<!--
## Threads
+ [thread_create](syscalls/thread_create.md) - create a new thread within a process
+ [thread_exit](syscalls/thread_exit.md) - exit the current thread
+ [thread_read_state](syscalls/thread_read_state.md) - read register state from a thread
+ [thread_start](syscalls/thread_start.md) - cause a new thread to start executing
+ [thread_write_state](syscalls/thread_write_state.md) - modify register state of a thread

## Processes
+ [process_create](syscalls/process_create.md) - create a new process within a job
+ [process_read_memory](syscalls/process_read_memory.md) - read from a process's address space
+ [process_start](syscalls/process_start.md) - cause a new process to start executing
+ [process_write_memory](syscalls/process_write_memory.md) - write to a process's address space
+ [process_exit](syscalls/process_exit.md) - exit the current process

## Jobs
+ [job_create](syscalls/job_create.md) - create a new job within a job
+ [job_set_policy](syscalls/job_set_policy.md) - modify policies for a job and its descendants

## Tasks (Thread, Process, or Job)
+ [task_bind_exception_port](syscalls/task_bind_exception_port.md) - attach an exception port to a task
+ [task_kill](syscalls/task_kill.md) - cause a task to stop running
+ [task_resume_from_exception](syscalls/task_resume_from_exception.md) - resume a task from a previously caught exception
+ [task_suspend](syscalls/task_suspend.md) - cause a task to be suspended
-->

## 线程
+ [thread_create](syscalls/thread_create.md) - 在一个进程内创建一个新的线程。
+ [thread_exit](syscalls/thread_exit.md) - 退出当前线程。
+ [thread_read_state](syscalls/thread_read_state.md) - 读取线程的寄存器状态。
+ [thread_start](syscalls/thread_start.md) - 开始执行一个新的线程。
+ [thread_write_state](syscalls/thread_write_state.md) - 修改线程的寄存器状态。

## 进程
+ [process_create](syscalls/process_create.md) - 在任务中创建一个新的进程。
+ [process_read_memory](syscalls/process_read_memory.md) - 读取进程的地址空间。
+ [process_start](syscalls/process_start.md) - 启动新的进程开始运行。
+ [process_write_memory](syscalls/process_write_memory.md) - 写入进程的地址空间。
+ [process_exit](syscalls/process_exit.md) - 退出当前进程。

## 作业
+ [job_create](syscalls/job_create.md) - 在作业中创建一个新的任务。
+ [job_set_policy](syscalls/job_set_policy.md) - 修改作业及其子作业的策略。


## 任务（线程，进程或作业）
+ [task_bind_exception_port](syscalls/task_bind_exception_port.md) - 将异常端口挂到某个任务上。
+ [task_kill](syscalls/task_kill.md) - 暂停任务运行。
+ [task_resume_from_exception](syscalls/task_resume_from_exception.md) - 将任务从之前的异常中恢复。
+ [task_suspend](syscalls/task_suspend.md) - 挂起任务。

<!--
## Channels
+ [channel_call](syscalls/channel_call.md) - synchronously send a message and receive a reply
+ [channel_create](syscalls/channel_create.md) - create a new channel
+ [channel_read](syscalls/channel_read.md) - receive a message from a channel
+ [channel_read_etc](syscalls/channel_read.md) - receive a message from a channel with handle information
+ [channel_write](syscalls/channel_write.md) - write a message to a channel

## Sockets
+ [socket_accept](syscalls/socket_accept.md) - receive a socket via a socket
+ [socket_create](syscalls/socket_create.md) - create a new socket
+ [socket_read](syscalls/socket_read.md) - read data from a socket
+ [socket_share](syscalls/socket_share.md) - share a socket via a socket
+ [socket_shutdown](syscalls/socket_shutdown.md) - prevent reading or writing
+ [socket_write](syscalls/socket_write.md) - write data to a socket

## Fifos
+ [fifo_create](syscalls/fifo_create.md) - create a new fifo
+ [fifo_read](syscalls/fifo_read.md) - read data from a fifo
+ [fifo_write](syscalls/fifo_write.md) - write data to a fifo
-->

## 信道
+ [channel_call](syscalls/channel_call.md) - 同步发送消息，并接受回复。
+ [channel_create](syscalls/channel_create.md) - 创建一个新的信道。
+ [channel_read](syscalls/channel_read.md) - 从信道中接收消息。
+ [channel_read_etc](syscalls/channel_read.md) - 从信道中接收带有句柄信息的消息。
+ [channel_write](syscalls/channel_write.md) - 向信道中写入消息。

## 套接字
+ [socket_accept](syscalls/socket_accept.md) - 通过一个套接字接受套接字连接。
+ [socket_create](syscalls/socket_create.md) - 创建一个新的套接字。
+ [socket_read](syscalls/socket_read.md) - 从套接字中读取数据。
+ [socket_share](syscalls/socket_share.md) - 通过套接字共享一个套接字。
+ [socket_shutdown](syscalls/socket_shutdown.md) - 停止套接字的读写。
+ [socket_write](syscalls/socket_write.md) - 向套接字写入数据。

## Fifos
+ [fifo_create](syscalls/fifo_create.md) - 创建一个新的 fifo。
+ [fifo_read](syscalls/fifo_read.md) - 从 fifo 中读取数据。
+ [fifo_write](syscalls/fifo_write.md) - 向 fifo 中写入数据。

<!--
## Events and Event Pairs
+ [event_create](syscalls/event_create.md) - create an event
+ [eventpair_create](syscalls/eventpair_create.md) - create a connected pair of events

## Ports
+ [port_create](syscalls/port_create.md) - create a port
+ [port_queue](syscalls/port_queue.md) - send a packet to a port
+ [port_wait](syscalls/port_wait.md) - wait for packets to arrive on a port
+ [port_cancel](syscalls/port_cancel.md) - cancel notifications from async_wait

## Futexes
+ [futex_wait](syscalls/futex_wait.md) - wait on a futex
+ [futex_wake](syscalls/futex_wake.md) - wake waiters on a futex
+ [futex_requeue](syscalls/futex_requeue.md) - wake some waiters and requeue other waiters
-->

## 事件和事件对
+ [event_create](syscalls/event_create.md) - 创建一个事件
+ [eventpair_create](syscalls/eventpair_create.md) - 创建一个连接的事件对。

## Ports
+ [port_create](syscalls/port_create.md) - 创建一个端口。
+ [port_queue](syscalls/port_queue.md) - 从端口上发送数据包。
+ [port_wait](syscalls/port_wait.md) - 当代端口上的数据包到达。
+ [port_cancel](syscalls/port_cancel.md) - 取消 async_wait 的异步等待。

## Futexes
+ [futex_wait](syscalls/futex_wait.md) - 等待快速用户空间互斥体。
+ [futex_wake](syscalls/futex_wake.md) - 唤醒快速用户空间互斥体上的等待者。
+ [futex_requeue](syscalls/futex_requeue.md) - 唤醒互斥体上的等待着，并且将其他的等待着重新排队。

<!--
## Virtual Memory Objects (VMOs)
+ [vmo_create](syscalls/vmo_create.md) - create a new vmo
+ [vmo_read](syscalls/vmo_read.md) - read from a vmo
+ [vmo_write](syscalls/vmo_write.md) - write to a vmo
+ [vmo_clone](syscalls/vmo_clone.md) - clone a vmo
+ [vmo_get_size](syscalls/vmo_get_size.md) - obtain the size of a vmo
+ [vmo_set_size](syscalls/vmo_set_size.md) - adjust the size of a vmo
+ [vmo_op_range](syscalls/vmo_op_range.md) - perform an operation on a range of a vmo
+ [vmo_replace_as_executable](syscalls/vmo_replace_as_executable.md) - add execute rights to a vmo
+ [vmo_create_physical](syscalls/vmo_create_physical.md) - create a VM object referring to a specific contiguous range of physical memory
+ [vmo_clone](syscalls/vmo_clone.md) - clone a vmo
+ [vmo_set_cache_policy](syscalls/vmo_set_cache_policy.md) - set the caching policy for pages held by a VMO.

## Virtual Memory Address Regions (VMARs)
+ [vmar_allocate](syscalls/vmar_allocate.md) - create a new child VMAR
+ [vmar_map](syscalls/vmar_map.md) - map a VMO into a process
+ [vmar_unmap](syscalls/vmar_unmap.md) - unmap a memory region from a process
+ [vmar_protect](syscalls/vmar_protect.md) - adjust memory access permissions
+ [vmar_destroy](syscalls/vmar_destroy.md) - destroy a VMAR and all of its children
-->

## 虚拟内存对象（VMOs）

+ [vmo_create](syscalls/vmo_create.md) - 创建一个新的 vmo。
+ [vmo_read](syscalls/vmo_read.md) - 从 vmo 中读取数据。
+ [vmo_write](syscalls/vmo_write.md) - 向 vmo 中写入数据。
+ [vmo_clone](syscalls/vmo_clone.md) - 复制一个 vmo。
+ [vmo_get_size](syscalls/vmo_get_size.md) - 获取 vmo 的大小。
+ [vmo_set_size](syscalls/vmo_set_size.md) - 调整 vmo 的大小。
+ [vmo_op_range](syscalls/vmo_op_range.md) - 在一定范围的 vmo 上执行操作。
+ [vmo_replace_as_executable](syscalls/vmo_replace_as_executable.md) - 给一个 vmo 添加执行属性。
+ [vmo_create_physical](syscalls/vmo_create_physical.md) - 创建一个VM对象，用于指向一段特殊的连续物理地址内存。
+ [vmo_clone](syscalls/vmo_clone.md) - 克隆 vmo。
+ [vmo_set_cache_policy](syscalls/vmo_set_cache_policy.md) - 设置 VMO 中的页缓存策略。

## 虚拟内存地址块（VMARs）

+ [vmar_allocate](syscalls/vmar_allocate.md) - 创建一个新的子 VMAR。
+ [vmar_map](syscalls/vmar_map.md) - 将 VMO 映射进一个进程空间。
+ [vmar_unmap](syscalls/vmar_unmap.md) - 从进程中取消内存块的映射。
+ [vmar_protect](syscalls/vmar_protect.md) - 调整内存访问的权限。
+ [vmar_destroy](syscalls/vmar_destroy.md) - 销毁 VMAR 以及它所有的子区块。

<!--
## Cryptographically Secure RNG
+ [cprng_draw](syscalls/cprng_draw.md)
+ [cprng_add_entropy](syscalls/cprng_add_entropy.md)

## Time
+ [nanosleep](syscalls/nanosleep.md) - sleep for some number of nanoseconds
+ [clock_get](syscalls/clock_get.md) - read a system clock
+ [clock_get_monotonic](syscalls/clock_get_monotonic.md) - read the monotonic system clock
+ [ticks_get](syscalls/ticks_get.md) - read high-precision timer ticks
+ [ticks_per_second](syscalls/ticks_per_second.md) - read the number of high-precision timer ticks in a second
+ [deadline_after](syscalls/deadline_after.md) - Convert a time relative to now to an absolute deadline

## Timers
+ [timer_create](syscalls/timer_create.md) - create a timer object
+ [timer_set](syscalls/timer_set.md) - start a timer
+ [timer_cancel](syscalls/timer_cancel.md) - cancel a timer
-->

## 加密安全 RNG
+ [cprng_draw](syscalls/cprng_draw.md)
+ [cprng_add_entropy](syscalls/cprng_add_entropy.md)

## 时间
+ [nanosleep](syscalls/nanosleep.md) - 休眠几个纳秒。
+ [clock_get](syscalls/clock_get.md) - 读取系统时钟。
+ [clock_get_monotonic](syscalls/clock_get_monotonic.md) - 读取单调系统时钟。
+ [ticks_get](syscalls/ticks_get.md) - 读取高精度定时器的滴答值。
+ [ticks_per_second](syscalls/ticks_per_second.md) - 读取高精度定时器的每秒滴答值。
+ [deadline_after](syscalls/deadline_after.md) - 转换到现在的相对时间值为绝对截止时间。

## 定时器
+ [timer_create](syscalls/timer_create.md) - 创建定时器对象。
+ [timer_set](syscalls/timer_set.md) - 启动定时器。
+ [timer_cancel](syscalls/timer_cancel.md) - 取消定时器。


<!--
## Hypervisor guests
+ [guest_create](syscalls/guest_create.md) - create a hypervisor guest
+ [guest_set_trap](syscalls/guest_set_trap.md) - set a trap in a hypervisor guest

## Virtual CPUs
+ [vcpu_create](syscalls/vcpu_create.md) - create a virtual cpu
+ [vcpu_resume](syscalls/vcpu_resume.md) - resume execution of a virtual cpu
+ [vcpu_interrupt](syscalls/vcpu_interrupt.md) - raise an interrupt on a virtual cpu
+ [vcpu_read_state](syscalls/vcpu_read_state.md) - read state from a virtual cpu
+ [vcpu_write_state](syscalls/vcpu_write_state.md) - write state to a virtual cpu

## Global system information
+ [system_get_features](syscalls/system_get_features.md) - get hardware-specific features
+ [system_get_num_cpus](syscalls/system_get_num_cpus.md) - get number of CPUs
+ [system_get_physmem](syscalls/system_get_physmem.md) - get physical memory size
+ [system_get_version](syscalls/system_get_version.md) - get version string

## Logging
+ [debuglog_create](syscalls/debuglog_create.md) - create a kernel managed log reader or writer
+ [log_write](syscalls/log_write.md) - write log entry to log
+ [log_read](syscalls/log_read.md) - read log entries from log
-->

## 超级管理者访客
+ [guest_create](syscalls/guest_create.md) - 创建一个高级管理者访客。
+ [guest_set_trap](syscalls/guest_set_trap.md) - 在高级管理者访客设置一个陷阱。

## 虚拟 CPU
+ [vcpu_create](syscalls/vcpu_create.md) - 创建虚拟 CPU。
+ [vcpu_resume](syscalls/vcpu_resume.md) - 恢复虚拟 CPU 执行。
+ [vcpu_interrupt](syscalls/vcpu_interrupt.md) - 在虚拟 CPU 上引发中断。
+ [vcpu_read_state](syscalls/vcpu_read_state.md) - 读取虚拟 CPU 的状态。
+ [vcpu_write_state](syscalls/vcpu_write_state.md) - 写入虚拟 CPU 状态。

## 全局系统信息

+ [system_get_features](syscalls/system_get_features.md) - 获取特定硬件功能。
+ [system_get_num_cpus](syscalls/system_get_num_cpus.md) - 获取 CPU 数量。
+ [system_get_physmem](syscalls/system_get_physmem.md) - 获取物理内存大小。
+ [system_get_version](syscalls/system_get_version.md) - 获取版本字符串。

## 日志
+ [debuglog_create](syscalls/debuglog_create.md) - 创建一个内核管理的日志读者或写者。
+ [log_write](syscalls/log_write.md) - 将日志条目写入日志。
+ [log_read](syscalls/log_read.md) - 从日志中读取日志条目。

<!--
## Multi-function
+ [vmar_unmap_handle_close_thread_exit](syscalls/vmar_unmap_handle_close_thread_exit.md) - three-in-one
+ [futex_wake_handle_close_thread_exit](syscalls/futex_wake_handle_close_thread_exit.md) - three-in-one

## System
+ [system_mexec](syscalls/system_mexec.md) - Soft reboot the system with a new kernel and bootimage
+ [system_mexec_payload_get](syscalls/system_mexec_payload_get.md) - Return a ZBI containing ZBI entries necessary to boot this system

## DDK
+ [bti_create](syscalls/bti_create.md) - create a new bus transaction initiator
+ [bti_pin](syscalls/bti_pin.md) - pin pages and grant devices access to them
+ [bti_release_quarantine](syscalls/bti_release_quarantine.md) - releases all quarantined PMTs
+ [cache_flush](syscalls/cache_flush.md) - Flush CPU data and/or instruction caches
+ [interrupt_ack](syscalls/interrupt_ack.md) - Acknowledge an interrupt object
+ [interrupt_bind](syscalls/interrupt_bind.md) - Bind an interrupt object to a port
+ [interrupt_create](syscalls/interrupt_create.md) - Create a physical or virtual interrupt object
+ [interrupt_destroy](syscalls/interrupt_destroy.md) - Destroy an interrupt object
+ [interrupt_trigger](syscalls/interrupt_trigger.md) - Trigger a virtual interrupt object
+ [interrupt_wait](syscalls/interrupt_wait.md) - Wait on an interrupt object
+ [iommu_create](syscalls/iommu_create.md) - create a new IOMMU object in the kernel
+ [pmt_unpin](syscalls/pmt_unpin.md) - unpin pages and revoke device access to them
+ [resource_create](syscalls/resource_create.md) - create a resource object
+ [smc_call](syscalls/smc_call.md) - Make an SMC call from user space
-->

## 多个函数组合
+ [vmar_unmap_handle_close_thread_exit](syscalls/vmar_unmap_handle_close_thread_exit.md) - 三合一函数。
+ [futex_wake_handle_close_thread_exit](syscalls/futex_wake_handle_close_thread_exit.md) - 三合一函数。

## 系统
+ [system_mexec](syscalls/system_mexec.md) - 用新的内核和引导镜像重新引导系统。
+ [system_mexec_payload_get](syscalls/system_mexec_payload_get.md) - 返回ZBI，它包含了引导系统必须的ZBI条目。

## DDK

+ [bti_create](syscalls/bti_create.md) - 创建新的总线事务发起者。
+ [bti_pin](syscalls/bti_pin.md) - 将内存页订入物理内存页，并允许设备访问。
+ [bti_release_quarantine](syscalls/bti_release_quarantine.md) - 释放所有隔离的 PMT。
+ [cache_flush](syscalls/cache_flush.md) - 冲刷 CPU 的数据和指令缓存。
+ [interrupt_ack](syscalls/interrupt_ack.md) - 确认中断对象。
+ [interrupt_bind](syscalls/interrupt_bind.md) - 将中断对象绑定到端口。
+ [interrupt_create](syscalls/interrupt_create.md) - 创建物理或虚拟的中断对象。
+ [interrupt_destroy](syscalls/interrupt_destroy.md) - 销毁中断对象。
+ [interrupt_trigger](syscalls/interrupt_trigger.md) - 触发虚拟终端对象。
+ [interrupt_wait](syscalls/interrupt_wait.md) - 等待中断对象。
+ [iommu_create](syscalls/iommu_create.md) - 在内核中创建新的 IOMMU 对象。
+ [pmt_unpin](syscalls/pmt_unpin.md) - 释放物理内存页，禁止设备访问内存。
+ [resource_create](syscalls/resource_create.md) - 创建资源对象。
+ [smc_call](syscalls/smc_call.md) - 从用户空间发起一次 SMC 调用。
