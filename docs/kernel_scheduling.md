<!--
Zircon Scheduling
=================

Background
==========

The primary responsibility of any scheduler is to share the limited
resource of processor time between all threads that wish to use it. In a
general purpose operating system, it tries to do so in a fair way,
ensuring that all threads are allowed to make some progress.

Our scheduler is an evolution of LK’s scheduler. As such it
started as a minimal scheduler implementation and was extended to meet
our needs as the project grew.
-->

Zircon 调度
==========

背景
===

任何调度器的首要任务都是在所有希望使用CPU的线程之间分享有限的处理器时间资源。在通用操作系统中，调度器尝试用一种公平的方法来实现这个任务，从而确保所有线程都可以做一些处理。

我们的调度器是 LK 调度器的改进。因此，在开始时它还是一个最小的调度器实现，随着项目增长，它也被扩展以满足需求。

<!--
Design
======

#### Overview

In essence there is a scheduler running on each logical CPU in the machine.
These schedulers run independently and use IPI (Inter-Processor
Interrupts) to coordinate. However each CPU is responsible for
scheduling the threads that are running on it. See [*CPU
Assignment*](#cpu-assignment-and-migration) below for how we decide
which CPU a thread is on, and how/when it migrates.

Each CPU has its own set of priority queues. One for each priority level
in the system, currently 32. Note that these are fifo queues, not the data
structure known as a priority queue. In each queue is an ordered list of
runnable threads awaiting execution. When it is time for a new thread to run,
the scheduler simply looks at the highest numbered queue that contains a thread,
pops the head off of that queue and runs that thread.See
[*Priority Management*](#priority-management) below for more details
about how it decides which thread should be in which queue. If there are no
threads in the queues to run it will instead run the idle thread, see [*Realtime
and Idle Threads*](#realtime-and-idle-threads) below for more details.
-->

设计
===

#### 概述

本质上讲，在每个机器中的每一个逻辑 CPU 都运行了一个调度器。这些调度器独立运行，使用 IPI（处理期间中断）来进行协作。但是，每个 CPU 负责调度执行在它上面的线程。参考下面的 [*CPU Assignment*](#cpu-assignment-and-migration) 了解我们如何确定一个线程运行在那个CPU上，以及何时，如何将它转移。

每个 CPU 都有他自己的一组优先级队列。系统中的每一个优先级，当前值为32。注意，一般有fifo队列，不是数据结构而是优先级队列。每一个队列都是一组等待执行的可执行线程的序列。当到了新线程运行的时候，调度器简单地查看最大编号队列中的线程，将第一个弹出队列，然后运行这个线程。参考下面的内容 
[*Priority Management*](#priority-management) 了解更多关于那个队列中的那个线程应该运行。如果在队列中没有线程要运行，那么 CPU 会执行一个空闲线程，参考 [*Realtime and Idle Threads*](#realtime-and-idle-threads) 了解详细信息。

<!--
Each thread is assigned the same timeslice size (THREAD_INITIAL_TIME_SLICE)
when it is picked to start running. If it uses its whole timeslice it will be
reinserted at the end of the appropriate priority queue. However if it has
some of its timeslice remaining from a previous run it will be inserted at the
head of the priority queue so it will be able to resume as quickly as possible.
When it is picked back up again it will only run for the remainder of its
previous timeslice.

When the scheduler selects a new thread from the priority queue it sets
the CPU's preemption timer for either a full timeslice, or the remainder of the
previous timeslice. When that timer fires the scheduler will stop execution on
that thread, add it to the appropriate queue, select another thread and start
over again.

If a thread blocks waiting for a shared resource then it's taken out of
its priority queue and is placed in a wait queue for the shared resource.
When it is unblocked it will be reinserted in the appropriate priority
queue of an eligible CPU ([*CPU
Assignment*](#cpu-assignment-and-migration)) and if it had remaining timeslice
to run it will be added to the front of the queue for expedited handling.
-->

当线程被选中开始运行时，每一个线程都被赋予了相同的时间片值（THREAD_INITIAL_TIME_SLICE）。如果它用完了整个时间片，它会被插入到适当优先级队列的末尾。但是，如果它从上一次运行后还有一些时间片，那么它会被插入到优先级队列的头部，这样它可以尽快恢复执行。当它再次被选中时，那么它就只能运行前一次剩余的时间片长度。

当调度器从优先级队列中选择了一个新的线程，它会设置 CPU 的抢占定时器为完整时间片或前一个时间片剩余值。当定时器触发时，调度器会暂停当前线程执行，将它放回适当优先级的队列中，选择另外一个线程并且继续执行。

如果线程阻塞等待共享资源，那么它会被拿出它的优先级队列，放到共享资源的等待队列中。当线程解除了阻塞，它就被重新插入到合适的CPU（[*CPU Assignment*](#cpu-assignment-and-migration)）的某个适当的优先级队列中，如果它还有剩余的时间片可以执行，它会被放到队列头部以加快处理。

<!--
#### Priority Management

There are three different factors used to determine the effective
priority of a thread, the effective priority being what is used to
determine which queue it will be in.

The first factor is the base priority, which is simply the thread’s
requested priority. There are currently 32 levels with 0 being the
lowest and 31 being the highest.

The second factor is the priority boost. This is a value bounded between
\[-MAX_PRIORITY_ADJ, MAX_PRIORITY_ADJ\] used to offset the base priority, it is
modified by the following cases:

-   When a thread is unblocked, after waiting on a shared resource or
    sleeping, it is given a one point boost.

-   When a thread yields (volunteers to give up control), or volunteers
    to reschedule, its boost is decremented by one but is capped at 0
    (won’t go negative).

-   When a thread is preempted and has used up its entire timeslice, its
    boost is decremented by one but is able to go negative.
-->

#### 优先级管理

有三个不同的影响因素用于确定线程的优先级，有效的优先级用于确定线程要被排入那个队列。

第一个因子是基础优先级，它就是线程请求的优先级。有32个级别，其中0是最低优先级，31为最高优先级。

第二个因子是优先级增进。在 \[-MAX_PRIORITY_ADJ, MAX_PRIORITY_ADJ\] 之间有一个值边界用于在基础优先级上进行偏移，它可以在如下的情况中进行修改：

- 在线程等待共享资源或睡眠后，线程解除阻塞时，它可以得到一个点的优先级提升

- 当线程放弃执行（资源放弃 CPU 控制），或自愿被调度，它的优先级会降低一个点，但是值不会越过0（不会变为负数）。

- 当线程抢占 CPU，已经用完了它整个时间片，那么它的优先级减少一个点，并且它的值可以变为负数。

<!--
The third factor is its inherited priority. If the thread is in control
of a shared resource and it is blocking another thread of a higher
priority then it is given a temporary boost up to that thread’s priority
to allow it to finish quickly and allow the higher priority thread to
resume.

The effective priority of the thread is either the inherited priority,
if it has one, or the base priority plus its boost. When this priority
changes, due to any of the factors changing, the scheduler will move it
to a new priority queue and reschedule the CPU. Allowing it to have
control if it is now the highest priority task, or relinquish control if
it is no longer highest.

The intent in this system is to ensure that interactive threads are
serviced quickly. These are usually the threads that interact directly
with the user and cause user-perceivable latency. These threads usually
do little work and spend most of their time blocked awaiting another
user event. So they get the priority boost from unblocking while
background threads that do most of the processing receive the priority
penalty for using their entire timeslice.
-->

第三个因子是它的继承优先级。如果线程正控制着一组共享资源，并且它阻塞了另外一个更高优先级的线程，那么就会给这个线程一个临时的优先级提升，达到被阻塞线程优先级级别，这样使得它可以尽快结束执行，允许更高优先级线程恢复执行。

线程的有效优先级或者是继承优先级（只要它有），或者就是基础优先级加上优先级提升的值。当优先级变化时，由于任何一个因子变化，调度器都会将它移动到新的优先级队列，并且重新在CPU上进行调度。如果线程现在是最高优先级任务，则允许线程控制 CPU，或者如果它不再是最高优先级，则放弃对 CPU 的控制。

这个系统中的目的是确保交互线程可以尽快响应。这些线程通常都是直接与用户交互的线程，容易引起用户角度的延迟。这些线程通常做很少工作，大部分时间都是阻塞当代另外一个用户事件。因此，当它们从阻塞中恢复后可以获得更高优先级，后台线程因为处理数据，每次都会用尽它们的时间片而受到优先级惩罚，导致优先级很低。

<!--
#### CPU Assignment and Migration

Threads are able to request which CPUs on which they wish to run using a
CPU affinity mask, a 32 bit mask where 0b001 is CPU 1, 0b100 is CPU 3,
and 0b101 is either CPU 1 or CPU 3. This mask is usually respected but
if the CPUs it requests are all inactive it will be assigned to another
CPU. Also notable, if it is “pinned” to a CPU, that is its mask contains
only one CPU, and that CPU becomes inactive the thread will sit
unserviced until that CPU becomes active again. See [*CPU
Activation*](#cpu-activation) below for details.

When selecting a CPU for a thread the scheduler will choose, in order:

1.  The CPU doing the selection, if it is **idle** and in the affinity mask.

2.  The CPU the thread last ran on, if it is **idle** and in the affinity mask.

3.  Any **idle** CPU in the affinity mask.

4.  The CPU the thread last ran on, if it is active and in the affinity mask.

5.  The CPU doing the selection, if it is the only one in the affinity mask or
    all cpus in the mask are not active.

6.  Any active CPU in the affinity mask.
-->

#### CPU 分配和转移

线程能够使用 CPU 亲和力掩码来请求它们想要运行的 CPU。32位掩码 `0b001` 表示 CPU 1，`0b100` 表示CPU 3，值 `0b101` 表示或者是 CPU 1 或者是 CPU 3。这个掩码通常是有效的，但是如果线程请求的 CPU 都是不活动的，那么它将被分配给另外一个 CPU。同时值得注意的是，如果线程被“钉”到了一个 CPU 上，即它的掩码中只包含一个 CPU，如果 CPU 变为不活跃状态，那么该线程会一直处于不服务，直到这个 CPU 再次变为活动状态。参考下文的 [*CPU Activation*](#cpu-activation)了解详细信息。

当为一个线程选择了一个 CPU，调度器会按照顺序选择线程：

1. 如果 CPU 处于空闲状态，且处于亲和力掩码中，则 CPU 选择线程。

2. 如果 CPU 处于空闲状态，且位于亲和力掩码中，那么线程分配到上次运行的CPU上执行。

3.  选择任何一个在亲和力掩码中的空闲 CPU。

4.  如果 CPU 处于活动状态，且位于亲和力掩码总，则选择线程上一次执行的 CPU。

5.  如果 CPU 是亲和力掩码中为唯一一个或掩码中所有的CPU都处于不活动状态，则选择这个 CPU 或这些 CPU 中一个。

6.  亲和力掩码中的任何一个活动 CPU。

<!--
If the thread is running on a CPU not in its affinity mask (due to case
5 above) the scheduler will try to rectify this every time the thread is
preempted, yields, or voluntarily reschedules. Also if the thread
changes its affinity mask the scheduler may migrate it.

Every time a thread comes back from waiting on a shared resource or
sleeping and needs to be assigned a priority queue, the scheduler will
re-evaluate its CPU choice for the thread, using the above logic, and
may move it.
-->

如果线程运行于不在它亲和力掩码中的 CPU 上（由于情况5导致），调度器会尝试在每次线程被抢占，放弃执行或自愿被调度时进行改正。同样，如果线程修改了它的亲和力掩码，那么调度器也会转移线程。

线程每次从等待共享资源或睡眠中返回时，要被重新分配新的优先级队列，调度器会重新评估它的 CPU 选择，使用上述的逻辑进行移动。

<!--
#### CPU Activation

When a CPU is being deactivated, that is shutdown and removed from the
system, the scheduler will transition all running threads onto other
CPUs. The only exception is threads that are “pinned”, that is they only
have the deactivating CPU in their affinity mask, these threads are put
back into the run queue where they will sit unserviced until the CPU is
reactivated.

When a CPU is reactivated it will service the waiting pinned threads and
threads that are running on non-Affinity CPUs should be migrated back
pretty quickly by their CPUs scheduler due to the above rules. There is
no active rebalancing of threads to the newly awakened CPU, but as it
should be idle more often, it should see some migration due to the logic
laid out above in [*CPU Assignment and
Migration*](#cpu-assignment-and-migration).
-->

#### CPU 激活

当 CPU 被置为无效时，那就是这个 CPU 被关闭，并且从系统中移除，调度器会将它上面运行的所有线程转移到其他的 CPU 上。例外情况是线程被“钉”到了这个 CPU 上，即在线程的亲和力掩码中只有这个非活动状态的 CPU。这些线程会被放回到执行队列，它们会处于不服务直到绑定的 CPU 激活。

当一个 CPU 重新激活时，它将会服务等待的钉死线程，执行在非亲和 CPU 上的线程根据上面的规则，它们应该被尽快移回自己的 CPU 上调度。新唤醒的 CPU 没有这些线程的可用资源重平衡，但是它应该尽可能多地处于空闲状态，由于前面说的逻辑 [*CPU 分配和转移*](#cpu-assignment-and-migration)，这个CPU应该会看到线程的转移。

<!--
#### Realtime and Idle Threads

These are special threads that are treated a little differently.

The idle thread runs when no other threads are runnable. There is one on
each CPU and it lives outside of the priority queues, but effectively in
a priority queue of -1. It is used to track idle time and can be used by
platform implementations for a low power wait mode.

Realtime threads (marked with `THREAD_FLAG_REAL_TIME`) are allowed to run without
preemption and will run until they block, yield, or manually reschedule.
-->

#### 实时和空闲线程

有一些特殊线程应该被特殊对待，它们与上述线程有一些不同。

当没有其他可执行线程时，空闲线程开始执行。每一个 CPU 上都有一个空闲线程，它存在于优先级队列之外，但是在优先级 `-1` 的队列有效。它用于记录空闲时间，可以被平台用于实现低功耗的等待模式。

实时线程（被标记 `THREAD_FLAG_REAL_TIME` 标注）可以运行无抢占运行，会一直运行直到它们阻塞，放弃执行或手动重新调度。
