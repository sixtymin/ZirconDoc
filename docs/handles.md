<!--
# Zircon Handles

[TOC]

## Basics
Handles are kernel constructs that allows user-mode programs to
reference a kernel object. A handle can be thought as a session
or connection to a particular kernel object.

It is often the case that multiple processes concurrently access
the same object via different handles. However, a single handle
can only be either bound to a single process or be bound to
kernel.

When it is bound to kernel we say it's 'in-transit'.
-->

# Zircon 句柄

[TOC]

## 基础

句柄是内核构件，它允许用于程序引用内核对象。句柄可以被当作特定内核对象的会话或链接。

通常的情况是多进程通过不同的句柄同时访问相同的对象。但是，一个句柄只能或者绑定到一个进程或绑定到内核。

当句柄绑定到内核时，我们称它为在转移途中。

<!--
In user-mode a handle is simply a specific number returned by
some syscall. Only handles that are not in-transit are visible
to user-mode.

The integer that represents a handle is only meaningful for that
process. The same number in another process might not map to any
handle or it might map to a handle pointing to a completely
different kernel object.

The integer value for a handle is any 32-bit number except
the value corresponding to ZX_HANDLE_INVALID.

For kernel-mode, a handle is a C++ object that contains three
logical fields:

+ A reference to a kernel object
+ The rights to the kernel object
+ The process it is bound to (or if it's bound to kernel)

The '[rights](rights.md)' specify what operations on the kernel object
are allowed. It is possible for a single process to have two different
handles to the same kernel object with different rights.
-->

在用户模式中，句柄是一些系统调用返回的简单的特定数字。只有不在转移途中的句柄对用户模式是可见的。

表示句柄的整数值对这个进程有意义。另外一个进程的相同数字可能不会映射到任何句柄，或它映射到指向另外一个完全不同的内核对象的句柄。

句柄的整数值是一个任意的32位数字，除了对应于 `ZX_HANDLE_INVALID` 的值。

内核模式中，句柄是一个 `C++` 对象，它博阿含了三个逻辑成员：

+ 引用内核对象。
+ 内核对象的权限。
+ 它绑定的进程（或者它绑定到内核）

'[权限](rights.md)'指定了在内核对象可以进行的操作。一个进程中可以有两个不同的句柄指向相同的对象，但是两个句柄具有不同的权限。

<!--
## Using Handles
There are many syscalls that create a new kernel object
and which return a handle to it. To name a few:
+ [`zx_event_create`](syscalls/event_create.md)
+ [`zx_process_create`](syscalls/process_create.md)
+ [`zx_thread_create`](syscalls/thread_create.md)

These calls create both the kernel object and the first
handle pointing to it. The handle is bound to the process that
issued the syscall and the rights are the default rights for
that type of kernel object.

There is only one syscall that can make a copy of a handle,
which points to the same kernel object and is bound to the same
process that issued the syscall:
+ [`zx_handle_duplicate`](syscalls/handle_duplicate.md)

There is one syscall that creates an equivalent handle (possibly
with fewer rights), invalidating the original handle:
+ [`zx_handle_replace`](syscalls/handle_replace.md)

There is one syscall that just destroys a handle:
+ [`zx_handle_close`](syscalls/handle_close.md)

There are two syscalls that takes a handle bound to calling
process and binds it into kernel (puts the handle in-transit):
+ [`zx_channel_write`](syscalls/channel_write.md)
+ [`zx_socket_share`](syscalls/socket_share.md)
-->

## 使用句柄

有许多系统调用创建新的内核对象，它们返回指向内核对象的句柄。例如：

+ [`zx_event_create`](syscalls/event_create.md)
+ [`zx_process_create`](syscalls/process_create.md)
+ [`zx_thread_create`](syscalls/thread_create.md)

这些系统调用创建内核对象以及第一个指向它们的句柄。句柄绑定到发起系统调用的进程中，权限则是这些类型内核对象默认的权限。

只有一个系统调用用于复制句柄，复制出的句柄指向相同的内核对象，并且绑定到发起系统调用的进程：

+ [`zx_handle_duplicate`](syscalls/handle_duplicate.md)

有一个系统调用创建等价句柄（可能有更少的权限），然后释放原始的句柄：

+ [`zx_handle_replace`](syscalls/handle_replace.md)

有一个系统调用只用于销毁句柄：

+ [`zx_handle_close`](syscalls/handle_close.md)

有两个系统调用用于将绑定到调用进程的句柄绑定到内核（将句柄放到转移途中状态）：

+ [`zx_channel_write`](syscalls/channel_write.md)
+ [`zx_socket_share`](syscalls/socket_share.md)

<!--
There are three syscalls that takes an in-transit handle and
binds it to the calling process:
+ [`zx_channel_read`](syscalls/channel_read.md)
+ [`zx_channel_call`](syscalls/channel_call.md)
+ [`zx_socket_accept`](syscalls/socket_accept.md)

The channel and socket syscalls above are used to transfer a handle from
one process to another. For example it is possible to connect
two processes with a channel. To transfer a handle the source process
calls `zx_channel_write` or `zx_channel_call` and then the destination
process calls `zx_channel_read` on the same channel.

Finally, there is a single syscall that gives a new process its
bootstrapping handle, that is, the handle that it can use to
request other handles:
+ [`zx_process_start`](syscalls/process_start.md)

The bootstrapping handle can be of any transferable kernel object but
the most reasonable case is that it points to one end of a channel
so this initial channel can be used to send further handles into the
new process.
-->

<!--
## Garbage Collection
If a handle is valid, the kernel object it points to is guaranteed
to be valid. This is ensured because kernel objects are ref-counted
and each handle holds a reference to its kernel object.

The opposite does not hold. When a handle is destroyed it does not
mean its object is destroyed. There could be other handles pointing
to the object or the kernel itself could be holding a reference to
the kernel object. An example of this is a handle to a thread; the
fact that the last handle to a thread is closed it does not mean that
the thread has been terminated.

When the last reference to a kernel object is released, the kernel
object is either destroyed or the kernel marks the object for
garbage collection; the object will be destroyed at a later time
when the current set of pending operations on it are completed.
-->

<!--
## Special Cases
+ When a handle is in-transit and the channel or socket it was written
to is destroyed, the handle is closed.
+ Debugging sessions (and debuggers) might have special syscalls to
get access to handles.

## Invalid Handles and handle reuse

It is an error to pass to any syscall except for `zx_object_get_info`
the following values:

+ A handle value that corresponds to a closed handle
+ The **ZX_HANDLE_INVALID** value, except for `zx_handle_close` syscall

The kernel is free to re-use the integer values of closed handles for
newly created objects. Therefore, it is important to make sure that proper
handle hygiene is observed:

+ Don't have one thread close a given handle and another thread use the
  same handle in a racy way. Even if the second thread is also closing it.
+ Don't ignore **ZX_ERR_BAD_HANDLE** return codes. They usually mean the
  code has a logic error.

Detecting invalid handle usage can be automated by using the
**ZX_POL_BAD_HANDLE** Job policy with **ZX_POL_ACTION_EXCEPTION** to
generate an exception when a process under such job object attempts any of
the of the mentioned invalid cases.

## See Also
[Objects](objects.md),
[Rights](rights.md)
-->

