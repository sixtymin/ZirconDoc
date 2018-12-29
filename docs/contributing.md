<!--
# Contributing Patches to Zircon

At this point in time, Zircon is under heavy, active development, and we're
not seeking major changes or new features from new contributors, however, if
you desire to [contribute](https://fuchsia.googlesource.com/docs/+/master/CONTRIBUTING.md), small bugfixes are welcome.

Here are some general guidelines for patches to Zircon.  This list is
incomplete and will be expanded over time.
-->

# Zircon 贡献补丁

在这个时间点上，Zircon正处于繁重且积极地开发状态，我们并不寻找新的贡献者的重大变化或新的功能，但是，如果你非常想要 [贡献](https://fuchsia.googlesource.com/docs/+/master/CONTRIBUTING.md)，bug 修复还是非常欢迎的。

如下有一些给 Zircon 编写补丁的通用准则。这个列表还没有完成，随着时间推移会发生变化。

[TOC]

<!--
## Process

*   GitHub pull requests are not accepted. Patches are handled via Gerrit Code
    Review at: https://fuchsia-review.googlesource.com/#/q/project:zircon

*   The #fuchsia channel on the freenode irc network is a good place to ask
    questions.

*   Include [tags] in the commit subject flagging which module, library, app,
    etc, is affected by the change. The style here is somewhat informal. Look at
    past changes to get a feel for how these are used. Gerrit will flag your
    change with `Needs Label: Commit-Message-has-tags` if these are missing.

*   Include a line like `Test: <how this was tested>` in the commit message, or
    else Gerrit will flag your change with `Needs Label:
    Commit-Message-has-TEST-line`.

    *   The full (Java-style) regex is `(?i:test|tests|tested|testing)[
        \t]*[=:]`, so lines like `TESTED=asdf` or `Testing : 1234` are also
        valid, along with lines that only contain `Test:` with the details on
        the following lines.

*   [Googlers only] Commit messages may reference issue IDs, which will be
    turned into links in the Gerrit UI. Issues may also be automatically closed
    using the syntax `BUG-123 #done`. *Note*: Zircon's issue tracker is not open
    to external contributors at this time.

*   Zircon should be buildable for all major targets (x86-64, arm64) at every
    change. ./scripts/build-all-zircon can help with this.

*   Avoid breaking the unit tests. Boot Zircon and run "runtests" to verify that
    they're all passing.

*   Avoid whitespace or style changes. Especially do not mix style changes with
    patches that do other things as the style changes are a distraction.

*   Avoid changes that touch multiple modules at once if possible. Most changes
    should be to a single library, driver, app, etc.
-->

## 流程

*   GitHub 不接收合并请求，补丁需要通过 `Gerrit Code Review` 处理，地址为: https://fuchsia-review.googlesource.com/#/q/project:zircon

*   在自由节点 IRC 网络上的 `#fuchsia` 频道是问问题的好地方。

*   在提交的主题中要添加修改影响到的 `[tags]`，使用模块，库，APP等标记。这种风格某种程度上来说是不正式的，可以参考过去的修改来感觉如何使用。如果没有添加标记，Gerrit 会使用 `Needs Label: Commit-Message-has-tags` 来标注你的修改。

*   在提交信息中包含类似 `Test: <how this was tested>` 这样的信息，否则 Gerrit 会使用 `Needs Label:Commit-Message-has-TEST-line` 标记你的修改。

    *   完整的(Java风格)正则表达式是 `(?i:test|tests|tested|testing)[\t]*[=:]`，因此添加内容类似 `TESTED=asdf` 或 `Testing : 1234` 也是有效的，单独行包含 `Test:`，在接下来新的行里面列举详细信息。

*   [仅适用于Googlers] 提交信息要指出问题的ID，它会在 Gerrit 界面上变成链接。问题也会使用语法`BUG-123 #done` 自动关闭。 *注*: Zircon 的问题追踪者并没有开放给外部贡献者。

*   在每一次修改时，Zircon 应该对所有目标（x86-64， arm64）都可编译。可以使用 `./scripts/build-all-zircon`来帮助验证。

*   避免破坏单元测试，引导 Zircon，然后运行"runtests"来验证修改通过了所有的单元测试。

*   避免空白行或风格变化，特别是不要在补丁中混合使用各种风格，风格变化很容易导致混乱。

*   尽量避免一次修改触及多个模块。大部分的修改应该只怎对单个库，驱动，app等。

<!--
## Style

* Code style mostly follows the [Google C++ Style Guide][google-style-guide], except:

    - When editing existing code, match local style.  This supersedes the other style rules.

    - Maximum line width is 100 characters rather than 80.

    - Indentation is four spaces, not two.  Still never use tabs.  Do not leave trailing whitespace
      on lines.  Gerrit will flag bad whitespace usage with a red background in diffs.

    - Zircon declares pointers as `char* p` and **not** as `char *p;`.  The Google style guide is
      ambivalent about this; Zircon standardized on asterisk-with-type instead of
      asterisk-with-name.

    - Inside a `switch` statement, the `case` labels are not indented.  Example:

          void foo(int which) {
              switch (which) {
              case 0:
                  foo_zero();
                  break;
              case 17:
                  foo_seventeen();
                  break;
              default:
                  panic("I don't know how to foo here (which = %d)\n", which);
              }
          }

    - Put curly braces around the body of any loop and in any `if` statement where the body is on
      another line (including any `if` statement that has an `else if` or `else` part). Trivial `if`
      statements can be written on one line, like this:

          if (result != ZX_OK) return result;

       However, do **not** write:

          if (result == ZX_OK)
              return do_more_stuff(data);
          else
              return result;

       and do **not** write:

          while (result == ZX_OK)
              result = do_another_step(data);

      Note that this rule isn't enforced by `clang-fmt`, so please pay attention to it when writing
      code and reviewing commits.

* You can run `./scripts/clang-fmt` to reformat files.  Pass in the filenames as arguments, e.g.

      scripts/clang-fmt kernel/top/main.c kernel/top/init.c

  The `clang-fmt` script will automatically download and run the `clang-format` binary.  This fixes
  most style problems, except for line length (since `clang-format` is too aggressive about
  re-wrapping modified lines).
-->

## 代码风格

* 代码风格大致上遵循了 [Google C++风格指南][google-style-guide]，但是有如下例外：

    - 当编辑现有代码时，符合本地代码风格。这要取代其他的风格规则。

    - 最大的行宽是100个字符而非80。

    - 缩进是四个空格，而非两个。仍然是不要使用 Tab，行结尾处也不要留下空白字符。 Gerrit 会在`diffs`中使用红线标记出不符合风格的空白字符使用。

    - Zircon 声明指针使用`char* p`**而非**`char *p;`。 这与Google的风格指南是矛盾的，Zircon 依据标准的`asterisk-with-type`而非`asterisk-with-name`。

    - 在 `switch` 语句中，`case` 标记不使用缩进，例如:

          void foo(int which) {
              switch (which) {
              case 0:
                  foo_zero();
                  break;
              case 17:
                  foo_seventeen();
                  break;
              default:
                  panic("I don't know how to foo here (which = %d)\n", which);
              }
          }

    - 在任何循环和`if`语句中，如果语句体位于不同行，则都应该使用花括号括住。（包括任何的包含了`else if` 或 `else` 部分的`if`语句). 以`if`为例，如下形式代码可以写在同一行上：

          if (result != ZX_OK) return result;

       但是，**不要**使用如下写法：

          if (result == ZX_OK)
              return do_more_stuff(data);
          else
              return result;

       也**不要**写这样的语句：

          while (result == ZX_OK)
              result = do_another_step(data);

      注意，这个规则并不是 `clang-fmt` 强制的，因此请在写代码和review提交时注意。

* 你可以运行`./scripts/clang-fmt`脚本来重新格式化文件，按照如下的形式传递文件名作为参数：

      scripts/clang-fmt kernel/top/main.c kernel/top/init.c

  `clang-fmt` 脚本会自动下载和运行 `clang-format` 二进制文件。这可以修复大部分的风格问题，除了行长度问题（因为 `clang-format` 关于重包装修改行太过于激进。

<!--
## Documentation

* Documentation is one honking great idea &mdash; let's do more of that!

    - Documentation should be in Markdown files.  Our Markdown is rendered both in Gitiles in
      [the main repo browser][googlesource-docs] and in Github in [the mirrored repo][github-docs].
      Please check how your docs are rendered on both websites.

    - Some notable docs: there's a list of syscalls in [docs/syscalls.md][syscall-doc] and a list of
      kernel cmdline options in [docs/kernel_cmdline.md][cmdline-doc].  When editing or adding
      syscalls or cmdlines, update the docs!

    - [The `h2md` tool][h2md-doc] can scrape source files and extract embedded Markdown.  It's
      currently used to generate API docs for DDK.  `./scripts/make-markdown` runs `h2md` against
      all source files in the `system/` tree.
-->

## 文档

* 文档是一种让好想法多面发声的方法，我们要多做这个工作!

    - 文档应该是 Markdown 文件。我们的 Markdown 在 Gitiles 中的 [主库浏览器][googlesource-docs]中的和 Github 中的[映射库][github-docs]都可以渲染。请检查你的文档在两个网站上都可以渲染显示。

    - 一些重要的文档：[docs/syscalls.md][syscall-doc]中的系统调用的列表，[docs/kernel_cmdline.md][cmdline-doc] 中的命令行选项。当编辑或增加系统调用或命令行时，要更新这些文档。

    - [`h2md`工具][h2md-doc] 可以搜索源码，提取出嵌入的 Markdown。它现在用于生成 DDK 的 API 文档。`./scripts/make-markdown` 针对 `system/` 源码树中的所有源码文件运行 `h2md`。

[google-style-guide]: https://google.github.io/styleguide/cppguide.html
[googlesource-docs]: https://fuchsia.googlesource.com/zircon/+/master/docs/
[github-docs]: https://github.com/fuchsia-mirror/zircon/tree/master/docs
[syscall-doc]: https://fuchsia.googlesource.com/zircon/+/master/docs/syscalls.md
[cmdline-doc]: https://fuchsia.googlesource.com/zircon/+/master/docs/kernel_cmdline.md
[h2md-doc]: https://fuchsia.googlesource.com/zircon/+/master/docs/h2md.md
