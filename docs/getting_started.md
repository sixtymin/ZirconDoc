<!--
# Quick Start Recipes

## Checking out the Zircon source code

*** note
NOTE: The Fuchsia source includes Zircon. See Fuchsia's
[Getting Started](https://fuchsia.googlesource.com/docs/+/master/getting_started.md)
doc. Follow this doc to work on only Zircon.
***

The Zircon Git repository is located
at: https://fuchsia.googlesource.com/zircon

To clone the repository, assuming you setup the $SRC variable
in your environment:
```shell
git clone https://fuchsia.googlesource.com/zircon $SRC/zircon
```

For the purpose of this document, we will assume that Zircon is checked
out in $SRC/zircon and that we will build toolchains, QEMU, etc alongside
that.  Various make invocations are presented with a "-j32" option for
parallel make.  If that's excessive for the machine you're building on,
try -j16 or -j8.
-->

# 快速入门参考

## 检出 Zircon 的源码

> 注：Fuchsia 的源码中包含了 Zircon。参考 Fuchsia 的 [入门指南](https://fuchsia.googlesource.com/docs/+/master/getting_started.md) 文档。本文档只针对 Zircon。

Zircon 的 Git 仓库位于: [https://fuchsia.googlesource.com/zircon](https://fuchsia.googlesource.com/zircon)。使用如下的命令克隆源码仓库，假设你已经设置了`$SRC`环境变量：

```shell
git clone https://fuchsia.googlesource.com/zircon $SRC/zircon
```

本文接下来的内容都假设你已经将代码检出到了`$SRC/zircon`目录，并且在旁边会单独建立编译工具链，QEMU等。各种`make`调用都使用了选项`-j32`用于并行`make`。如果这个选项在编译时超过了你机器的硬件配置，可以尝试用选项`-j16`或`-j8`。

<!--
## Preparing the build environment

### Ubuntu

On Ubuntu this should obtain the necessary pre-reqs:
```
sudo apt-get install texinfo libglib2.0-dev autoconf libtool bison libsdl-dev build-essential
```

### macOS
Install the Xcode Command Line Tools:
```
xcode-select --install
```

Install the other pre-reqs:

* Using Homebrew:
```
brew install wget pkg-config glib autoconf automake libtool
```

* Using MacPorts:
```
port install autoconf automake libtool libpixman pkgconfig glib2
```
-->

## 准备编译环境

### Ubuntu

在 Ubuntu 上，可以通过如下的命令来获取：

```
sudo apt-get install texinfo libglib2.0-dev autoconf libtool bison libsdl-dev build-essential
```

### macOS

首先使用命令行控制台安装 Xcode 的命令行工具：

```
xcode-select --install
```

安装其他的需要的程序：

* 使用 Homebrew:
```
brew install wget pkg-config glib autoconf automake libtool
```

* 使用 MacPorts:
```
port install autoconf automake libtool libpixman pkgconfig glib2
```

<!--
## Install Toolchains

If you're developing on Linux or macOS, there are prebuilt toolchain binaries available.
Just run this script from your Zircon working directory:

```
./scripts/download-prebuilt
```

If you would like to build the toolchains yourself, follow the instructions later
in the document.
-->

## 安装编译工具链

如果你在 Linux 或 macOS 上开发，这有一些预编译的工具链二进制程序可用。只需要在 Zircon 的工作目录执行如下的脚本即可：

```
./scripts/download-prebuilt
```

如果你想要构建自己的工具链，可以参考文档后面的内容。

<!--
## Build Zircon

Build results will be in $SRC/zircon/build-{arm64,x64}

The variable $BUILDDIR in examples below refers to the build output directory
for the particular build in question.

```
cd $SRC/zircon

# for aarch64
make -j32 arm64

# for x64
make -j32 x64
```
-->

## 编译 Zircon

编译结果会放在 `$SRC/zircon/build-{arm64,x64}` 目录。

在下面例子中的环境变量 `$BUILDDIR` 指向问题中特定编译目标的编译输出目录。

```
cd $SRC/zircon

# aarch64 架构
make -j32 arm64

# x64 架构
make -j32 x64
```

<!--
### Using Clang

To build Zircon using Clang as the target toolchain, set the
`USE_CLANG=true` variable when invoking Make.

```
cd $SRC/zircon

# for aarch64
make -j32 USE_CLANG=true arm64

# for x64
make -j32 USE_CLANG=true x64
```

## Building Zircon for all targets

```
# The -r enables release builds as well
./scripts/buildall -r
```

Please build for all targets before submitting to ensure builds work
on all architectures.
-->

### 使用 Clang

要使用 Clang 作为目标工具链来编译 Zircon，在调用 `make` 进行编译时设置选项 `USE_CLANG=true`。

```
cd $SRC/zircon

# aarch64架构
make -j32 USE_CLANG=true arm64

# x64架构
make -j32 USE_CLANG=true x64
```

## 编译所有目标平台的 Zircon

```
# 选项 -r 开启发布编译
./scripts/buildall -r
```

在提交代码前，请为所有的目标平台进行编译，保证所有架构上的编译都是无错误的。

<!--
## QEMU

You can skip this if you're only testing on actual hardware, but the emulator
is handy for quick local tests and generally worth having around.

See [QEMU](qemu.md) for information on building and using QEMU with zircon.


## Build Toolchains (Optional)

If the prebuilt toolchain binaries do not work for you, you can build your
own from vanilla upstream sources.

 * The GCC toolchain is used to build Zircon by default.
 * The Clang toolchain is used to build Zircon if you build with
   `USE_CLANG=true` or `USE_ASAN=true`.
 * The Clang toolchain is also used by default to build host-side code, but
   any C++14-capable toolchain for your build host should work fine.

Build one or the other or both, as needed for how you want build Zircon.
-->

## QEMU

如果你只是在真实的硬件上进行测试，那么你可以跳过这部分内容。但是要进行快速的本地测试使用模拟器非常方便，通常也很值得。

参考 [QEMU](qemu.md) 获取更多编译 QEMU 以及使用QEMU运行 Zircon 的内容。

## 编译工具链（可选）

如果预编译的工具链二进制程序在你的机器上不工作，你可以获取这些工具的源码自己编译它们。

* 默认使用 GCC 工具链编译 Zircon。
* 如果在编译时添加参数 `USE_CLANG=true` 或 `USE_ASAN=true`，则表示使用 Clang 工具链编译 Zircon。
* Clang 工具链默认也用于宿主端代码的编译，但是用于编译宿主端工具的工具链都必须是兼容`C++14`的。

编译一个或另外一方，再或者两者都编译，这取决于你想如何编译 Zircon。

<!--
### GCC Toolchain

We use GNU `binutils` 2.30(`*`) and GCC 8.2(`**`), configured with
`--enable-initfini-array --enable-gold`, and with `--target=x86_64-elf
--enable-targets=x86_64-pep` for x86-64 or `--target=aarch64-elf` for ARM64.

For `binutils`, we recommend `--enable-deterministic-archives` but that switch
is not necessary to get a working build.

For GCC, it's necessary to pass `MAKEOVERRIDES=USE_GCC_STDINT=provide` on the
`make` command line.  This should ensure that the `stdint.h` GCC installs is
one that works standalone (`stdint-gcc.h` in the source) rather than one that
uses `#include_next` and expects another `stdint.h` file installed elsewhere.

Only the C and C++ language support is required and no target libraries other
than `libgcc` are required, so you can use various `configure` switches to
disable other things and make your build of GCC itself go more quickly and use
less storage, e.g. `--enable-languages=c,c++ --disable-libstdcxx
--disable-libssp --disable-libquadmath`.  See the GCC installation
documentation for more details.

You may need various other `configure` switches or other prerequisites to
build on your particular host system.  See the GNU documentation.

(`*`) The `binutils` 2.30 release has some harmless `make check` failures in
the `aarch64-elf` and `x86_64-elf` configurations.  These are fixed on the
upstream `binutils-2_30-branch` git branch, which is what we actually build.
But the 2.30 release version works fine for building Zircon; it just has some
spurious failures in its own test suite.

(`**`) As of 2008-6-15, GCC 8.2 has not been released yet.  There is no
released version of GCC that works for building Zircon without backporting
some fixes.  What we actually use is the upstream `gcc-8-branch` git branch.
-->

<!--
### Clang/LLVM Toolchain

We use a trunk snapshot of Clang and update to new snapshots frequently.  Any
build of recent-enough Clang with support for `x86_64` and `aarch64` compiled
in should work.  You'll need a toolchain that also includes the runtime
libraries.  We normally also use the same build of Clang for the host as well
as for the `*-fuchsia` targets.  See
[here](https://fuchsia.googlesource.com/docs/+/master/development/build/toolchain.md)
for details on how we build Clang.

### Set up `local.mk` for toolchains

If you're using the prebuilt toolchains, you can skip this step, since
the build will find them automatically.

Create a GNU makefile fragment in `local.mk` that points to where you
installed the toolchains:

```makefile
CLANG_TOOLCHAIN_PREFIX := .../clang-install/bin/
ARCH_x86_64_TOOLCHAIN_PREFIX := .../gnu-install/bin/x86_64-elf-
ARCH_arm64_TOOLCHAIN_PREFIX := .../gnu-install/bin/aarch64-elf-
```

Note that `CLANG_TOOLCHAIN_PREFIX` should have a trailing slash, and the
`ARCH_*_TOOLCHAIN_PREFIX` variables for the GNU toolchains should include the
`${target_alias}-` prefix, so that simple command names like `gcc`, `ld`, or
`clang` can be appended to the prefix with no separator.  If the `clang` or
`gcc` in your `PATH` works for Zircon, you can just use empty prefixes.
-->

<!--
## Copying files to and from Zircon

With local link IPv6 configured, the host tool ./build-ARCH/tools/netcp
can be used to copy files.

```
# Copy the file myprogram to Zircon
netcp myprogram :/tmp/myprogram

# Copy the file myprogram back to the host
netcp :/tmp/myprogram myprogram
```
-->

## 与 Zircon 相互拷贝文件

如果配置了本地的 IPv6 链接，宿主机端工具 `./build-ARCH/tools/netcp` 可以用于拷贝文件。

```
# 将文件 myprogram 拷贝到 Zircon 中
netcp myprogram :/tmp/myprogram

# 将文件 myprogram 从 Zircon 拷贝回宿主机
netcp :/tmp/myprogram myprogram
```

<!--
## Including Additional Userspace Files

The Zircon build creates a bootfs image containing necessary userspace components
for the system to boot (the device manager, some device drivers, etc).  The kernel
is capable of including a second bootfs image which is provided by QEMU or the
bootloader as a ramdisk image.

To create such a bootfs image, use the zbi tool that's generated as part of
the build.  It can assemble a bootfs image for either source directories (in which
case every file in the specified directory and its subdirectories are included) or
via a manifest file which specifies on a file-by-file basis which files to include.

```
$BUILDDIR/tools/zbi -o extra.bootfs @/path/to/directory

echo "issue.txt=/etc/issue" > manifest
echo "etc/hosts=/etc/hosts" >> manifest
$BUILDDIR/tools/zbi -o extra.bootfs manifest
```

On the booted Zircon system, the files in the bootfs will appear under /boot, so
in the above manifest example, the "hosts" file would appear at /boot/etc/hosts.

For QEMU, use the -x option to the run-zircon-* scripts to specify an extra bootfs image.
-->

## 包含更多的用户空间文件

Zircon 编译创建了 bootfs 映像，它包含了必要的用于系统引导的用户空间组件（设备管理器，一些设备驱动等）。内核能够包括第二个由 QEMU 提供的 bootfs 映像，或将引导加载器作为 randisk 映像。

要创建这样的 bootfs 映像，要使用 zbi 工具，它是编译输出的一部分。它可以用于汇集文件创建一个 bootfs，既可以用于源目录（其中包括指定目录和它的子目录在内的所有文件），也可以使用 manifest 配置文件来逐一指定要包含进 bootfs中的文件。

```
$BUILDDIR/tools/zbi -o extra.bootfs @/path/to/directory

echo "issue.txt=/etc/issue" > manifest
echo "etc/hosts=/etc/hosts" >> manifest
$BUILDDIR/tools/zbi -o extra.bootfs manifest
```

在引导的 Zircon 系统上，bootfs 中的文件将会出现在 `/boot` 目录中，因此在上述的配置文件例子中，`hosts`文件会出现在 `/boot/etc/hosts`。

对于 QEMU 来说，使用 `-x` 选项来执行 `run-zircon-*` 脚本来指定额外的 bootfs 映像。

<!--
## Network Booting

Network booting is supported via two mechanisms: Gigaboot and Zirconboot.
Gigaboot is an EFI based bootloader whereas zirconboot is a mechanism that
allows a minimal zircon system to serve as a bootloader for zircon.

On systems that boot via EFI (such as Acer and NUC), either option is viable.
On other systems, zirconboot may be the only option for network booting.
-->

## 网络引导

网络引导通过两种机制支持：Gigaboot 和 Zirconboot。Gigaboot是一个基于 EFI的引导程序，但是 zirconboot是一种机制，它允许小型zircon系统作为引导程序服务于 Zircon 的引导启动。

在通过 EFI （例如 Acer 和 NUC）引导的系统上，这两个选项都可用。在其他的系统上，Zirconboot 可能是唯一可用的网络引导。

<!--
### Via Gigaboot
The [GigaBoot20x6](https://fuchsia.googlesource.com/zircon/+/master/bootloader) bootloader speaks a simple network boot protocol (over IPV6 UDP)
which does not require any special host configuration or privileged access to use.

It does this by taking advantage of IPV6 Link Local Addressing and Multicast,
allowing the device being booted to advertise its bootability and the host to find
it and send a system image to it.

If you have a device (for example a Broadwell or Skylake Intel NUC) running
GigaBoot20x6 first create a USB drive [manually](https://fuchsia.googlesource.com/zircon/+/master/docs/targets/acer12.md#How-to-Create-a-Bootable-USB-Flash-Drive)
or (Linux only) using the [script](https://fuchsia.googlesource.com/scripts/+/master/build-bootable-usb-gigaboot.sh).

```
$BUILDDIR/tools/bootserver $BUILDDIR/zircon.bin

# if you have an extra bootfs image (see above):
$BUILDDIR/tools/bootserver $BUILDDIR/zircon.bin /path/to/extra.bootfs
```

By default bootserver will continue to run and every time it observes a netboot
beacon it will send the kernel (and bootfs if provided) to that device.  If you
pass the -1 option, bootserver will exit after a successful boot instead.
-->

### 通过 Gigaboot 引导

[GigaBoot20x6](https://fuchsia.googlesource.com/zircon/+/master/bootloader) 引导程序使用简单的网络引导协议（使用 IPV6 的 UDP），它不需要任何特殊的主机配置或权限访问要求。

它通过使用 IPv6链接本地寻址和多播实现，通过被引导硬件广播它的可引导性，主机就会找到它，并且将系统映像发送给它。

如果你有硬件设备（比如 Broadwell 或 Skylake Intel NUC）运行了 GigaBoot20x6，首先创建一个USB驱动 
[manually](https://fuchsia.googlesource.com/zircon/+/master/docs/targets/acer12.md#How-to-Create-a-Bootable-USB-Flash-Drive) 或者使用 [脚本](https://fuchsia.googlesource.com/scripts/+/master/build-bootable-usb-gigaboot.sh) （仅用于 Linux）。

```
$BUILDDIR/tools/bootserver $BUILDDIR/zircon.bin

# 如果有额外的引导文件映像（参考上面内容）:
$BUILDDIR/tools/bootserver $BUILDDIR/zircon.bin /path/to/extra.bootfs
```

默认情况下，bootserver 会持续运行，每次它发现了网络引导的信号，它就会发送内核（如果提供了bootfs，则也包括它）到这台设备上。如果你传递 `-l` 选项，bootserver 会在一次成功引导后退出。

<!--
### Via Zirconboot
Zirconboot is a mechanism that allows a zircon system to serve as the
bootloader for zircon itself. Zirconboot speaks the same boot protocol as
Gigaboot described above.

To use zirconboot, pass the `netsvc.netboot=true` argument to zircon via the
kernel command line. When zirconboot starts, it will attempt to fetch and boot
into a zircon system from a bootserver running on the attached host.
-->

### 通过 Zirconboot 启动

Zirconboot 是一种引导机制，它允许 zircon 系统作为 bootloader 来为 Zircon 本身服务。Zirconboot 使用于上面描述的 Gigaboot 一样的引导协议。

要使用 zirconboot，需要通过内核命令行给 zircon 传递`netsvc.netboot=true`参数。当 Zirconboot 启动时，它会尝试从运行在挂接宿主机上的 bootserver 上获取 Zircon系统，并且引导进入系统。

<!--
## Network Log Viewing

The default build of Zircon includes a network log service that multicasts the
system log over the link local IPv6 UDP.  Please note that this is a quick hack
and the protocol will certainly change at some point.

For now, if you're running Zircon on QEMU with the -N flag or running on hardware
with a supported ethernet interface (ASIX USB Dongle or Intel Ethernet on NUC),
the loglistener tool will observe logs broadcast over the local link:

```
$BUILDDIR/tools/loglistener
```
-->

## 网络日志查看

Zircon 默认的编译中包含了网络日志服务，它将系统的日志通过本地 IPv6 的 UDP 链接进行多播。请注意这只是一个快速开发中使用的手段，在以后完善过程中这个协议会发生变化。

当前，如果在 `QEMU` 上使用 `-N` 标记执行 Zircon，或者在支持网卡接口（ASIX USB软件狗或NUC上的Intel的以太网卡）的硬件上运行 Zircon，工具`loglistener`会通过本地连接观察广播日志信息。

```
$BUILDDIR/tools/loglistener
```

<!--
## Debugging

For random tips on debugging in the zircon environment see
[debugging](debugging/tips.md).

## Contribute changes
* See [contributing.md](contributing.md).
-->

## 调试

在 Zircon 环境中的一些调试小贴士可以参考 [debugging](debugging/tips.md)。

## 贡献变化

* 参考文档 [contributing.md](contributing.md) 。