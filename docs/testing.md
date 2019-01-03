<!--
# Testing

The test harness which runs on our bots (called "runtests") picks up all
executables in the "/boot/test" and "/system/test" directories and runs them.
If you provide a command-line argument, such as `runtests -S -m widget_test`,
runtests will only run the single test requested -- in this case, `widget_test`.

"runtests" takes command-line arguments to toggle classes of tests to execute.

These classes are the following:

* **Small**: Isolated tests for functions and classes. These must be totally
  synchronous and single-threaded. These tests should be parallelizable; there
  shouldn't be any shared resources between them.
* **Medium**: Single-process integration tests. Ideally these are also synchronous
  and single-threaded but they might run through a large chunk of code in each
  test case, or they might use disk, making them a bit slower.
* **Large**: Slow, multi-process, or particularly incomprehensible single-process
  integration tests. These tests are often too slow / flaky to run in a CQ, and
  we should try to limit how many we have.
* **Performance**: Tests which are expected to pass, but which are measured
  using other metrics (thresholds, statistical techniques) to identify
  regressions.
-->

# 测试

执行在我们项目上的测试工具（被称为“runtests”）会选取 "/boot/test" 和 "/system/test" 目录下的所有二进制可执行文件，并且运行它们。如果提供了命令行参数，例如 `runtests -S -m widget_test`，`runtests` 会只执行要求的单个测试，这个例子中是 `widget_test`。

"runtests" 使用命令行参数来确定要执行的测试类别。

测试类别哦有如下几种：

* **小型**: 用于函数和类的单独测试。这些测试必须是完全同步和单线程。这些测试需要能够并行化；所以在这些测试之间不应该有任何的资源共享。
* **中型**: 单进程整体测试。理想情况下，这些测试也应该是同步且单线程的，但是他们可能会在每次测试中执行非常大一块代码，或它们可能使用磁盘，这使得测试程序运行缓慢。
* **大型**: 运行缓慢，多进程或特别情况下不万千是单进程集成测试。这些测试通常在一个 CQ 中运行很慢或很耗资源。因此需要尝试限制这个测试。
* **性能**: 这些测试都是想要通过的测试，但是它们是使用其他的指标衡量（吞吐量，统计学技术）来进行回归分析。

<!--
Since runtests doesn't really know what "class" is executing when it launches a
test, it encodes this information in the environment variable
`RUNTESTS_TEST_CLASS`, which is detailed in [the unittest
header][unittest-header] , and lets the executable itself decide what to run /
not run. This environment variable is a bitmask indicating which tests to run.

For example, if a a test executable is run with "small" and "medium" tests,
it will be executed ONCE with `RUNTESTS_TEST_CLASS` set to 00000003 (the
hex bitwise OR of "TEST_SMALL" and "TEST_MEDIUM" -- though this information
should be parsed using the [unittest header][unittest-header], as it may be
updated in the future).
-->

因为 runtests 在启动一个测试时并不确切知道什么类别在执行，它将这个信息编码进了环境变量 `RUNTESTS_TEST_CLASS` 中了，详细格式是`[the unittest header][unittest-header]`；这就可以让执行程序自己决定要执行那些程序，不执行那些程序。这个环境变量是一个位掩码，用于标识那个测试要运行。

例如，如果测试可执行程序用`small` 和 `medium` 测试，它会将`RUNTESTS_TEST_CLASS` 设置为`00000003`执行一次， 其中 `00000003` 十六进制位是"TEST_SMALL" 和 "TEST_MEDIUM"的或运算 —— 这个信息应该使用 [unittest header][unittest-header] 进行解析，因为它以后可能会被更新。

<!--
## Zircon Tests (ulib/test, and/or using ulib/unittest)

The following macros can be used to filter tests into these categories:
```
RUN_TEST_SMALL(widget_tiny_test)
RUN_TEST_MEDIUM(widget_test)
RUN_TEST_LARGE(widget_big_test)
RUN_TEST_PERFORMANCE(widget_benchmark)
```

The legacy `RUN_TEST(widget_test)` is aliased to mean the same thing as
`RUN_TEST_SMALL`.

## Zircon Kernel Tests (kernel/lib/unittest)

For tests compiled into the kernel itself you can call them from the shell with
`k ut`. `k ut all` will run all tests or you can use `k ut $TEST_NAME` to run a
specific test.

The output from these tests will only be shown on the serial console.
-->

## Zircon测试（ulib/test, and/or using ulib/unittest）

如下的宏可以用于将测试分为如下的种类：

```
RUN_TEST_SMALL(widget_tiny_test)
RUN_TEST_MEDIUM(widget_test)
RUN_TEST_LARGE(widget_big_test)
RUN_TEST_PERFORMANCE(widget_benchmark)
```

过时的宏定义 `RUN_TEST(widget_test)` 是 `RUN_TEST_SMALL` 的别名。

## Zircon内核测试（kernel/lib/unittest）

对于编译进内核本身的测试，可以从Shell中使用 `k ut` 命令调用它们。`k ut all`命令会执行所有的测试，或者可以使用 `k ut $TEST_NAME` 来执行特定测试。

这些测试的输出只会出现在串口控制台中。

<!--
## Fuchsia Tests (not using ulib/unittest)

The environment variable `RUNTESTS_TEST_CLASS` will still be available to all
executables launched by runtests. The [unittest header][unittest-header] can be
used to parse different categories of tests which the runtests harness attempted
to run.

## Runtests CLI

By default, runtests will run both small and medium tests.

To determine how to run a custom set of test categories, run `runtests -h`,
which includes usage information.

[unittest-header]: ../system/ulib/unittest/include/unittest/unittest.h "Unittest Header"
-->

## Fuchsia测试（不要使用 ulib/unittest）

环境变量 `RUNTESTS_TEST_CLASS` 对所有 runtests 启动的二进制程序都可用。[unittest header][unittest-header] 可以用于解析 runtests 尝试去支持的测试的不同类别。

## Runtests CLI

默认情况下，runtest 会执行小型和中型的测试。

为了确定如何运行自定义的测试种类，执行 `runtests -h` 显示使用信息。

[unittest-header]: ../system/ulib/unittest/include/unittest/unittest.h "Unittest Header"