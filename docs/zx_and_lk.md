<!--
# Zircon and LK

Zircon was born as a branch of [LK](https://github.com/littlekernel/lk) and even
now many inner constructs are based on LK while the layers above are new. For
example, Zircon has the concept of a process but LK does not. However, a Zircon
process is made of LK-level constructs such as LK's ``thread_t``.

LK is a Kernel designed for small systems typically used in embedded
applications. It is a good alternative to commercial offerings like
[FreeRTOS](http://www.freertos.org/) or [ThreadX](http://rtos.com/products/threadx/).
Such systems often have a very limited amount of ram, a fixed set of peripherals
and a bounded set of tasks.

On the other hand, Zircon targets modern phones and modern personal computers
with fast processors, non-trivial amounts of ram with arbitrary peripherals
doing open ended computation.

More specifically, some the visible differences are:

+ LK can run in 32-bit systems. Zircon is 64-bit only.
+ Zircon has first class user-mode support. LK does not.
+ Zircon has a capability-based security model. In LK all code is trusted.

Over time, even the low level constructs have changed to accommodate the new
requirements and to better fit the rest of the system.
-->

# Zircon 和 LK 关系

Zircon是作为 [LK](https://github.com/littlekernel/lk) 的一个分支诞生，即使到现在很多内部构件依然是基于 LK 的，但是上层都是新的内容。例如，Zircon 有进程概念，但是LK没有。不过，Zircon 的进程是由 LK 层的构件，比如 LK 的 `thread_t` 来构成的。

LK 是为用于嵌入式应用的小型系统设计的内核。它相对于一些商业的项目，比如 [FreeRTOS](http://www.freertos.org/) 或 [ThreadX](http://rtos.com/products/threadx/)，也是一个选择。这些系统通常有非常有限的内存，固定的外围设备和固定的一组任务。

另外一方面，Zircon 针对于现代的手机和现代的个人电脑，它们都具有快速的处理器，大量的内存 RAM 以及任意的外围设备，用于完成开放式的计算。

详细来说，几个明显的区别如下：

+ LK 可以运行于32位系统，Zircon 只是64位。
+ Zircon 有第一类的用户模式支持，而 LK 没有用户模式概念。
+ Zircon 有本位的安全模型，而 LK 认为所有代码都是可信的。

随着时间推移，LK 的底层构件也发生变化用于适应新的需求，以及更好满足系统其他部分功能。
