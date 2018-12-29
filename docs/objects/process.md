<!--
# Process

## NAME

process - Process abstraction

## SYNOPSIS

A zircon process is an instance of a program in the traditional
sense: a set of instructions which will be executed by one or more
threads, along with a collection of resources.
-->

# 进程

## 名字

process - 进程的抽象

## 概要

Zircon 进程是一个传统意义上的程序的实例：一个或多个线程执行的一组指令，以及一系列资源。

<!--
## DESCRIPTION

The process object is a container of the following resources:

+ [Handles](../handles.md)
+ [Virtual Memory Address Regions](vm_address_region.md)
+ [Threads](thread.md)

In general, it is associated with code which it is executing until it is
forcefully terminated or the program exits.

Processes are owned by [jobs](job.md) and allow an application that is
composed by more than one process to be treated as a single entity, from the
perspective of resource and permission limits, as well as lifetime control.
-->

## 描述

进程对象是如下资源的容器：

+ [句柄](../handles.md)
+ [虚拟内存地址区块（VMAR）](vm_address_region.md)
+ [线程](thread.md)

通常，它与一组代码相关，这组代码一直执行，直到进程被强制终止或程序退出。

[任务](job.md) 包含进程，允许一个应用程序可以有多个进程组成，但从资源和权限限制的角度以及生命周期控制，它们可以被当作单一整体。

<!--
### Lifetime
A process is created via `zx_process_create()` and its execution begins with
`zx_process_start()`.

The process stops execution when:
+ the last thread is terminated or exits
+ the process calls  ``zx_process_exit()``
+ the parent job terminates the process
+ the parent job is destroyed

The call to `zx_process_start()` cannot be issued twice. New threads cannot
be added to a process that was started and then its last thread has exited.
-->

### 生命周期

进程是通过 `zx_process_create()` 系统调用创建，并且使用 `zx_process_start()` 系统调用开始执行。

在如下的情况下进程停止执行：

+ 最后一个线程终止或退出。
+ 进程调用了 `zx_process_exit()` 函数。
+ 父任务终止了进程。
+ 父任务被销毁。

系统调用 `zx_process_start()` 在一个进程中不能调用两次。新的线程不能被添加到已经开始执行或进程最后一个线程退出的进程中。

<!--
## SYSCALLS

+ [process_create](../syscalls/process_create.md) - create a new process within a job
+ [process_read_memory](../syscalls/process_read_memory.md) - read from a process's address space
+ [process_start](../syscalls/process_start.md) - cause a new process to start executing
+ [process_write_memory](../syscalls/process_write_memory.md) - write to a process's address space
+ [process_exit](../syscalls/process_exit.md) - exit the current process

<br>

+ [job_create](../syscalls/job_create.md) - create a new job within a parent job

<br>

+ [vmar_map](../syscalls/vmar_map.md) - Map memory into an address space range
+ [vmar_protect](../syscalls/vmar_protect.md) - Change permissions on an address space range
+ [vmar_unmap](../syscalls/vmar_unmap.md) - Unmap memory from an address space range
-->

## 系统调用

+ [进程创建](../syscalls/process_create.md) - 在一个任务中创建一个新的进程。
+ [进程读内存](../syscalls/process_read_memory.md) - 从一个进程的地址空间读取数据。
+ [进程启动](../syscalls/process_start.md) - 启动新进程。
+ [进程写内容](../syscalls/process_write_memory.md) - 向一个进程的地址空间写数据。
+ [进程退出](../syscalls/process_exit.md) - 将当前进程退出。

<br>

+ [任务创建](../syscalls/job_create.md) - 再一个父任务中创建一个新的任务。

<br>

+ [vmar映射](../syscalls/vmar_map.md) - 映射某个地址空间范围内存。
+ [vmar保护](../syscalls/vmar_protect.md) - 修改一块地址空间的保护属性。
+ [vmar反映射](../syscalls/vmar_unmap.md) - 从地址空间中释放内存映射。