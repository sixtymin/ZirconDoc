<!--
# Notes for hacking on Zircon

This file contains a random collection of notes for hacking on Zircon.

[TOC]

## Building and testing

To ensure changes don't impact any of the builds it is recommended that
that one tests all targets, with gcc and clang, and in both release mode
and debug mode. This can all be executed with the `buildall` script:

```./scripts/buildall -q -c -r```

From the zircon shell run `k ut all` to execute all kernel tests, and
`runtests` to execute all userspace tests.
-->

# Zircon 小工具说明

这篇文章包含了几个 Zircon 小工具的说明。

[TOC]

## 编译和测试

为了确保不影响任何推荐的编译目标，一次编译测试所有的目标，用 gcc 和 clang，即测试发布模式与测试调试模式。这些可以一次使用 `buildall` 脚本执行：

```./scripts/buildall -q -c -r```

在 Zircon 的 Shell 中执行 `k ut all` 执行所有的内核测试，`runtests` 执行所有的用户空间测试。

<!--
## Syscall generation

Syscall support is generated from
system/public/zircon/syscalls.abigen.  A host tool called
[abigen](../system/host/abigen) consumes that file and produces output
for both the kernel and userspace in a variety of languages. This
output includes C or C++ headers for both the kernel and userspace,
syscall entry points, other language bindings, and so on.

This tool is invoked as a part of the build, rather than checking in
its output.

## Terminal navigation and keyboard shortcuts

* Alt+Tab switches between VTs
* Alt+F{1,2,...} switches directly to a VT
* Alt+Up/Down scrolls up and down by lines
* Shift+PgUp/PgDown scrolls up and down by half page
* Ctrl+Alt+Delete reboots
-->

## 系统调用生成

系统调用支持是从 `system/public/zircon/syscalls.abigen` 生成的，宿主机工具 [abigen](../system/host/abigen) 处理这个文件，并且为内核和用户空间以各种语言形式生成输出。输出中包含了 `C` 或 `C++` 头文件，即用于内核，也用于用户空间，即系统调用入口点，其他语言的绑定信息等等。

这个工具是作为编译的一部分来调用的，而不是用于检查它的数据。

## 终止导航和键盘快捷键

* Alt+Tab 在 VTs 之间切换
* Alt+F{1,2,...} 直接切换到某个 VT
* Alt+Up/Down 上下滚动一行
* Shift+PgUp/PgDown 上下滚动半页
* Ctrl+Alt+Delete 重启

<!--
## Kernel panics

Since the kernel can't reliably draw to a framebuffer when the GPU is enabled,
the system will reboot by default if the kernel crashes or panics.

If the kernel crashes and the system reboots, the log from the kernel panic will
appear at `/boot/log/last-panic.txt`, suitable for viewing, downloading, etc.

> Please attach your `last-panic.txt` and `zircon.elf` files to any kernel
> panic bugs you file.

If there's a `last-panic.txt`, that indicates that this is the first successful
boot since a kernel panic occurred.

It is not "sticky" -- if you reboot cleanly, it will be gone, and if you crash
again it will be replaced.

To disable reboot-on-panic, pass the kernel commandline argument
[`kernel.halt-on-panic=true`](kernel_cmdline.md#kernel_halt_on_panic_bool).
-->

## 内核错误

当GPU开启时，内核不能够实际向帧内存画东西，否则如果内核崩溃或错误，默认情况下系统会重启。

如果内核崩溃且系统重新引导，内核应急日志会在 `/boot/log/last-panic.txt` 文件中，用于查看和下载等。

> 如果报告内核错误时，请将你的 `last-panic.txt` 和 `zircon.elf` 文件一起附上。

如果有文件 `last-panic.txt` 存在，那么意味着这是上次内核崩溃发生后第一次成功引导。

这个文件不会持续保留—— 如果你重新完整引导，这个文件会被删除，并且如果你的内核再次崩溃，它也会被替代。

开启崩溃重启功能，在内核命令行参数传递 [`kernel.halt-on-panic=true`](kernel_cmdline.md#kernel_halt_on_panic_bool)。

<!--
## Low level kernel development

For kernel development it's not uncommon to need to monitor or break things
before the gfxconsole comes up.

To force-enable log output to the legacy serial console on an x64 machine, pass
"kernel.serial=legacy".  For other serial configurations, see the kernel.serial
docs in [kernel_cmdline.md](kernel_cmdline.md).

To enable the early console before the graphical console comes up use the
``gfxconsole.early`` cmdline option. More information can be found in
[kernel_cmdline.md](kernel_cmdline.md).
Enabling ``startup.keep-log-visible``will ensure that the kernel log stays
visible if the gfxconsole comes up after boot. To disable the gfxconsole
entirely you can disable the video driver it is binding to via ``driver.<driver
name>.disable``.
On a skylake system, all these options together would look something like:

```
$ tools/build-x86/bootserver build-x86/zircon.bin -- gfxconsole.early driver.intel-i915-display.disable
```

To directly output to the console rather than buffering it (useful in the event
of kernel freezes) you can enable ``ENABLE_KERNEL_LL_DEBUG`` in your ``local.mk`` like so:

```
EXTERNAL_KERNEL_DEFINES := ENABLE_KERNEL_LL_DEBUG=1

```
-->

## 底层内核开发

对于内核开发，需要监视器或断点在 gfxconsole 启动起来之前触发时很平常的情况。

在X64机器上要强制开启日志输出到传统串口控制台，只需要传递命令行参数 `kernel.serial=legacy`。其他串口的配置，参考 [kernel_cmdline.md](kernel_cmdline.md) 中的 `kernel.serial` 文档。

在图形控制台出现之前，要开启早期控制台，可以使用 `gfxconsole.early` 命令行选项。更多信息参考 [kernel_cmdline.md](kernel_cmdline.md) 中的信息。

开启 `startup.keep-log-visible`，如果在引导后 gfxconsole 出现，会保证内核日志持续可见。完全关闭 `gfxconsole`，可以通过关闭它绑定的视频驱动来实现，使用命令行参数 `driver.<drivername>.disable`。

在 Skylake（CPU架构） 系统中，所有这些选项放到一起如下所示：

```
$ tools/build-x86/bootserver build-x86/zircon.bin -- gfxconsole.early driver.intel-i915-display.disable
```

直接将日志输出到控制台而不使用缓存（在内核事件冻结情况下有用），可以在 `local.mk` 中添加 `ENABLE_KERNEL_LL_DEBUG`值，类似如下情况：

```
EXTERNAL_KERNEL_DEFINES := ENABLE_KERNEL_LL_DEBUG=1
```

<!--
There is also a kernel cmdline parameter kernel.bypass-debuglog, which can be set
to true to force output to the console instead of buffering it. The reason we have
both a compile switch and a cmdline parameter is to facilitate prints in the kernel
before cmdline is parsed to be forced to go to the console. The compile switch setting
overrides the cmdline parameter (if both are present). Note that both the compile switch
and the cmdline parameter have the side effect of disabling irq driven uart Tx.

More information on ``local.mk`` can be found via ``make help``

## Changing the compiler optimization level of a module

You can override the default `-On` level for a module by defining in its
`rules.mk`:

```
MODULE_OPTFLAGS := -O0
```
-->

其实也有内核命令行参数 `kernel.bypass-debuglog`，将该参数设置为true来强制日志输出到控制台而不是它的缓存中。之所以有编译开关和命令行参数两个设置，是为了在内核中解析命令行之前利用打印输出信息。编译开关设置会覆盖命令行参数（如果两个开关都设置了）。注意，编译开关和命令行参数在关闭 `irq` 驱动 `uart Tx` 中有副作用。

更多关于 `local.mk` 的信息可以通过 `make help` 命令找到。

## 修改模块的编译器优化级别

可以在 `rules.mk` 中定义参数用于针对某个模块覆盖默认的优化选项 `-On`：

```
MODULE_OPTFLAGS := -O0
```

<!--
## Requesting a backtrace

### From within a user process

For debugging purposes, the system crashlogger can print backtraces by
request. It requires modifying your source, but in the absence of a
debugger, or as a general builtin debug mechanism, this can be useful.

```
#include <lib/backtrace-request/backtrace-request.h>

void my_function() {
  backtrace_request();
}
```

When `backtrace\_request` is called, it causes an
exception used by debuggers for breakpoint handling.
If a debugger is not attached, the system crashlogger will
process the exception, print a backtrace, and then resume the thread.
-->

## 要求栈回溯

### 在用户进程内

为了调试，系统的崩溃日志程序会按要求打印栈回溯。它需要修改你的源码，但是在缺少调试器或作为一个通用的内在的调试机制，它是非常有用的。

```
#include <lib/backtrace-request/backtrace-request.h>

void my_function() {
  backtrace_request();
}
```

当 `backtrace\_request` 调用时，它会引发调试器用于断点处理的一个异常。如果调试没有挂接，系统的崩溃日志程序会处理这个异常，打印站回溯然后恢复线程执行。

<!--
### From a kernel thread

```
#include <kernel/thread.h>

void my_function() {
  thread_print_backtrace(get_current_thread(), __GET_FRAME(0));
}
```

## Exporting debug data during boot

To support testing the system during early boot, there is a mechanism to export
data files from the kernel to the /boot filesystem. To export a data file,
create a VMO, give it a name, and pass it to userboot with handle\_info of type
PA\_VMO\_DEBUG\_FILE (and argument 0). Then userboot will automatically pass it
through to devmgr, and devmgr will export the VMO as a file at the path

```
/boot/kernel/<name-of-vmo>
```

This mechanism is used by the entropy collector quality tests to export
relatively large (~1 Mbit) files full of random data.
-->

### 从内核线程中打印栈回溯

```
#include <kernel/thread.h>

void my_function() {
  thread_print_backtrace(get_current_thread(), __GET_FRAME(0));
}
```

## 引导期间导出调试数据

为了支持早期引导期间的系统，有一个机制，它会从内核导出数据文件到文件系统的 `/boot` 目录。要导出数据文件，创建一个 VMO，然后给它一个名字，将它传递给 `userboot`，使用`PA\_VMO\_DEBUG\_FILE`（参数为0）类型的`handle\_info`。然后 userboot 会自动将它传递给 devmgr，devmgr 会将 VMO 导出到如下路径中的文件：

```
/boot/kernel/<name-of-vmo>
```

这个机制被熵收集器质量测试使用，用于导出相对大的文件，内容为随机数据。


<!--
## How to deprecate #define constants

One can create a deprecated typedef and have the constant definition
cast to that type.  The ensuing warning/error will include the name
of the deprecated typedef.

```
typedef int ZX_RESUME_NOT_HANDLED_DEPRECATION __attribute__((deprecated));
#define ZX_RESUME_NOT_HANDLED ((ZX_RESUME_NOT_HANDLED_DEPRECATION)(2))
```
-->

## 如何废弃 #define 常量

你可以创建一个废弃的类型定义，将常量定义转换为这个类型。接下来出现的警告或错误会包含废弃的类型定义的名字。

```
typedef int ZX_RESUME_NOT_HANDLED_DEPRECATION __attribute__((deprecated));
#define ZX_RESUME_NOT_HANDLED ((ZX_RESUME_NOT_HANDLED_DEPRECATION)(2))
```
