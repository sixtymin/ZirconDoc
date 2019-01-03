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