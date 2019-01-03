<!--
# Memory and resource usage

This file contains information about memory and resource management in Zircon,
and talks about ways to examine process and system memory usage.

*** note
**TODO**(dbort): Talk about the relationship between address spaces,
[VMARs](objects/vm_address_region.md), [mappings](syscalls/vmar_map.md), and
[VMOs](objects/vm_object.md)
***
-->

# 内存和资源使用

这个文档包含了 Zircon 中的内存和资源管理的信息，讨论检查进程和系统内存使用的几个方法。

> **TODO** （dbort）: 讨论地址空间，[VMARs](objects/vm_address_region.md)，[映射](syscalls/vmar_map.md)，以及[VMOs](objects/vm_object.md)的关系。

[TOC]

<!--
## Userspace memory

Which processes are using all of the memory?

### Dump total process memory usage

Use the `ps` tool:

```
$ ps
TASK           PSS PRIVATE  SHARED NAME
j:1028       32.9M   32.8M         root
  p:1043   1386.3k   1384k     28k bin/devmgr
  j:1082     30.0M   30.0M         zircon-drivers
    p:1209  774.3k    772k     28k /boot/bin/acpisvc
    p:1565  250.3k    248k     28k devhost:root
    p:1619  654.3k    652k     28k devhost:misc
    p:1688  258.3k    256k     28k devhost:platform
    p:1867 3878.3k   3876k     28k devhost:pci#1:1234:1111
    p:1916   24.4M   24.4M     28k devhost:pci#3:8086:2922
  j:1103   1475.7k   1464k         zircon-services
    p:1104  298.3k    296k     28k crashlogger
    p:1290  242.3k    240k     28k netsvc
    p:2115  362.3k    360k     28k sh:console
    p:2334  266.3k    264k     28k sh:vc
    p:2441  306.3k    304k     28k /boot/bin/ps
TASK           PSS PRIVATE  SHARED NAME
```
-->

## 用户空间内存

那个进程正在使用所有的内存？

### 转储所有进程内存使用情况：

使用 `ps` 工具：

```
$ ps
TASK           PSS PRIVATE  SHARED NAME
j:1028       32.9M   32.8M         root
  p:1043   1386.3k   1384k     28k bin/devmgr
  j:1082     30.0M   30.0M         zircon-drivers
    p:1209  774.3k    772k     28k /boot/bin/acpisvc
    p:1565  250.3k    248k     28k devhost:root
    p:1619  654.3k    652k     28k devhost:misc
    p:1688  258.3k    256k     28k devhost:platform
    p:1867 3878.3k   3876k     28k devhost:pci#1:1234:1111
    p:1916   24.4M   24.4M     28k devhost:pci#3:8086:2922
  j:1103   1475.7k   1464k         zircon-services
    p:1104  298.3k    296k     28k crashlogger
    p:1290  242.3k    240k     28k netsvc
    p:2115  362.3k    360k     28k sh:console
    p:2334  266.3k    264k     28k sh:vc
    p:2441  306.3k    304k     28k /boot/bin/ps
TASK           PSS PRIVATE  SHARED NAME
```

<!--
**PSS** (proportional shared state) is a number of bytes that estimates how much
in-process mapped physical memory the process consumes. Its value is `PRIVATE +
(SHARED / sharing-ratio)`, where `sharing-ratio` is based on the number of
processes that share each of the pages in this process.

The intent is that, e.g., if four processes share a single page, 1/4 of the
bytes of that page is included in each of the four process's `PSS`. If two
processes share a different page, then each gets 1/2 of that page's bytes.

**PRIVATE** is the number of bytes that are mapped only by this process. I.e.,
no other process maps this memory. Note that this does not account for private
VMOs that are not mapped.

**SHARED** is the number of bytes that are mapped by this process and at least
one other process. Note that this does not account for shared VMOs that are not
mapped. It also does not indicate how many processes share the memory: it could
be 2, it could be 50.
-->

**PSS** （proportional shared state）是进程消耗的进程内映射物理内存的字节数的估算。它的值是 `PRIVATE + (SHARED / sharing-ratio)`，`sharing-ratio` 是基于共享本进程内页面的进程数获取的值。

含义是，比如，如果四个进程共享一个页面，那么这个页面的四分之一就计算进四个进程中任何一个进程的`PSS`字段中。如果两个进程共享另外一个不同的页，那么每一个进程就要加上二分之一页的字节数。

**PRIVATE** 是当前这个进程单独映射的内存的字节数，即没有其他的进程映射这块内存。注意，这里并不包含没有映射的私有的VMO。

**SHARED** 是当前进程和至少一个其他进程映射的内存字节数。注意，这并没有包含没有映射的共享 VMO 的数量。它也没有表示有多少进程共享内存，可能是2个进程，也可能是50个进程。

<!--
### Visualize memory usage

If you have a Fuchsia build, you can use treemap to visualize memory usage by
the system.

 1. On your host machine, run the following command from the root of your
    Fuchsia checkout:

    ```./scripts/fx shell memgraph -vt | ./scripts/memory/treemap.py > mem.html```
 2. Open `mem.html` in a browser.

The `memgraph` tool generates a JSON description of system task and memory
information, which is then parsed by the `treemap.py` script. `-vt` says
to include VMOs and threads in the output.
-->

### 虚拟内存使用

如果已经编译了 Fuchsia，可以使用 `treemap` 命令来图形化查看系统使用内存情况。

 1. 在主机上运行从 Fuchsia 的检出根目录执行如下的命令

    ```./scripts/fx shell memgraph -vt | ./scripts/memory/treemap.py > mem.html```

 2. 在浏览器中打开 `mem.html` 页面。

<!--
### Dump a process's detailed memory maps

If you want to see why a specific process uses so much memory, you can run the
`vmaps` tool on its koid (koid is the ID that shows up when running ps) to see
what it has mapped into memory.

```
$ vmaps help
Usage: vmaps <process-koid>

Dumps a process's memory maps to stdout.

First column:
  "/A" -- Process address space
  "/R" -- Root VMAR
  "R"  -- VMAR (R for Region)
  "M"  -- Mapping

  Indentation indicates parent/child relationship.
```

Column tags:

-   `:sz`: The virtual size of the entry, in bytes. Not all pages are
    necessarily backed by physical memory.
-   `:res`: The amount of memory "resident" in the entry, in bytes; i.e., the
    amount of physical memory that backs the entry. This memory may be private
    (only accessible by this process) or shared by multiple processes.
-   `:vmo`: The `koid` of the VMO mapped into this region.
-->

### 转储进程的详细内存映射情况

如果你想要查看为什么一个特定进程使用了那么多内存，可以使用进程的 koid 运行 `vmaps` 工具（koid 是运行`ps` 命令时显示出来的 ID）查看它已经映射的内存。

```
$ vmaps help
Usage: vmaps <process-koid>

Dumps a process's memory maps to stdout.

第一列:
  "/A" -- 进程地址空间
  "/R" -- 根 VMAR
  "R"  -- VMAR (R for Region)
  "M"  -- 映射

  缩进表示父/子关系。
```

列标记：

-   `:sz`: 条目的虚拟大小，字节单位。并不是所有的也都需要有后备的物理内存。
-   `:res`: 在条目中留存的物理内存的数量，字节单位，即有后备物理内存的条目的数量。这块内存可能是私有的（只在当前进程中可访问）或者是多个进程共享。
-   `:vmo`: 映射进这块区域的VMO的`koid`。

<!--
```
$ vmaps 2470
/A ________01000000-00007ffffffff000    128.0T:sz                    'proc:2470'
/R ________01000000-00007ffffffff000    128.0T:sz                    'root'
...
# This 'R' region is a dynamic library. The r-x section is .text, the r--
# section is .rodata, and the rw- section is .data + .bss.
R  00000187bc867000-00000187bc881000      104k:sz                    'useralloc'
 M 00000187bc867000-00000187bc87d000 r-x   88k:sz   0B:res  2535:vmo 'libfdio.so'
 M 00000187bc87e000-00000187bc87f000 r--    4k:sz   4k:res  2537:vmo 'libfdio.so'
 M 00000187bc87f000-00000187bc881000 rw-    8k:sz   8k:res  2537:vmo 'libfdio.so'
...
# This 2MB anonymous mapping is probably part of the heap.
M  0000246812b91000-0000246812d91000 rw-    2M:sz  76k:res  2542:vmo 'mmap-anonymous'
...
# This region looks like a stack: a big chunk of virtual space (:sz) with a
# slightly-smaller mapping inside (accounting for a 4k guard page), and only a
# small amount actually committed (:res).
R  0000358923d92000-0000358923dd3000      260k:sz                    'useralloc'
 M 0000358923d93000-0000358923dd3000 rw-  256k:sz  16k:res  2538:vmo ''
...
# The stack for the initial thread, which is allocated differently.
M  0000400cbba84000-0000400cbbac4000 rw-  256k:sz   4k:res  2513:vmo 'initial-stack'
...
# The vDSO, which only has .text and .rodata.
R  000047e1ab874000-000047e1ab87b000       28k:sz                    'useralloc'
 M 000047e1ab874000-000047e1ab87a000 r--   24k:sz  24k:res  1031:vmo 'vdso/full'
 M 000047e1ab87a000-000047e1ab87b000 r-x    4k:sz   4k:res  1031:vmo 'vdso/full'
...
# The main binary for this process.
R  000059f5c7068000-000059f5c708d000      148k:sz                    'useralloc'
 M 000059f5c7068000-000059f5c7088000 r-x  128k:sz   0B:res  2476:vmo '/boot/bin/sh'
 M 000059f5c7089000-000059f5c708b000 r--    8k:sz   8k:res  2517:vmo '/boot/bin/sh'
 M 000059f5c708b000-000059f5c708d000 rw-    8k:sz   8k:res  2517:vmo '/boot/bin/sh'
...
```
-->

```
$ vmaps 2470
/A ________01000000-00007ffffffff000    128.0T:sz                    'proc:2470'
/R ________01000000-00007ffffffff000    128.0T:sz                    'root'
...
# 'R' 区块是动态链接库，r-x 部分段表示是 .text， r-- 段表示是
# .rodata，而 rw- 段表示 .data + .bss。
R  00000187bc867000-00000187bc881000      104k:sz                    'useralloc'
 M 00000187bc867000-00000187bc87d000 r-x   88k:sz   0B:res  2535:vmo 'libfdio.so'
 M 00000187bc87e000-00000187bc87f000 r--    4k:sz   4k:res  2537:vmo 'libfdio.so'
 M 00000187bc87f000-00000187bc881000 rw-    8k:sz   8k:res  2537:vmo 'libfdio.so'
...
# 这个 2MB 的匿名映射可能是堆的一部分。
M  0000246812b91000-0000246812d91000 rw-    2M:sz  76k:res  2542:vmo 'mmap-anonymous'
...
# 这块看起来像栈：一大块虚拟地址空间(:sz)，但是只有非常少的映射（以及 4K 的防护页）
# 和少量的真实提存提交(:res)。
R  0000358923d92000-0000358923dd3000      260k:sz                    'useralloc'
 M 0000358923d93000-0000358923dd3000 rw-  256k:sz  16k:res  2538:vmo ''
...
# 初始线程的栈，它以一种不同的方式分配
M  0000400cbba84000-0000400cbbac4000 rw-  256k:sz   4k:res  2513:vmo 'initial-stack'
...
# vDSO，它有一个 .text 段和一个 .rodata 段
R  000047e1ab874000-000047e1ab87b000       28k:sz                    'useralloc'
 M 000047e1ab874000-000047e1ab87a000 r--   24k:sz  24k:res  1031:vmo 'vdso/full'
 M 000047e1ab87a000-000047e1ab87b000 r-x    4k:sz   4k:res  1031:vmo 'vdso/full'
...
# 进程的主二进制程序。
R  000059f5c7068000-000059f5c708d000      148k:sz                    'useralloc'
 M 000059f5c7068000-000059f5c7088000 r-x  128k:sz   0B:res  2476:vmo '/boot/bin/sh'
 M 000059f5c7089000-000059f5c708b000 r--    8k:sz   8k:res  2517:vmo '/boot/bin/sh'
 M 000059f5c708b000-000059f5c708d000 rw-    8k:sz   8k:res  2517:vmo '/boot/bin/sh'
...
```

<!--
### Dump all VMOs associated with a process

```
vmos <pid>
```

This will also show unmapped VMOs, which neither `ps` nor `vmaps` currently
account for.

It also shows whether a given VMO is a clone, along with its parent's koid.

```
$ vmos 1118
rights  koid parent #chld #map #shr    size   alloc name
rwxmdt  1170      -     0    1    1      4k      4k stack: msg of 0x5a
r-xmdt  1031      -     2   28   14     28k     28k vdso/full
     -  1298      -     0    1    1      2M     68k jemalloc-heap
     -  1381      -     0    3    1    516k      8k self-dump-thread:0x12afe79c8b38
     -  1233   1232     1    1    1   33.6k      4k libbacktrace.so
     -  1237   1233     0    1    1      4k      4k data:libbacktrace.so
...
     -  1153   1146     1    1    1  883.2k     12k ld.so.1
     -  1158   1153     0    1    1     16k     12k data:ld.so.1
     -  1159      -     0    1    1     12k     12k bss:ld.so.1
rights  koid parent #chld #map #shr    size   alloc name
```

Columns:

-   `rights`: If the process points to the VMO via a handle, this column shows
    the rights that the handle has, zero or more of:
    -   `r`: `ZX_RIGHT_READ`
    -   `w`: `ZX_RIGHT_WRITE`
    -   `x`: `ZX_RIGHT_EXECUTE`
    -   `m`: `ZX_RIGHT_MAP`
    -   `d`: `ZX_RIGHT_DUPLICATE`
    -   `t`: `ZX_RIGHT_TRANSFER`
    -   **NOTE**: Non-handle entries will have a single '-' in this column.
-   `koid`: The koid of the VMO, if it has one. Zero otherwise. A VMO without a
    koid was created by the kernel, and has never had a userspace handle.
-   `parent`: The koid of the VMO's parent, if it's a clone.
-   `#chld`: The number of active clones (children) of the VMO.
-   `#map`: The number of times the VMO is currently mapped into VMARs.
-   `#shr`: The number of processes that map (share) the VMO.
-   `size`: The VMO's current size, in bytes.
-   `alloc`: The amount of physical memory allocated to the VMO, in bytes.
    -   **NOTE**: If this column contains the value `phys`, it means that the
        VMO points to a raw physical address range like a memory-mapped device.
        `phys` VMOs do not consume RAM.
-   `name`: The name of the VMO, or `-` if its name is empty.

To relate this back to `ps`: each VMO contributes, for its mapped portions
(since not all or any of a VMO's pages may be mapped):

```
PRIVATE =  #shr == 1 ? alloc : 0
SHARED  =  #shr  > 1 ? alloc : 0
PSS     =  PRIVATE + (SHARED / #shr)
```
-->

### 转储进程所有 VMO

```
vmos <pid>
```

这个命令会显示没有映射的 VMO，它们是 `ps` 命令和 `vmaps` 命令都没有计算的内存。

它也会显示当前的 VMO 是否是克隆而来，仅显示父 VMO 的 koid，

```
$ vmos 1118
rights  koid parent #chld #map #shr    size   alloc name
rwxmdt  1170      -     0    1    1      4k      4k stack: msg of 0x5a
r-xmdt  1031      -     2   28   14     28k     28k vdso/full
     -  1298      -     0    1    1      2M     68k jemalloc-heap
     -  1381      -     0    3    1    516k      8k self-dump-thread:0x12afe79c8b38
     -  1233   1232     1    1    1   33.6k      4k libbacktrace.so
     -  1237   1233     0    1    1      4k      4k data:libbacktrace.so
...
     -  1153   1146     1    1    1  883.2k     12k ld.so.1
     -  1158   1153     0    1    1     16k     12k data:ld.so.1
     -  1159      -     0    1    1     12k     12k bss:ld.so.1
rights  koid parent #chld #map #shr    size   alloc name
```

列：

-   `rights`: 如果进程通过句柄指向VMO，这一列显示句柄权限，0或者如下值：
    -   `r`: `ZX_RIGHT_READ`
    -   `w`: `ZX_RIGHT_WRITE`
    -   `x`: `ZX_RIGHT_EXECUTE`
    -   `m`: `ZX_RIGHT_MAP`
    -   `d`: `ZX_RIGHT_DUPLICATE`
    -   `t`: `ZX_RIGHT_TRANSFER`
    -   **注**: 非句柄条目会在该列只显示一个 '-'。
-   `koid`: VMO 的 koid，如果VMO有的话则显示，否则显示0。没有 koid 的 VMO 是由内核创建，不会有用户空间的句柄。
-   `parent`: 如果VMO是克隆而来，则显示父 VMO 的 koid。
-   `#chld`: VMO的活动克隆（子 VMO）数量。
-   `#map`: VMO 当前映射进 VMAR 的次数。
-   `#shr`: 映射（共享）VMO的进程的数量。
-   `size`: VMO 当前的大小，字节单位。
-   `alloc`: 分配给 VMO 的物理内存数量，字节单位。
    -   **注**: 如果这行包含值`phys`，它意思是这块 VMO 指向一个原始物理地址区块，类似内存映射设备。
        `phys` VMO 不消耗 RAM。
-   `name`: VMO 的名字，如果名字为空，则显示`-`。


<!--
### Dump "hidden" (unmapped and kernel) VMOs

> NOTE: This is a kernel command, and will print to the kernel console.

```
k zx vmos hidden
```

Similar to `vmos <pid>`, but dumps all VMOs in the system that are not mapped
into any process:
-   VMOs that userspace has handles to but does not map
-   VMOs that are mapped only into kernel space
-   Kernel-only, unmapped VMOs that have no handles

A `koid` value of zero means that only the kernel has a reference to that VMO.

A `#map` value of zero means that the VMO is not mapped into any address space.

**See also**: `k zx vmos all`, which dumps all VMOs in the system. **NOTE**:
It's very common for this output to be truncated because of kernel console
buffer limitations, so it's often better to combine the `k zx vmos hidden`
output with a `vmaps` dump of each user process.
-->

### 转储“隐藏”（未映射和内核）VMO

> 注：这是一个内核命令，它会将结果打印到内核控制台。

```
k zx vmos hidden
```

类似与`vmos <pid>`，但是只打印系统中所有没有映射进任何进程的 VMO：

- 有用户空间句柄指向，但是没有映射的 VMO。
- 只映射进内核空间的 VMO。
- 只是内核中，没有句柄也没有映射的 VMO。

`koid` 值为0意味着只有内核对这块 VMO 有引用。`#map` 值为0意味着 VMO 没有映射进任何地址空间。

**参考**: `k zx vmos all`，这个命令会转储系统中的所有 VMO。**注**: 由于内核控制台缓存限制原因，这个命令的输出很容易被截断，所以最好的方式是结合 `k zx vmos hidden` 和  `vmaps` 两个命令关于每一个用户进程的转储结果。

<!--
### Limitations

Neither `ps` nor `vmaps` currently account for:

-   VMOs or VMO subranges that are not mapped. E.g., you could create a VMO,
    write 1G of data into it, and it won't show up here.

None of the process-dumping tools account for:

-   Multiply-mapped pages. If you create multiple mappings using the same range
    of a VMO, any committed pages of the VMO will be counted as many times as
    those pages are mapped. This could be inside the same process, or could be
    between processes if those processes share a VMO.

    Note that "multiply-mapped pages" includes copy-on-write.
-   Underlying kernel memory overhead for resources allocated by a process.
    E.g., a process could have a million handles open, and those handles consume
    kernel memory.

    You can look at process handle consumption with the `k zx ps` command; run
    `k zx ps help` for a description of its columns.
-   Copy-on-write (COW) cloned VMOs. The clean (non-dirty, non-copied) pages of
    a clone will not count towards "shared" for a process that maps the clone,
    and those same pages may mistakenly count towards "private" of a process
    that maps the parent (cloned) VMO.

    TODO(dbort): Fix this; the tools were written before COW clones existed.
-->

### 限制

无论 `ps` 或 `vmaps` 当前都没有计算如下内容：

-   VMOs 或 VMO 子区块没有映射。即，你可以创建一个 VMO，向其中写入 1G 的数据，但是它并不会在这里显示。

没有进程转储工具考虑如下情况：

-   多映射页。如果你使用一个 VMO 的同一块区域创建多个映射，VMO 的任何提交页面都会按照它映射次数被计数。这会在同一个进程中发生，如果几个进程共享 VMO，也会出现在进程间。

    注意“多映射页面”包含了写时复制。
-   进程为资源分配的潜在的内核内存消耗。即，进程可以打开几百万的句柄，这些句柄会消耗内核内存。

    使用命令 `k zx ps` 可以查看进程句柄消耗内存，执行 `k zx ps help` 查看输出内容列说明。
-   写时拷贝（COW）克隆 VMOs。克隆的干净的页面（非脏页面，非拷贝页面）不会向映射这份克隆的进程针对“共享”进行计数。这些相同的页面或许会对映射了父（克隆）VMO的私有页面计数产生错误影响。

    TODO（dbort）：修复这个问题。这个工具是在 COW 克隆出现之前编写。

<!--
## Kernel memory

### Dump system memory arenas and kernel heap usage

Running `kstats -m` will continuously dump information about physical memory
usage and availability.

```
$ kstats -m
--- 2017-06-07T05:51:08.021Z ---
     total       free       VMOs      kheap      kfree      wired        mmu
   2046.9M    1943.8M      20.7M       1.1M       0.9M      72.6M       7.8M

--- 2017-06-07T05:51:09.021Z ---
...
```

Fields:

-   `2017-06-07T05:51:08.021Z`: Timestamp of when the stats were collected, as
    an ISO 8601 string.
-   `total`: The total amount of physical memory available to the system.
-   `free`: The amount of unallocated memory.
-   `VMOs`: The amount of memory committed to VMOs, both kernel and user. A
    superset of all userspace memory. Does not include certain VMOs that fall
    under `wired`.
-   `kheap`: The amount of kernel heap memory marked as allocated.
-   `kfree`: The amount of kernel heap memory marked as free.
-   `wired`: The amount of memory reserved by and mapped into the kernel for
    reasons not covered by other fields in this struct. Typically for readonly
    data like the ram disk and kernel image, and for early-boot dynamic memory.
-   `mmu`: The amount of memory used for architecture-specific MMU metadata like
    page tables.

-->

## 内核内存

### 转储系统内存区块和内核堆使用

运行 `kstats -m` 命令会持续转储物理内存使用情况以及物理内存可用量的信息。

```
$ kstats -m
--- 2017-06-07T05:51:08.021Z ---
     total       free       VMOs      kheap      kfree      wired        mmu
   2046.9M    1943.8M      20.7M       1.1M       0.9M      72.6M       7.8M

--- 2017-06-07T05:51:09.021Z ---
...
```

字段含义：

-   `2017-06-07T05:51:08.021Z`: 以 ISO 8601 字符串形式显示状态收集的时间戳。
-   `total`: 系统可用的物理内存总量。
-   `free`: 未分配内存的数量。
-   `VMOs`: 提交到 VMO 的内存数量，包括内核和用户。它是所有用户空间内存的超集。但是不包括某些落入 `wired`的 VMO。
-   `kheap`: 内核分配的堆内存数量。
-   `kfree`: 标记为空闲的内核堆内存数量。
-   `wired`: 保留的内存数量，以及在这个结构中其他字段没有概括的映射进内核的内存。典型的，比如 RAM 磁盘和内核映像，早期的动态内存等。
-   `mmu`: 架构特定MMU元数据，比如页表等使用的内存量。

<!--
### Dump the kernel address space

> NOTE: This is a kernel command, and will print to the kernel console.

```
k zx asd kernel
```

Dumps the kernel's VMAR/mapping/VMO hierarchy, similar to the `vmaps` tool for
user processes.

```
$ k zx asd kernel
as 0xffffffff80252b20 [0xffffff8000000000 0xffffffffffffffff] sz 0x8000000000 fl 0x1 ref 71 'kernel'
  vmar 0xffffffff802529a0 [0xffffff8000000000 0xffffffffffffffff] sz 0x8000000000 ref 1 'root'
    map 0xffffff80015f89a0 [0xffffff8000000000 0xffffff8fffffffff] sz 0x1000000000 mmufl 0x18 vmo 0xffffff80015f8890/k0 off 0 p ages 0 ref 1 ''
      vmo 0xffffff80015f8890/k0 size 0 pages 0 ref 1 parent k0
    map 0xffffff80015f8b30 [0xffffff9000000000 0xffffff9000000fff] sz 0x1000 mmufl 0x18 vmo 0xffffff80015f8a40/k0 off 0 pages 0 ref 1 ''
      object 0xffffff80015f8a40 base 0x7ffe2000 size 0x1000 ref 1
    map 0xffffff80015f8cc0 [0xffffff9000001000 0xffffff9000001fff] sz 0x1000 mmufl 0x1a vmo 0xffffff80015f8bd0/k0 off 0 pages 0 ref 1 ''
      object 0xffffff80015f8bd0 base 0xfed00000 size 0x1000 ref 1
...
```
-->

### 转储内核地址空间

> 注： 这是一个内核命令，只会在内核控制台输出内容。

```
k zx asd kernel
```

转储内核的 `VMAR/mapping/VMO` 结构，类似于用户空间的 `vmaps` 工具。

```
$ k zx asd kernel
as 0xffffffff80252b20 [0xffffff8000000000 0xffffffffffffffff] sz 0x8000000000 fl 0x1 ref 71 'kernel'
  vmar 0xffffffff802529a0 [0xffffff8000000000 0xffffffffffffffff] sz 0x8000000000 ref 1 'root'
    map 0xffffff80015f89a0 [0xffffff8000000000 0xffffff8fffffffff] sz 0x1000000000 mmufl 0x18 vmo 0xffffff80015f8890/k0 off 0 p ages 0 ref 1 ''
      vmo 0xffffff80015f8890/k0 size 0 pages 0 ref 1 parent k0
    map 0xffffff80015f8b30 [0xffffff9000000000 0xffffff9000000fff] sz 0x1000 mmufl 0x18 vmo 0xffffff80015f8a40/k0 off 0 pages 0 ref 1 ''
      object 0xffffff80015f8a40 base 0x7ffe2000 size 0x1000 ref 1
    map 0xffffff80015f8cc0 [0xffffff9000001000 0xffffff9000001fff] sz 0x1000 mmufl 0x1a vmo 0xffffff80015f8bd0/k0 off 0 pages 0 ref 1 ''
      object 0xffffff80015f8bd0 base 0xfed00000 size 0x1000 ref 1
...
```


