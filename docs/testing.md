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
* **中型**: Single-process integration tests. Ideally these are also synchronous
  and single-threaded but they might run through a large chunk of code in each
  test case, or they might use disk, making them a bit slower.
* **大型**: Slow, multi-process, or particularly incomprehensible single-process
  integration tests. These tests are often too slow / flaky to run in a CQ, and
  we should try to limit how many we have.
* **性能**: Tests which are expected to pass, but which are measured
  using other metrics (thresholds, statistical techniques) to identify
  regressions.


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
