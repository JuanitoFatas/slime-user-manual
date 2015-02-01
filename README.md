# SLIME 使用手冊（帝归翻译）

## 目录

* [1. 简介](#-1)

* [2. 开始](#-2)
    * [2.1 支持平台](#21-)
    * [2.2 下载 Slime](#22--slime)
        * [2.2.1 从 CVS 下载](#221--cvs-)
        * [2.2.2 使用 CVS](#222--cvs)
    * [2.3 安装](#23-)
    * [2.4 启动 Slime](#24--slime)
    * [2.5 调整设置](#25-)
        * [2.5.1 自动加载](#251-)
        * [2.5.2 多种 Lisp](#252--lisp)
        * [2.5.3 更快地加载 Swank](#253--swank)

* [3. 使用 Slime 模式](#-slime-)
    * [3.1 用户介面须知](#31-)
        * [3.1.1 临时缓冲区](#311-)
        * [3.1.2 `inferior-lisp`缓冲区](#312-inferior-lisp)
        * [3.1.3 多线程](#313-)
        * [3.1.4 键绑定](#314-)
    * [3.2 求值命令](#32-)
    * [3.3 编译命令](#33-)
    * [3.4 补全命令](#34-)
    * [3.5 查找定义（“Meta-Point”命令）](#35-meta-point)
    * [3.6 文档命令](#36-)
    * [3.7 交叉引用命令](#37-)
        * [3.7.1 XREF 缓冲区命令](#371-xref)
    * [3.8 宏扩展命令](#38-)
    * [3.9 分解命令](#39-)
    * [3.10 中止 / 恢复命令](#310-)
    * [3.11 检查命令](#311-)
    * [3.12 分析命令](#312-)
    * [3.13 遮盖命令](#313--1)
    * [3.14 语义缩进](#314--1)
    * [3.15 根据读取器的结果字符化](#315-)

* [4. SLDB：Slime 调试器](#sldbslime)
    * [4.1 检查窗口](#41-)
    * [4.2 重启](#42-)
    * [4.3 在不同的窗口间操作](#43-)
    * [4.4 单步调试](#44-)
    * [4.5 其它命令](#45-)

* [5. 杂项](#-3)
    * [5.1 slime-selector](#51-slime-selector)
    * [5.2 slime-macroexpansion-minor-mode](#52-slime-macroexpansion-minor-mode)
    * [5.3 多重连接](#53-)

* [6. 定制](#-4)
    * [6.1 Emacs 端定制](#61-emacs)
        * [6.1.1 钩子](#611-)
    * [6.2 Lisp 端（Swank）](#62-lispswank)
        * [6.2.1 通信模式](#621-)
        * [6.2.2 其它配置](#622-)

* [7. 小技巧](#-5)
    * [7.1 连接到远程 Lisp](#71-lisp)
        * [7.1.1 设置 Lisp 镜像](#711-lisp)
        * [7.1.2 设置 Emacs](#712-emacs)
        * [7.1.3 设置路径名翻译](#713-)
    * [7.2 重定向全局 IO 到 REPL](#72-iorepl)
    * [7.3 自动连接到 Slime](#73-slime)

* [8. 扩展包](#-6)
    * [8.1 加载扩展包](#81)
    * [8.2 REPL：“顶层环境”](#82-repl)
        * [8.2.1 REPL 命令](#821-repl)
        * [8.2.2 输入引导](#822-)
        * [8.2.3 快捷命令](#823-)
    * [8.3 多 REPL](#83-repl)
    * [8.4 inferior-slime-mode](#84-inferior-slime-mode)
    * [8.5 混合补全](#85-)
    * [8.6 模糊补全](#86-)
    * [8.7 slime-autodoc-mode](#87-slime-autodoc-mode)
    * [8.8 ASDF](#88-asdf)
    * [8.9 导航条](#89-)
    * [8.10 编辑命令](#810-)
    * [8.11 更好的检查器](#811-)
    * [8.12 对象描述](#812-)
    * [8.13 打印窗口](#813-)
    * [8.14 TRAMP](#814-tramp)
    * [8.15 文档链接](#815-)
    * [8.16 交叉引用和类查看器](#816-)
    * [8.17 高亮编辑](#817-)
    * [8.18 空白缓冲区](#818-)
    * [8.19 slime-sprof](#819-slime-sprof)
    * [8.20 元包 slime-fancy](#820--slime-fancy)

* [9. 致谢](#-8)
    * [黑客们](#-9)
    * [多谢！](#-10)


## 一、简介

Slime 的意思是“Emacs 下优秀的 Lisp 交互式开发模式”。

通过支持 Common Lisp 的交互式编程，Slime 扩展了 Emacs。所以的特性都基于 slime-mode，一个 Emacs 的 minor-mode，它为标准的 lisp-mode 提供补充。lisp-mode 为编辑 Lisp 源文件提供支持，而 slime-mode 则提供了与一个 Lisp 进程进行交互的功能，包括编译、调试、文档查找等等。

slime-mode 开发环境效仿 Emacs 原生的 Emacs Lisp 环境。我们也从某些类似的系统（例如 ILISP）那里借鉴了一些，当然也包括我们自己的想法。

Slime 由两部分组成：用 Emacs Lisp 写的用户界面，和用 Common Lisp 写的服务器端。这两部分通过套接字连接在一起，并且使用一个类似于 RPC 的协议通信。

服务器端的 Lisp 主要是可移植的 Commom Lisp。所需要的跟特定 Lisp 实现相关的特性都由一个接口定义好，然后由不同的 Lisp 实现提供。这使得 Slime 非常容易移植。

## 二、开始

本章告诉你如何配置和启动 Slime。

### 2.1 支持平台

Slime 广泛地支持多种操作系统和 Lisp 实现。Slime 可以在类 Unix 系统、Mac OSX 和 Microsoft Windows 上运行。GNU Emacs 21、22 和 23 以及 XEmacs 21 都可以运行 Slime。

粗略地根据支持的良好程度来排序的话，所有支持的 Lisp 实现为：

- CMU Common Lisp（CMUCL），19d 版或更新
- Steel Bank Common Lisp（SBCL），1.0 版或更新
- Clozure Common Lisp（CCL），1.3 版或更新
- LipsWorks，4.3 版或更新
- Allegro Common Lisp（ACL），6 版或更新
- CLISP，2.35 版或更新
- Armed Bear Common Lisp（ABCL）
- Corman Common Lisp，2.51 版或更新，需要 http://www.grumblesmurf.org/lisp/corman-patches 的补丁
- Scieneer Common Lisp（SCL），1.2.7 版或更新
- Embedded Common Lisp（ECL）

绝大部分特性在不同实现上的表现都是一致的，但是有些可能会有所不同。这些包括放置编译信息的注释的精度、XREF 支持以及调试器命令（例如“重启缓冲区”）。

### 2.2 下载 Slime

你可以选择使用发行版本的 Slime 或者直接通过 CVS 仓库使用 Slime。你可以从我们的网站下载最新发布版本： http://www.common-lisp.net/project/slime/ 。

我们建议加入了 slime-dev 邮件列表的用户使用 CVS 版本的代码。

### 2.2.1 从 CVS 下载

可以从 common-lisp.net 的 CVS 仓库取得 Slime。你可以选择使用最新版本的代码或者是带有 FAIRLY-STABLE 标签的快照版本。

跟 FAIRLY-STABLE 版本的代码相比，最新版本的代码可能有更多的特性和更少的 BUG，但也有可能因为较大的改动而不稳定。根据经验法则，我们建议如果你加入了 slime-dev 邮件列表，你最好使用最新版本（当进行主要的变动时，我们会发出通告）。如果你没有加入邮件列表，你就无法得知最新版本代码的情况，所以使用 FAIRLY-STABLE 或者发布版本是一个安全的选择。

如果你从 CVS 迁出代码，记得经常更新。经常会有小的改进提交上去，而 FAIRLY-STABLE 标签也会随时推进。

### 2.2.2 使用 CVS

要下载 Slime 你要先配置你的 CVSROOT 并且登录到仓库。

```
export CVSROOT=:pserver:anonymous@common-lisp.net:/project/slime/cvsroot
cvs login
```

最新版的代码可以通过以下方式迁出：

```
cvs checkout slime
```

或者可以通过以下方式迁出 FAIRLY-STABLE 版本：

```
cvs checkout -rFAIRLY-STABLE slime
```

如果你想知道最新版本的代码跟你运行的版本有什么新的东西，你可以将本地的 ChangeLog 和版本仓库的进行对比：

```sh
cvs diff -rHEAD ChangeLog      # or: -rFAIRLY-STABLE
```

## 2.3 安装

如果你已经有了一个可以从命令行启动的 Lisp 实现，那么仅需在 .emacs 文件中添加几行即可安装成功：

```emacs-lisp
(setq inferior-lisp-program "/opt/sbcl/bin/sbcl") ; your Lisp system
(add-to-list 'load-path "~/hacking/lisp/slime/")  ; your SLIME directory
(require 'slime)
(slime-setup)
```

在 README 文件里也可以见到上面这些代码。你可以从那里复制粘贴，但要记得替换正确的路径。

这是没有其它杂项的最小化配置。如果基本配置可以工作，那么你可以试附加模块。（8.1 加载扩展包）

我们建议如果你要使用 Slime 就不要在 Emacs 里加载 ILISP 包。如果你这么做了，那么在编辑 Lisp 源文件时就会加入许多键绑定，而且这些键绑定可能会跟 Slime 启动的 Lisp 进程发生冲突而无法正常工作。

## 2.4 启动 Slime

Emacs 命令 M-x slime 可以启动 Slime。它使用 inferior-lisp 包来启动一个 Lisp 进程，加载并启动 Lisp 端服务器（叫做“Swank”），然后在 Emacs 和 Lisp 之间建立一个 socket 连接。最后会生成一个 REPL 缓冲区，你可以在这里输入 Lisp 表达式并求值。

于是现在 Slime 启动完成，你可以开始使用了。

## 2.5 调整设置

这一部分说明了如何减少 Slime 的启动时间和如何为多 Lisp 系统配置 Slime。

在进行本部分之前请确认你的基本配置已经可以工作。如果你对基本配置感到满意，那么请跳过这部分。

关于附加模块请看“8.1 加载扩展包”。

### 2.5.1 自动加载

基本设置始终会加载 Slime，即使你不使用它。如果你只在需要的时候才加载 Slime，那么 Emacs 会启动的快一点。要这样，你需要稍微更改你的 .emacs 文件：

```emacs-lisp
(setq inferior-lisp-program "the path to your Lisp system")
(add-to-list 'load-path "the path of your slime directory")
(require 'slime-autoloads)
(slime-setup)
```

跟基本配置相比，差别只在这一行 (require 'slime-autoloads)。它告诉 Emacs 当 M-x slime 或者 M-x slime-connect 命令第一次执行之后 Slime 的其它部分会被自动加载。

### 2.5.2 多种 Lisp

默认情况下，M-x slime 命令启动的程序是由 inferior-lisp-program 指定的。如果你在执行 M-x slime 命令时添加了一个前缀参数，Emacs 会启动参数中指定的程序。如果你需要经常使用它或者命令的名称太长，那么在 .emacs 文件里设置 slime-lisp-implementations 变量则较为方便。例如，在这里我们定义了两个程序：

```emacs-lisp
(setq slime-lisp-implementations
      '((cmucl ("cmucl" "-quiet"))
  (sbcl ("/opt/sbcl/bin/sbcl") :coding-system utf-8-unix)))
```

这个变量包含了一个 Lisp 程序的列表，如果你通过一个减号前缀参数启动 Slime，M-- M-x slime，你可以从这个列表里选择一个程序。当不加前缀地启动该命令，slime-default-lisp 变量里指定的程序或者是列表中的第一项会被使用。列表的元素应该像这样：

```emacs-lisp
(NAME (PROGRAM PROGRAM-ARGS...) &key CODING-SYSTEM INIT INIT-FUNCTION ENV)
```

- NAME
  是一个符号，用来指定 Lisp 程序
- PROGRAM
  是程序的文件名。注意文件名可以包含空格。
- PROGRAM-ARGS
  是一个命令行参数的列表。
- CODING-SYSTEM
  指定了连接的编码系统（见 6.1 Emacs 端 slime-net-coding-system）。
- INIT
  应该是一个接受两个参数的函数：一个文件名和一个字符编码。这个函数应该返回一个字符串格式的 Lisp 表达式，来指导 Lisp 启动 Swank 服务器并且将端口号写入文件。启动时，Slime 启动一个 Lisp 进程并将此函数的结果发送给 Lisp 的标准输入。默认情况下，slime-init-command 会被使用。“2.5.3 更快地加载 Swank”里有一个例子。
- INIT-FUNCTION
  应该是一个不接受参数的函数。连接建立之后它会被调用。（见 6.1.1 钩子 slime-connected-hook）
- ENV
  一个为子进程指定环境变量的列表。例如：
```emacs-lisp
(sbcl-cvs ("/home/me/sbcl-cvs/src/runtime/sbcl"
     "--core" "/home/me/sbcl-cvs/output/sbcl.core") :env ("SBCL_HOME=/home/me/sbcl-cvs/contrib/"))
```
在子进程中初始化 SBCL_HOME。

### 2.5.3 更快地加载 Swank

对于 SBCL，我们建议你新建一个有 socket 支持和 POSIX 绑定的核心配置文件，因为这些模块加载起来很耗时。为了新建一个这样的核心，执行以下的命令：

```sh
shell$ sbcl
(mapc 'require '(sb-bsd-sockets sb-posix sb-introspect sb-cltl2 asdf))
(save-lisp-and-die "sbcl.core-for-slime")
```

然后，在你的 .emacs 文件里加入如下代码：

```emacs-lisp
(setq slime-lisp-implementations
      '((sbcl ("sbcl" "--core" "sbcl.core-for-slime"))))
```

为了最大化启动速度，你可以在核心文件里直接包含 Swank 服务器。这样做的缺点是设置的时候比较麻烦，并且当你想升级你的 Slime 或者 SBCL 的时候你要新建一个核心文件。这样做的步骤是：

```sh
shell$ sbcl
(load ".../slime/swank-loader.lisp")
(swank-loader:dump-image "sbcl.core-with-swank")
```

然后在 .emacs 里加入如下代码：

```emacs-lisp
(setq slime-lisp-implementations
      '((sbcl ("sbcl" "--core" "sbcl.core-with-swank") :init (lambda (port-file _)
        (format "(swank:start-server %S)\n" port-file)))))
```

类似的配置对其它 Lisp 实现也适用。

# 三、使用 Slime 模式

Slime 的所有命令都通过 slime-mode 提供。它是一个与 Emacs 的 lisp-mode 配合使用的 minor-mode。本章描述 slime-mode 及其相关事项。

## 3.1 用户介面须知

要方便地使用 Slime，了解一些“全局的”用户界面特性是十分重要的。这一部分描述了最为重要的原则。

### 3.1.1 临时缓冲区

某些 Slime 命令会创建临时缓冲区来显示结果。虽然这些缓冲区有它们自己的为了特定目的而使用的 major-mode，但某些特定的约定是通用的。

可以通过按 q 键来关闭临时缓冲区。此操作会关闭缓冲区并且回复缓冲区显示之前的窗口配置。临时缓冲区也可以通过一般的命令例如 kill-buffer 来关闭，这样的话之前的窗口配置就不会被恢复。

按 RET 键被认为是“做最明显的有用的事情”。例如，在 apropos 缓冲区此操作会打印出当前光标处的符号的详细描述，而在 XREF 缓冲区此操作会显示当前光标处的索引的源代码。这样的行为是效仿 Emacs 的显示相关内容、补全结果等的缓冲区。

含有 Lisp 符号的临时缓冲区会使用 slime-mode 来补充它自己的特定的模式。这样就可以使用一般的 Slime 命令，例如描述符号、查找函数定义等等。

对这些“描述性的”缓冲区的初始聚焦由变量 slime-description-autofocus 确定。如果它是 nil（默认的），这些描述性缓冲区不会自动聚焦，反之则会。

### 3.1.2 `*inferior-lisp*`缓冲区

Slime 在内部使用 comint 包来启动 Lisp 进程。这会产生一些用户可见的结果，有些是好的，另一些则不是。为了避免产生疑惑，理解其交互特性是很有用的。

*inferior-lisp* 缓冲区包含有 Lisp 进程自己的 top-level。这个与 Lisp 的直接连接对错误排查很有用，并且使用 inferior-slime-mode 可以达到某种程度上的 Slime 集成。许多人选择加载更好的集成模块 SLIME REPL 包（见 8.2 REPL）而无视 *inferior-lisp* 缓冲区。（见 8.1 加载扩展包 获得更多关于启动 REPL 的信息。）

### 3.1.3 多线程

如果 Lisp 支持多线程，对于每个请求 Slime 会生成一个新的线程，例如，C-x C-e 会创建一个新线程来对表达式求值。但是对于从 REPL 来的请求则是一个例外：所有在 REPL 缓冲区里输入的命令都会在一个专用的 REPL 线程里求值。

多线程和特殊变量会导致一些复杂性。非全局的特殊绑定是在本地线程里的，也就是说，在一个线程里改变一个由 let 绑定的特殊变量的值不会影响到其它线程里相同名字的变量的值。这增加了改变新线程的打印和读取行为的困难程度。变量 swank:*default-worker-thread-bindings* 就是为了应付这种情况的：不需要改变一个变量的全局的值，而是增加 swank:*default-worker-thread-bindings* 的绑定，例如，使用下面的代码，新的线程会默认将浮点值读取为 double。

```emacs-lisp
(push '(*read-default-float-format# . double-float)
      swank:*default-worker-thread-bindings*)
```

### 3.1.4 键绑定

总体上我们会让我们的键绑定跟 Emacs 的键绑定配合良好。我们也使用了我们自己的某种不太寻常的约定：当键入一个三次按键的命令时，最后一次按键可以按 Control 也可以不按。例如，slime-describe-symbol 命令的键绑定是 C-c C-d d，但是按 C-c C-d C-d 也是同样的。我们将两种方式都绑定了，因为有些人喜欢三次按键都按着 Control 键，而有些人则不是。并且有两次按键作为前缀，我们不怕键不够用。

这条规则只有一个例外，希望不要让你中招。我们从来不在任何命令里绑定 C-h 键，所以 C-c C-d C-h 跟 C-c C-d h 做的事情是不一样的。这是因为 Emacs 内建的默认情况是，输入一个前缀，然后按 C-h，会显示处所有以该前缀开始的键绑定。所以 C-c C-d C-h 命令实际上会显示出所有的文档命令。这个特性太有用了所以我们不会替换它！

“你是故意破坏 Emacs 超级牛逼的在线帮助机制吗？上帝都会震怒的！”

此建议十分有用。Emacs 的在线帮助机制是你最快捷、最完整和最新的关于键绑定的信息来源。它们是你的朋友：

- C-h k <key> 或者 M-x describe-key <key>

  描述当前缓冲区的绑定到<key>的函数。

- C-h b 或 describe-bindings

  列出当前缓冲区的所有键绑定。

- C-h m 或 describe-mode

  显示所有当前缓冲区可用的 major-mode 的命令，然后是所有的 minor-mode 的命令。

- C-h l 或 view-lossage

  按顺序显示出你刚刚按了的所有按键的序列。

注意：在本文档里 C-h 指定的意思是一个“典型键绑定”（canonical key），它可能是 Ctrl-h 或者是 F1。或者是普通情况下你的.emacs 文件里配置的 help-command 函数的绑定。下面是一种情形：

```emacs-lisp
(global-set-key [f1]   'help-command)
(global-set-key "\C-h" 'delete-backward-char)
```

在这种情况下，在本文档中任何地方你看到的 C-h 你都可以用 F1 来代替。

你可以像这样用 global-set-key 函数在你的~/.emacs 文件里全局地更改默认的键绑定：

```emacs-lisp
(global-set-key "\C-c s" 'slime-selector)
```

这会绑定 slime-select 函数到 C-c s。

或者，如果你只是想在特定的 slime 模式下新建或改变键绑定，你可以像这样在你的~/.emacs 文件里使用 define-key 函数：

```emacs-lisp
(define-key slime-repl-mode-map (kbd "C-c ;") 'slime-insert-balanced-comments)
```

这会在 REPL 缓冲区里绑定 slime-insert-balanced-comments 函数到 C-c ; 键绑定。

## 3.2 求值命令

这些命令每一个都以不同的方式来对一个 Common Lisp 表达式求值。一般来说它们模仿 Emacs Lisp 的求值命令。默认情况下它们会在显示区显示出结果，但是一个前缀参数会让结果插入到当前缓冲区中。

- C-x C-e 或 M-x M-x slime-eval-last-expression

  对光标前的表达式求值并且将结果显示到显示区

- C-M-x 或 M-x slime-eval-defun

  对当前 toplevel 的形式进行求值并将结果打印到显示区。“C-M-x”会特别对待“defvar”。正常来讲，如果定义的变量已经有一个值了，“defvar”表达式不会做任何事情。但是“C-M-x”命令无条件的将“defvar”表达式里定义的值初始化并赋予指定的值。这个特性十分便于调试 Lisp 程序。

如果带数字参数地执行 C-M-x 或者 C-x C-e，它会将结果插入到当前缓冲区，而不是将其打印到显示区。

- C-c : 或 M-x slime-interactive-eval

  从迷你缓冲区读取一个表达式并求值

- C-c C-r 或 M-x slime-eval-region

  对区域进行求值

- C-c C-p 或 M-x slime-pprint-eval-last-expression

  对光标前的表达式进行求值并将结果漂亮地打印在一个新的缓冲区里

- C-c E 或 M-x slime-edit-value

  在一个叫做“Edit <form>”的新缓冲区里编辑一个可以 setf 的形式的值。这个值会被插入一个临时缓冲区以便编辑，然后用 C-c C-c 命令来提交设置于 Lisp 中。

- C-x M-e 或 M-x slime-eval-last-expression-display-output

  对光标前的表达式求值并将结果打印在显示缓冲区里。如果表达式会写一些内容到输出流的话这会很有用。

- C-c C-u 或 M-x slime-undefine-function

  用 fmakunbound 来取消当前光标处函数的定义。

## 3.3 编译命令

Slime 有许多很好的命令来编译函数、文件和包。好的地方在于，很多 Lisp 编译器生成的提示和警告会被拦截，然后直接注释给 Lisp 源文件缓冲区里相应的表达式。（试一试看会发生什么。）

- C-c C-c 或 M-x slime-compile-defun

  编译光标处的 top-level 形式。被选择的区域会闪一下以给出回应，表明是哪一部分被选择了。若给了一个（正的）前缀参数的时候，形式会以最小调试设置来编译。若是一个负的前缀参数，编译速度会被优化。区域里的代码在编译之后将要被执行，总的来说，此命令将该区域写入一个文件，编译该文件，然后加载结果代码。

- C-c C-k 或 M-x slime-compile-and-load-file

  编译和加载当前缓冲区的源文件。如果编译步骤失败了，那么文件不会被加载。编译是否失败并不总是那么容易判断的：某些情况下你可能会在加载阶段进入调试器。

- C-c M-k 或 M-x slime-compile-file

  编译（但不加载）当前缓冲区的源文件。

- C-c C-l 或 M-x slime-load-file

  加载 Lisp 文件。此命令用到了 Common Lisp 的 LOAD 函数。

- M-x slime-compile-region

  编译选中的区域。

Slime 通过在源代码的形式下加下划线来表示有提示信息。可以通过将鼠标置于文本处或者下面这些选择命令来阅读带有提示信息的编译器消息。

- M-n 或 M-x slime-next-note

  将光标移到下一个编译器消息处并显示消息。

- M-p 或 M-x slime-previous-note

  将光标移到上一个编译器消息处并显示消息。

- C-c M-c 或 M-x slime-remove-notes

  删除缓冲区里的所有提示信息。

- C-x ‘ 或 M-x next-error

  访问下一个错误消息。实际上这不是一个 Slime 命令，Slime 会创建一个隐藏的缓冲区，然后大部分的编译模式的命令（见 info “emacs”文件的“Compilation Mode”节点）都会类似批处理编译器一样地编译 Lisp。

## 3.4 补全命令

补全命令的作用是根据光标处已有的东西来补全一个符号或者形式。典型的补全假设一个确定的前缀，给出的选择也只是可能发生的分支。模糊补全会做更多的尝试。

- M-TAB 或 M-x slime-complete-symbol

  补全光标处的符号。注意，Slime 里有三种模式的补全；默认的模式跟正常的 Emacs 补全类似（见 6.1 slime-complete-symbol-function）

## 3.5 查找定义（“Meta-Point”命令）

Slime 提供了熟悉的 M-. 命令。对于广泛函数来讲此命令会找出所有的方法，而在某些系统上它会做一切其它事情（例如根据 DEFSTRUCT 定义来追踪结构访问器）。

- M-. 或 M-x slime-edit-definition

  跳至光标处符号的定义处

- M-, 或 M-# 或 M-x slime-pop-find-definition-stack

  回到 M-. 命令执行的光标处。如果 M-. 被执行了多次，那么此命令会多重地回溯。

- C-x 4 . 或 M-x slime-edit-definition-other-window

  类似 slime-edit-definition，但是会跳到另一个窗口来编辑其定义。

- C-x 5 . 或 M-x slime-edit-definition-other-frame

  类似 slime-edit-definition，但是会跳到另一个框架来编辑其定义。

- M-x slime-edit-definition-with-etags

  使用 ETAGES 的表来寻找当前光标处的定义。

## 3.6 文档命令

Slime 的在线文档命令效仿了 Emacs 的例子。这些命令都以 C-c C-d 为前缀，并且允许更改其键绑定或者取消更改（见 3.1.4 键绑定）

- SPC 或 M-x slime-space

  Space 键插入一个空格键，并且也查找并显示出当前光标处函数的参数列表，如果有的话。

- C-c C-d d 或 M-x slime-describe-symbol

  描述当前光标处的符号。

- C-c C-d f 或 M-x slime-describe-function

  描述当前光标处的函数。

- C-c C-d a 或 M-x slime-apropos

  对于一个正则表达式执行一个合适的搜索，来搜索所有的 Lisp 符号名称，并且显示出相应的文档字符串。默认情况下所有包的外部变量都会被搜索，你可以用一个前缀参数来指定特定的包或者是否包含未导出的符号。

- C-c C-d z 或 M-x slime-apropos-all

  类似 slime-apropos 但是默认包含所有内部符号。

- C-c C-d p 或 M-x slime-apropos-package

  显示包内所有符号的合适的结果。这个命令是用来在一个较高层次浏览包的。加上包名补全，它可以差不多被当作是一个 Smalltalk 类似的图像浏览器。

- C-c C-d h 或 M-x slime-hyperspec-lookup

  在《Common Lisp Hyperspec》里查找当前光标处的符号。它使用常用的 hyperspec.el 来在浏览器里显示相应的部分。Hyperspec 可以在网络上或者在 common-lisp-hyperspec-root 处，默认打开的浏览器通过 browse-url-browser-function 指定。

  注意：这里就是一个 C-c C-d h 跟 C-c C-d C-h 不同的例子

- C-c C-d ~ 或 M-x hyperspec-lookup-format

  在《Common Lisp Hyperspec》里查找一个 foramt 格式控制符。

- C-c C-d # 或 M-x hyperspec-lookup-reader-macro

  在《Common Lisp Hyperspec》里查找一个读取宏。

## 3.7 交叉引用命令

Slime 的交叉引用命令是基于 Lisp 系统的支持的，而此特性在不同 Lisp 实现上差异颇大。对于那些没有内置 XREF 支持的 Lisp 系统，Slime 需要一个可移植的 XREF 包，这个包是从 CMU AI Repository 获得并且与 Slime 绑定在一起的。

所以这些命令的操作对象都是当前光标处的符号，如果没有，则会要求用户输入。如果有前缀参数，则总会要求用户输入符号。你可以输入以下所示的键绑定，或者将最后一个键加上 control。见 3.1.4 键绑定。

- C-c C-w c 或 M-x slime-who-calls

  显示该函数的调用者。

- C-c C-w c 或 M-x slime-who-calls

  显示该函数调用了的函数。

- C-c C-w r 或 M-x slime-who-references

  显示对全局变量的引用。

- C-c C-w b 或 M-x slime-who-binds

  显示对全局标量的绑定。

- C-c C-w s 或 M-x slime-who-sets

  显示对全局标量的赋值。

- C-c C-w m 或 M-x slime-who-macroexpands

  显示某个宏扩展之后的结果。

- M-x slime-who-specializes

  显示一个类所有已知的方法。

当然也有所谓的“列出调用者 / 被调用者”命令。这些操作会在一个很底层的层次上搜寻堆上的函数对象，来确定所有调用的情况。只有某些 Lisp 系统有此功能，并且在无法获得精确的 XREF 信息时，这些功能可以作为备用。

- C-c < 或 M-x slime-list-callers

  列出一个函数的所有调用者。

- C-c > 或 M-x slime-list-callees

  列出一个函数所有调用的函数。

### 3.7.1 XREF 缓冲区命令

XREF 缓冲区可用的命令。

- RET 或 M-x slime-show-xref

  在另一个窗口里显示当前光标处的符号的定义。不离开 XREF 缓冲区。

- Space 或 M-x slime-goto-xref

  在另一个窗口里显示当前光标处的符号的定义并且关闭 XREF 缓冲区。

- C-c C-c 或 M-x slime-recompile-xref

  重新编译当前光标处的定义。

- C-c C-c 或 M-x slime-recompile-all-xrefs

  重新编译所有定义。

## 3.8 宏扩展命令

- C-c C-m 或 M-x slime-macroexpand-1

  将光标处的表达式宏展开一次。如果带有一个前缀参数，则使用 macroexpand 代替 macroexpand-1。

- C-c M-m 或 M-x slime-macroexpand-all

  将光标处的表达式完全宏展开。

- M-x slime-compiler-macroexpand-1

  显示光标处的编译宏展开的 sexp。

- M-x slime-compiler-macroexpand

  反复展开光标处的编译宏的 sexp。

更多的 minor-mode 命令及相关讨论见 5.2 slime-macroexpansion-minor-mode。

## 3.9 分解命令

- C-c M-d 或 M-x slime-disassemble-symbol

  分解光标处的函数定义。

- C-c C-t 或 M-x slime-toggle-trace-fdefinition

  触发对光标处函数的跟踪。若有前缀参数，则读取附加信息，例如跟踪某个指定的方法。

- M-x slime-untrace-all

  停止跟踪所有函数。

## 3.10 中止 / 恢复命令

- C-c C-b 或 M-x slime-interrupt

  中断 Lisp 进程（发送 SIGINT）。

- M-x slime-restart-inferior-lisp

  重启 inferior-lisp 进程。

- C-c ~ 或 M-x slime-sync-package-and-default-directory

  从 Emacs 到 Lisp 同步当前包到工作目录。

- C-c M-p 或 M-x slime-repl-set-package

  设置 REPL 的包。

- M-x slime-cd

  设置 Lisp 进程所在的当前目录。这也改变了 REPL 的当前目录。

- M-x slime-pwd

  打印出 Lisp 进程的当前目录。

## 3.11 检查命令

Slime 查看器是一个基于 Emacs 的对普通 INSPECT 函数的替代选择。Slime 查看器会在一个 Emacs 缓冲区中结合使用文本和对其它对象的超链接来展示一个对象。

Slime 查看器可以轻易地为你的程序里的对象做定制化。详细用法见 swank-backend.lisp 里的 inspect-for-emacs 广泛函数。

- C-c I 或 M-x slime-inspect

  查看输入在一个迷你缓冲区里的表达式的值。

在查看器里可以使用的标准命令有：

- RET 或 M-x slime-inspector-operate-on-point

  如果光标处是一个值，那么对这个值递归地调用查看器查看它。如果光标处是一个命令，则调用它。

- d 或 M-x slime-inspector-describe

  描述光标处的槽。

- v 或 M-x slime-inspector-toggle-verbose

  在冗余模式和简洁模式之间切换。默认值由 swank:*inspector-verbose* 指定。

- l 或 M-x slime-inspector-pop

  回到前一个对象（从 RET 返回）。

- n 或 M-x slime-inspector-next

  l 的逆操作。也绑定到了空格键。

- g 或 M-x slime-inspector-reinspect

  再次查看。

- q 或 M-x slime-inspector-quit

  关闭查看缓冲区。

- p 或 M-x slime-inspector-pprint

  在另一个缓冲区里打印出光标处的对象。

- . 或 M-x slime-inspector-show-source

  查看光标处的对象的源码。

- > 或 M-x slime-inspector-fetch-all

  取得所有查看器的内容并且移到其最后。

- M-RET 或 M-x slime-inspector-copy-down

  将光标之后所有在“*”变量里的值存储起来。这些对象可以之后在 REPL 中访问。

- TAB, M-x slime-inspector-next-inspectable-object 或 S-TAB, M-x slime-inspector-previous-inspectable-object

  分别是跳至下一个或者前一个可查看对象。

## 3.12 分析命令

所有的分析命令都是给予 CMUCL 的分析器。它们都是对函数的简单包装，然后打印一些信息到输出缓冲区。

- M-x slime-toggle-profile-fdefinition

  触发对一个函数的分析。

- M-x slime-profile-package

  分析一个包里的函数。

- M-x slime-profile-by-substring

  分析所有名字含有某个子串的函数。

- M-x slime-unprofile-all

  停止所有分析。

- M-x slime-profile-report

  报告分析数据。

- M-x slime-profile-reset

  重置分析数据。

- M-x slime-profiled-functions

  显示当前所有正在分析的函数。

## 3.13 遮盖命令

- C-c C-a, M-x slime-nop 和 C-c C-v, M-x slime-nop

  此键绑定由 inf-lisp 遮盖。

## 3.14 语义缩进

Slime 会自动地决定如何缩进你的 Lisp 程序里的宏。为了达到这个目的，Lisp 端会查看系统内所有的宏并且将有 &body 参数的宏报告给 Emacs。然后 Emacs 会对这些宏的缩进特殊处理，通常情况是，将其第一个参数缩进四个空格，而 body 参数缩进两个空格。

这仅仅“够用”。如果你是那种很幸运的人，那么你要阅读本节剩下的内容。

为了简化实现，Slime 并不区分宏和其他简单的符号名，除了不同的包名。这使得 Slime 和 Emacs 自己的缩进方式兼容得很好。但是，如果你有些宏和某些简单符号有相同的名字，那么它们的缩进会相同，使用它们的参数列表里任一缩进方式。你可以找出有哪些符号有缩进冲突：

```emacs-lisp
  (swank:print-indentation-lossage)
```

如果有冲突让你很恼火，不要崩溃，只要用你喜欢的缩进方式覆盖 elisp 符号的 common-lisp-indent-function 属性就可以了。Slime 不会覆盖你的定制变量，它只是尝试给你最好的默认设置。

更加巧妙的是，有个不那么完美的缓存机制来保证良好的性能。

理想情况下，在每次 Emacs 操作之后，Lisp 会自动查看所有符号的缩进的改变情况。但是若要每次都执行，那么效率就太低了。所以，Lisp 通常只会查看那些 Emacs 使用到的属于本地包的符号，而所有的请求都是从它们那里来的。这使得取得在交互环境下定义的宏的缩进变得十分高效。为了查看剩下的那些，当有新的 Lisp 包被创建的时候——例如新系统被加载，所有的符号都会被查看。

你可以使用 M-x slime-update-indentation 来强制要求所有符号的缩进信息都被查看。

## 3.15 根据读取器的结果字符化

Slime 会自动对读取器条件判断表达式进行求值，例如#+linux，在源代码缓冲区里，所有会被当前 Lisp 连接所忽略掉的代码都会呈现为灰色以示不被读取。

# 四、SLDB：Slime 调试器

Slime 有一个自己的基于 Emacs 的调试器，SLDB。Lisp 系统里的状况（Condition）发出的信号会在 Emacs 里通过 Lisp 符号`*DEBUGGER-HOOK*`触发 SLDB。

当有状况发出信号时，SLDB 会生成一个新的缓冲区。这个缓冲区会显示对状况的描述、一系列重启选项和调用栈。可以通过提供的命令来出发重启、检查调用栈和在堆栈调用窗口里移动。

## 4.1 检查窗口

用来查看光标处的堆栈调用窗口的命令。

- t 或 M-x sldb-toggle-details

  出发显示本地变量和 CATCH 标签

- v 或 M-x sldb-show-source

  查看窗口处的当前代码表达式。此表达式会被展现在 Lisp 源代码的缓冲区里。

- e 或 M-x sldb-eval-in-frame

  对窗口里的表达式求值。表达式可以引用窗口里可用的局部变量。

- d 或 M-x sldb-pprint-eval-in-frame

  对窗口里的表达式求值，并且在一个临时缓冲区里打印出来。

- D 或 M-x sldb-disassemble

  拆解窗口处的函数。包括例如窗口对指令指针等信息。

- i 或 M-x sldb-inspect-in-frame

  查看对窗口里一个函数求值后的结果。

- C-c C-c 或 M-x sldb-recompile-frame-source

  重新编译窗口。C-u C-c C-c 是以最大调试模式重新编译。

## 4.2 重启

- a 或 M-x sldb-abort

  触发 ABORT 重启。

- q 或 M-x sldb-quit

  “QUIT”——THROW 一个 top-level 的 Slime 请求循环可以捕获的标签。

- c 或 M-x sldb-continue

  触发 CONTINUE 重启。

- 0 ... 9

  通过数字触发重启。

重启也可以通过在缓冲区里按 RET 或者 Mouse-2 来触发。

## 4.3 在不同的窗口间操作

- n 或 M-x sldb-down， p 或 M-x sldb-up

  在不同窗口间移动。

- M-n 或 M-x sldb-details-down， M-p 或 M-x sldb-details-up

  有技巧地在不同窗口间移动：隐藏之前窗口的详细信息，显示下一个窗口的详细信息和源代码。这些小技巧让你只看到当前窗口的详细信息和源代码。

- > 或 M-x sldb-end-of-backtrace

  获取所有的堆栈调用并且到最后一个窗口。

- < 或 M-x sldb-beginning-of-backtrace

  到第一个窗口。

## 4.4 单步调试

单步调试并非在所有的 Lisp 实现里都是可用的，在那些可用的 Lisp 实现中，它的表现也很不同。

- s 或 M-x sldb-step

  单步运行窗口中的下一个表达式。对 CMUCL 来说，即时在从当前位置可到达的所有代码的位置上设置断点。

- x 或 M-x sldb-next

  单步运行函数里的下一个形式。

- o 或 M-x sldb-out

  暂时停止单步调试，当前函数一返回则恢复单步调试。

## 4.5 其它命令

- r 或 M-x sldb-restart-frame

  以当前窗口最先运行时的参数为参数重启。（此命令并非在所有 Lisp 实现里都可用）

- R 或 M-x sldb-return-from-frame

  在 minibuffer 里输入一个值，并以这个值为返回值回到窗口。（此命令并非在所有 Lisp 实现里都可用）

- B 或 M-x sldb-break-with-default-debugger

  退出 SLDB 并且以 Lisp 默认的调试器调试状况。

- C 或 M-x sldb-inspect-condition

  查看当前正在调试的状况。

- : 或 M-x slime-interactive-eval

  在 minibuffer 里输入一个表达式并求值。

# 五、杂项

## 5.1 slime-selector

slime-selector 用来快速切换到重要的缓冲区：REPL、SLDB、你正在 hack 的 Lisp 源代码等等。一旦触发 slime-selector，该命令会要求你输入一个字母来指定显示哪一个缓冲区。这里是一些选项。

- ?

  一个帮助缓冲区，它会列出所有 slime-selector 可以显示的缓冲区。

- r

  当前 Slime 连接的 REPL 缓冲区。

- d

  当前连接最近使用的 SLDB 缓冲区。

- l

  最近访问的 Lisp 源代码缓冲区。

- s

  *slime-scratch* 缓冲区。

- c

  Slime 连接缓冲区。

- t

  Slime 线程缓冲区。

slime-selector 没有一个默认的键绑定，但是我们建议你给它设置一个全局的键绑定。你可以像这样将它设置为 C-c s：

```emacs-lisp
(global-set-key "\C-c s" 'slime-selector)
```

然后你就可以通过 C-c s r 从任何地方切换到 REPL。

宏 def-slime-selector-method 可以用来定义 slime-selector 可识别的新缓冲区。

## 5.2 slime-macroexpansion-minor-mode

一个 Slime 宏扩展缓冲区提供了一些其它命令（这些命令一直是可用的，只是只有在宏扩展缓冲区里才绑定到了特定的键上）

- C-c C-m 或 M-x slime-macroexpand-1-inplace

  像 slime-macroexpand-1 一样不过原来的形式被展开后的形式替代了。

- g 或 M-x slime-macroexpand-1-inplace

  最后的宏展开再次被执行，当前宏展开缓冲区的内容被新展开的内容替换掉了。

- q 或 M-x slime-temp-buffer-quit

  关闭展开缓冲区。

- C-_ 或 M-x slime-macroexpand-undo

  取消最后一次宏展开操作。

## 5.3 多重连接

Slime 可以同时连接到多个 Lisp 进程。当带有前缀参数地调用 M-x slime 命令时，如果已经有一个 Lisp 进程了，它会创建一个新的 Lisp 连接。这很方便，但是这需要一些技巧来确保你的 Slime 命令是在你期望的 Lisp 进程里执行的。

有些缓冲区是连接到特定的 Lisp 进程的。每个 Lisp 进程都有自己的 REPL 缓冲区，在相应缓冲区里输入的所有表达式和所有命令都会被发送到相应的连接。其它 Slime 创建的进程也类似地跟它们最开始的进程绑定在一起，包括 SLDB 缓冲区、搜索结果等等。这些缓冲区是跟 Lisp 进程交互的结果，所以在这些缓冲区里执行的命令也发送到相应的进程。

在其它地方执行的命令，例如 slime-mode 源代码缓冲区，总是使用“默认的“链接。通常来讲是最近建立的连接，但是这可以通过“连接列表“缓冲区重新指定。

- C-c C-x c 或 M-x slime-list-connections

  生成一个缓冲区并列出所有已建立的连接。

- C-c C-x t 或 M-x slime-list-threads

  生成一个缓冲区并显示当前线程。

slime-list-connections 生成的缓冲区显示对每个连接有个一行的简介。简介显示了连接的序列号、Lisp 实现的名字以及其它的 Lisp 进程信息。当前的“默认”连接通过一个星号来指示。

connection-list 缓冲区里可用的命令有：

- RET 或 M-x slime-goto-connection

  显示光标处连接的 REPL 缓冲区。

- d 或 M-x slime-connection-list-make-default

  让光标处的连接成为“默认“连接。它会被 slime-mode 源代码缓冲区的命令使用。

- g 或 M-x slime-update-connection-list

  更新缓冲区里的连接列表。

- q 或 M-x slime-temp-buffer-quit

  退出连接列表缓冲区（杀掉缓冲区，回到之前的窗口设置）

- R 或 M-x slime-restart-connection-at-point

  重启光标处的进程的连接。

- M-x slime-connect

  连接到一个运行中的 Swank 服务器。

- M-x slime-disconnect

  退出所有连接。

- M-x slime-abort-connection

  取消当前的连接尝试。

# 六、定制

## 6.1 Emacs 端定制

Slime 的 Emacs 部分可以通过 Emacs 的定制系统来进行配置，通过 M-x customize-group slime RET。由于此定制系统是自描述的，因此在本文档里我们只会包含某些重要的或者可能有歧义的选项。

- slime-truncate-lines

  在 Slime 生成的 line-by-line 摘要缓冲区里使用 truncate-lines 的值。这是默认的，来保证行不会被以某种回溯的列表来折叠。它可能会造成某些信息超出屏幕。

- slime-complete-symbol-function

  用来补全 Lisp 符号的函数。有三种补全方式：slime-simple-complete-symbol，slime-complete-symbol*（见 8.5 混合补全），和 slime-fuzzy-complete-symbol（见 8.6 模糊补全）。

  默认的是 slime-simple-complete-symbol，类似于 Emacs 的补全方式。

- slime-filename-translations

  这个变量控制 Emacs 和 Lisp 之间文件名的转换。当你在不同的机器上运行 Emacs 和 Lisp，而它们的文件系统并不相同，或者它们有相同的文件系统但是布局不同，例如使用了 SMB 的文件共享的时候，这个变量就有用了。

- slime-net-coding-system

  如果你想在 Emacs 和 Lisp 之间传送 Unicode 字符，你应该设置此变量。例如你使用 SBCL，你应该这样设置：

  ```emacs-lisp
  (setq slime-net-coding-system 'utf-8-unix)
  ```

  要显示 Unicode 字符你还需要适当的字体，否则这些字符会被渲染成空心方块。如果你使用的是 Allegro CL 和 GNU Emacs，你也可以使用 emacs-mule-unix 作为编码系统。GNU Emacs 给较新的编码提供了很不错的字体。（不同的 Lisp 系统可以使用不同的编码，见 2.5.2 多种 Lisp。）

### 6.1.1 钩子

- slime-mode-hook

  每次有一个缓冲区进入 slime-mode，这个钩子就会启动。它最有用的是在你的 Lisp 源代码缓冲区里设置当前缓冲区的配置。有个例子是用它来启动 slime-autodoc-mode（见 8.7 slime-autodoc-mode）。

- slime-connected-hook

  这个钩子在每次连接到 Lisp 服务器的时候建立。有个例子是用它来生成一个打印窗口（见 8.13 打印窗口）。

- sldb-hook

  这个钩子在 SLDB 启动时创建。这个钩子的函数是从 SLDB 缓冲区建立后调用的。有个例子是添加 add sldb-print-condition 函数到这个钩子，这会让所有 SLDB 调试的状况被 REPL 缓冲区记录到。

## 6.2 Lisp 端（Swank）

Slime 的 Lisp 服务器端（也叫做“Swank”），提供几个变量可供设置。初始化文件~/.swank.lisp 在启动时会被自动求值，可以用来设置这些变量。

### 6.2.1 通信模式

最重要的可以配置的变量是 SWANK:*COMMUNICATION-STYLE*，它指定了 Lisp 端接收和读取 Emacs 发出的信息的协议。通信方式的选择会在全局影响 Slime 的行为。

可选的通信方式有：

- NIL

  这种方式只是简单地循环地读取通信套接字里的输入和当 Slime 协议事件发生是提供服务。这种简单意味着当处于 Slime 的控制的时候 Lisp 不能做其它的处理。

- :FD-HANDLER

  这种方式使用经典的 Unix 的“select-loop()”。Swank 通过一个事件分发框架（例如 CMUCL 和 SBCL 里的 SERVE-EVENT）来注册一个通信套接字，当数据到达时接收一个回调。在这种方式下，从 Emacs 而来的请求只在 Lisp 进入了事件循环时才会被检测到并处理。这种方式简单并且可预测。

- :SIGIO

  这种方式使用带有 SIGIO 信号处理函数的信号驱动 I/O。Lisp 接收来自 Emacs 的请求，附带一个信号，使 Lisp 中断任何正在做的事来处理请求。这种方式的好处是回应即时，因为 Emacs 可以使 Lisp 执行某些操作，即使 Lisp 正在忙于某些事情。它也允许 Emacs 并发地发起请求，例如发起一个长期的请求（例如编译）然后在它完成前发起几个短期的请求。它的缺点是它可能其它用户的 Lisp 代码的 SIGIO 信号冲突，而且在某些特殊的时刻中断 Lisp 进程的话则可能产生很严重的后果。

- :SPAWN

  这种方式通过 Lisp 里的多线程支持来在每个线程里执行一个请求。这种方式跟:SIGIO 有某些类似的特性，但它不使用信号，并且所有来自 Emacs 的请求都可以并行处理。

默认的请求处理方式是根据你的 Lisp 系统的能力来选择的。通常的选择顺序是：:SPAWN，然后是:SIGIO，然后:FD-HANDLER，最后是 NIL。你可以通过调用 SWANK-BACKEND::PREFERRED-COMMUNICATION-STYLE 来查看默认的方式。你也可以通过在你的 Swank 初始化文件里设置 SWANK:*COMMUNICATION-STYLE* 变量来覆盖默认设置。

## 6.2.2 其它配置

这些 Lisp 变量可以通过你的~/.swank.lisp 文件设置：

- SWANK:*CONFIGURE-EMACS-INDENTATION*

  这个变量控制宏里 &body 参数的缩进方式是否会被探测到并发送给 Emacs。它默认开启。

- SWANK:*GLOBALLY-REDIRECT-IO*

  当它为真，标准输出流（例如 *standard-output*）会被全局重定向到 Emacs 里的 REPL。当它为假（默认情况），这些流只是在处理请求时通过动态绑定临时重定向到 Emacs。主意 *standard-input* 现在不会被全局重定向到 Emacs，因为当它尝试从 Emacs 里读取信息时，它跟 Lisp 原生的 REPL 交互得很差。

- SWANK:*GLOBAL-DEBUGGER*

  当它为真（默认情况），它让 *DEBUGGER-HOOK* 全局设置为 SWANK:SWANK-DEBUGGER-HOOK，当后让 Slime 处理 Lisp 进程里的所有调试工作。这是用来调试多线程或回调驱动的应用的。

- SWANK:*SLDB-PRINTER-BINDINGS* 和 SWANK:*MACROEXPAND-PRINTER-BINDINGS* 和 SWANK:*SWANK-PPRINT-BINDINGS*

  这些变量可以在不同的情况下设置打印器。这些变量的值是打印器变量名和对应的值组成的联合列表。例如，在 SLDB 中开启 pretty 打印器来处理调用栈，你可以这样：

  ```emacs-lisp
  (push '(*print-pretty* . t) swank:*sldb-printer-bindings*)
  ```

- SWANK:*USE-DEDICATED-OUTPUT-STREAM*

  这个变量控制了是否用一个不安全但很有效的 hack 来从 Lisp 打印输出到 Emacs。默认是 nil，并且强烈建议不要使用它。

  当它为 t 时，会建立一个单独的套接字来把 Lisp 的输出打印到 Emacs，这比使用协议发送信息来将输出发送到 Emacs 快。但是，由于不能保证一个专用的输出流和一个给予协议消息的流之间的时间，Lisp 命令的输出到达的时间可能在 REPL 相应时间之前或之后。输出结果和 REPL 的显示结果可能以错误的顺序呈现，甚至在 REPL 里交叉出现。使用一个专用的输出流也会让用 SSH 跟一个在远程服务器上的 Lisp 程序通信变得困难。（见 7.1 连接到远程 Lisp）

- SWANK:*DEDICATED-OUTPUT-STREAM-PORT*

  当 *USE-DEDICATED-OUTPUT-STREAM* 是 t，流会在此端口开启。默认值是 0，表示流会在某个随机端口开启。

- SWANK:*LOG-EVENTS*

  将这个变量设置为 t 会让所有与 Emacs 交换的协议信息都打印到 *TERMINAL-IO*。这在底层调试和观察 Slime 底层如何运行是很有用。*TERMINAL-IO* 的输出可以在 Lisp 系统自己的监视器里看到，通常是 *inferior-lisp* 缓冲区。

# 七、小技巧

## 7.1 连接到远程 Lisp

### 7.1.1 设置 Lisp 镜像

如果你不想通过一般的基于 Emacs 的方式加载 swank，只需要加载 swank-load.lisp 文件就可以了。只需要在一个运行中的 Lisp 镜像 [[1]](#1) 里执行以下代码：

```emacs-lisp
(load "/path/to/swank-loader.lisp")
```

现在，我们需要做的就是启动 swank 服务器。在第一个例子里，我们假设使用默认配置。

```emacs-lisp
(swank:create-server)
```

由于我们将要使用 ssh[[2]](#2) 来建立链接并且只打开了一个端口，我们不希望 swank 使用另一个端口作为输出（目前这在 swank 里是默认的）：

```emacs-lisp
(setf swank:*use-dedicated-output-stream* nil)
```

如果你有其它特别的需求（例如在结束后重新连接到 swank），请查看 swank:create-server 的其它参数。其中的一些参数如下：

- :PORT

  指定服务器所监听的端口号（默认端口：4005）

- :STYLE

  见 6.2.1 通信模式

- :DONE-CLOSE

  布尔值，指明了在服务器在接受了第一个连接后是否还会接收其它连接（默认值：nil）。对于你希望可以随时连接的长期运行的 Lisp 进程，指定:done-close t

- :CODING-SYSTEM

  字符串，指明了 Emacs 和 Lisp 之间进行通讯的编码

更加完整的实例如下：

```emacs-lisp
(swank:create-server :port 4005  :dont-close t :coding-system "utf-8-unix")
```

在 Emacs 端，你会进行类似如下的设置来连接到同一台机器上的 Lisp 镜像：

```emacs-lisp
(setq slime-net-coding-system 'utf-8-unix)
(slime-connect "127.0.0.1" 4005)
```

### 7.1.2 设置 Emacs

现在我们需要在本地机器和远程机器之间建立连接。

```sh
ssh -N -f -L 4005:127.0.0.1:4005 username@remote.example.com
```

这里调用的 ssh 在本地机器的 4005 端口和远程机器的 4005 端口上建立了一个 ssh 连接 [[3]](#3)。

最后，我们启动 Slime：

```emacs-lisp
M-x slime-connect RET RET
```

RET RET 按键表示我们要使用默认主机（127.0.0.1）和默认端口（4005）。虽然我们是连接到远程机器上的，ssh 连接让 Emacs 以为我们是在本地操作。

### 7.1.3 设置路径名翻译

远程运行 swank 的一个主要问题就是，Emacs 以为可以通过普通的文件名找到文件。如果我们希望例如 slime-compile-and-load-file （C-c C-k）或者 slime-edit-definition （M-.）这样的函数正常工作，我们需要找到一种方法让本地的 Emacs 正确地找到远程文件。

要做到这一点主要有两种方式。第一种是挂载，使用 NFS 或者类似的东西。将远程机器的硬盘挂载到本地机器的文件系统上，这样的话例如 /opt/project/source.lisp 的文件名就可以在两台机器上都指向同一个文件。不幸的是，NFS 很慢，而且容易出错，并且不总是可行的。幸运的是我们有 ssh 连接以及 Emacs 的 tramp-mode 来完成这项工作。

我们需要做的事情是让 Emacs 接收一个远程机器上的文件名，然后将其翻译为某种 tramp 可以理解和使用的格式，反之亦然。假设远程机器的主机名叫做 remote.example.com，cl:machine-instance 返回“remote”，我们以“user”用户登陆，我们使用 slime-tramp 扩展包来设置适当的翻译方式，如下：

```emacs-lisp
(push (slime-create-filename-translator :machine-instance "remote.example.com"
                                        :remote-host "remote"
                                        :username "user")
      slime-filename-translations)
```

## 7.2 重定向全局 IO 到 REPL

默认情况下 Slime 并不会改变 *standard-output* 和 REPL 以外的其它事物。如果你有一些其它的线程，例如 format，write-string 等等，相应的输出仅仅能在 *inferior-lisp* 缓冲区或者是终端下看到，通常来说这很不方便。所以如果你有这样的代码：

```emacs-lisp
(run-in-new-thread
 (lambda ()
   (write-line "In some random thread.~%" *standard-output*)))
```

并且想让它输出到 Slime 的 REPL 缓冲区而不是 *inferior-lisp* 缓冲区，只需要将 swank:*globally-redirect-io* 设置为 T。

注意，这个变量的值只会在 swank 接收连接的时候被检查，所以你需要把它写在~/.swank.lisp 文件里。否则你要自己调用 swank::globally-redirect-io-to-connection 命令，但是你不应该这样做除非你知道原因。

## 7.3 自动连接到 Slime

如果想要在打开一个 Lisp 文件的时候自动启动 Slime，将以下设置加入~/.emacs 文件里：

```emacs-lisp
(add-hook 'slime-mode-hook
          (lambda ()
            (unless (slime-connected-p)
              (save-excursion (slime)))))
```

## 7. 脚注

### [1]

Slime 也提供了一个功能相同的 ASDF 系统定义

### [2]

有一种不使用 ssh 来连接的方法，但是其副作用是将允许所有东西连接到你的 Lisp 镜像，所以我们不讨论这种方式

### [3]

默认情况下 swank 监听来自 4005 端口的连接，如果我们调用 swank:create-server 函数时指定:port 参数，我们就可以使用其它端口了。


# 八、扩展包

在 3.0 版中，我们将一些功能移到了单独的包里。本章讲述如何加载扩展包的模块，并且描述了这些包的功能。

## 8.1 加载扩展包

默认情况下扩展包并没有被加载。你必须稍微设置一下，这样 Emacs 就可以知道在哪里找到这些扩展包、加载哪些扩展包。总的来说，你应该调用 slime-setup 函数，并将需要用的包的名字作为一个列表传给它。例如，加载 slime-scratch 和 slime-editing-commands 包的设置如下：

```emacs-lisp
(setq inferior-lisp-program "/opt/sbcl/bin/sbcl") ; your Lisp system
(add-to-list 'load-path "~/hacking/lisp/slime/")  ; your SLIME directory
(require 'slime-autoloads)
(slime-setup '(slime-scratch slime-editing-commands))
```

启动 Slime 后，这些扩展包的命令应该都可用了。

这里要特别提到 REPL 和 slime-fancy 扩展包。许多用户认为 REPL（见 8.2 REPL）很必要，而 slime-fancy（见 8.20 slime-fancy）加载了 REPL 包和其它几乎所有常用的包。所以，如果你不知道怎样启动，试试：

```emacs-lisp
(slime-setup '(slime-repl)) ; repl only
```

如果你喜欢你见到的，试试：

```emacs-lisp
(slime-setup '(slime-fancy)) ; almost everything
```

## 8.2 REPL：“顶层环境”

Slime 使用定制的读 - 写 - 打印循环（REPL，也被称作“顶层环境”，或者是监听者）。REPL 界面使用 Emacs Lisp 写成，因此与传统的基于 comint 的 Lisp 交互相比，它与 Emacs 更加契合：

- 可以通过 SLDB 调试 REPL 里表达式的状况信号

- 打印出的不同的返回值可以通过不同的 Emacs faces（颜色）加以区分

- Emacs 通过不同的符号管理 REPL 提示符。这确保了 Lisp 的输出被插入到正确的位置，并且不会和其它的用户输入搞混。

加载 REPL 需要在你的.emacs 文件里调用 (slime-setup '(slime-repl))。

- C-c C-z 或 M-x slime-switch-to-output-buffer

  选择输出缓冲区，一般是在另一个窗口里

- C-c C-y 或 M-x slime-call-defun

  在 REPL 里调用在当前光标处定义的函数

### 8.2.1 REPL 命令

- RET 或 M-x slime-repl-return

  如果输入完成，则在 Lisp 里对当前输入求值。如果没有，则开启一个新行并缩进。如果给出了前缀参数，则不检查输入完整性而直接求值。

- C-RET 或 M-x slime-repl-closing-return

  关闭所有未匹配的括号并对当前输入求值。也绑定到了 M-RET。

- TAB 或 M-x slime-indent-and-complete-symbol

  缩进当前行并进行符号补全。

- C-j 或 M-x slime-repl-newline-and-indent

  新开一行并缩进。

- C-a 或 M-x slime-repl-bol

  到行首，但是在 REPL 提示符之后。

- C-c C-c 或 M-x slime-interrupt

  以 SIGINT 中断 Lisp 进程。

- C-c M-o 或 M-x slime-repl-clear-buffer

  清空当前缓冲区，只留一个提示符。

- C-c C-o 或 M-x slime-repl-clear-output

  从缓冲区里清空之前表达式的输出和结果

### 8.2.2 输入引导

输入导航（历史）命令是模仿 coming-mode 的。如果你习惯了类似 Bash 的键绑定，那么要注意了： M-p 和 M-n 使用当前的输入作为搜索样本，并且类似 Bash 一样只有在当前行是空的情况下才工作。C-<up>和 C-<down>像 Bash 里的 up 和 down 键效果一样。

- C-<up> 或 M-x slime-repl-forward-input 和 C-<down> 和 M-x slime-repl-backward-input

  去到前一个 / 后一个历史输入

- M-n 或 M-x slime-repl-next-input 和 M-p 或 M-x slime-repl-previous-input

  使用当前的输入作为搜索样本，在输入历史里向前 / 向后搜索相关输入。如果 M-n/M-n 被连续按两次，第二次调用会使用相同的搜索样本（即使当前输入已经改变）。

- M-s 或 M-x slime-repl-next-matching-input 和 M-r 或 M-x slime-repl-previous-matching-input

  使用正则表达式在输入历史里向前 / 向后搜索

- C-c C-n 或 M-x slime-repl-next-prompt 和 C-c C-p 或 M-x slime-repl-previous-prompt

  在 REPL 缓冲区的当前和前一个提示符之间移动。在有之前输入的一行里按 RET 会把那一行复制到最新的提示符处。

slime-repl-wrap-history 变量控控制了环绕行为，即是如果到了末尾那么是否应该重新跳转到历史的最开头。

### 8.2.3 快捷命令

“快捷命令”是一组通过名称调用的 REPL 命令。要调用一个快捷命令，你需要先在 REPL 提示符后输入一个逗号，然后再输入命令的名称来执行。

快捷命令处理一些类似于切换目录和加载编译 Lisp 系统的事务。快捷命令在下面列出，或者你可以通过 help 快捷命令来交互式地列出来。

- change-directory (aka !d, cd)

  改变当前目录

- change-package (aka !p, in, in-package)

  改变当前包

- compile-and-load (aka cl)

  编译（如果）并加载一个 Lisp 文件

- defparameter (aka !)

  定义一个新的全局的特殊的变量

- disconnect

  关闭所有连接

- help (aka ?)

  显示帮助

- pop-directory (aka -d)

  弹出当前目录

- pop-package (aka -p)

  弹出包栈的顶端元素

- push-directory (aka +d, pushd)

  将一个新的目录推到目录栈里

- push-package (aka +p)

  将一个包推到包栈里

- pwd

  显示当前目录

- quit

  退出 Lisp

- resend-form

  再次发送最后的形式

- restart-inferior-lisp

  重启 *inferior-lisp* 并重新连接 Slime

- sayoonara

  退出所有 Lisp 并关闭所有 Slime 缓冲区

## 8.3 多 REPL

slime-mrepl 扩展包为多监听者缓冲区提供了支持。M-x slime-open-listener 命令创建一个新的缓冲区。在多线程 Lisp 里，每一个监听者都与一个单独的线程相连。在单线程 Lisp 里，创建多监听者缓冲区也是可以的，但是其命令都是在同一个进程里顺序执行的。

## 8.4 inferior-slime-mode

inferior-slime-mode 是一个用来与 *inferior-lisp* 缓冲区一起使用的次模式。它提供了一些 Slime 命令，例如符号补全和文档查询。它也跟踪 Lisp 进程的当前目录。将以下代码加入.emacs 配置来使用它：

```emacs-lisp
  (slime-setup '(inferior-slime-mode))
```

- M-x inferior-slime-mode

  打开或关闭 inferior-slime-mode

inferior-slime-mode-map 变量包含了额外的键绑定

## 8.5 混合补全

slime-c-p-c 扩展包提供了不同的符号补全算法，它通过中划线分割的符号名 [[1]](#1-1) 的单词子串来进行“并行”的补全。形式上来讲，“a-b-c”可以补全任何匹配“^a.*-b.*-c.*”正则表达式的符号（“圆点”匹配任何除了中划线之外的东西）。下面的例子会给你更直观的感觉：

- m-v-b 补全为 multiple-value-bind。

- w-open 稍有歧义：它可以补全 with-open-file 或 with-open-stream。它会扩展到最长的相同匹配（with-open-）然后光标会停留在有歧义的第一个字符处，在这里就是最后一个单词处。

- w--stream 扩展为 with-open-stream

slime-c-p-c-unambiguous-prefix-p 变量定义了在补全符号后光标应该置于何处。例如 f-o 可能的补全是 finish-output 和 force-output，默认情况下光标会移动到 f 后面，因为这里是明确的前缀。如果 f-o are finish-output and force-output 是 nil，光标会到插入的文本的最后，在这里就是在 o 之后。

除此之外，slime-c-p-c 也为字符名提供补全（对很多可以识别 Unicode 的 Lisp 实现来讲通常很有用）：

```sh
CL-USER> #\Sp<TAB>
```

在这里 Slime 会将其补全为 `#\Space`，但在一个可以识别 Unicode 的实现里，就可能会有以下的补全：

```
Space                              Space
Sparkle                            Spherical_Angle
Spherical_Angle_Opening_Left       Spherical_Angle_Opening_Up
```

slime-c-p-c 扩展包也提供了对关键字的大小写敏感的补全。例如：

```
CL-USER> (find 1 '(1 2 3) :s<TAB>
```

在这里 Slime 会补全为:start，而不是将所有以:s 开头的关键字列出来。

- C-c C-s 或 M-x slime-complete-form

如果有的话，将当前光标处的函数的参数列表列出来并插入缓冲区。更加一般地，此命令给不完全的形式的缺失参数提供了一个模板。对于发现泛函数的额外参数，处理 make-instance、defmethod 和其它很多函数来说有特殊的代码，例如：

```emacs-lisp
(subseq "abc" <C-c C-s>
         --inserts--> start [end])
(find 17 <C-c C-s>
         --inserts--> sequence :from-end from-end :test test
         :test-not test-not :start start :end end
         :key key)
 (find 17 '(17 18 19) :test #'= <C-c C-s>
          --inserts--> :from-end from-end
          :test-not test-not :start start :end end
          :key key)
 (defclass foo () ((bar :initarg :bar)))
 (defmethod print-object <C-c C-s>
            --inserts-->   (object stream)
            body...)
 (defmethod initialize-instance :after ((object foo) &key blub))
 (make-instance 'foo <C-c C-s>
                 --inserts--> :bar bar :blub blub initargs...)
```

## 8.6 模糊补全

slime-fuzzy 扩展包提供了另一种符号补全方式。

[最好有人描述一下这种算法到底是做什么的]

它尝试一次性补全整个符号，而不是只补全一部分。例如，“mvb” 会补全为 “multiple-value-bind”，“norm-df”会补全为 “least-positive-normalized-double-float”。

这种算法尝试以不同的方式扩展每一个字符，然后以下列的方式将所有可能的补全排序列出。

根据在字符串里的位置，字母会被赋予一个权值。字符串最开头，或者是前缀字母之后的字母的权值是最高的。分隔符之后的字符，例如 `#\-`，权值是次高的。字符串最后或者是后缀字母之前的字母有中等权值，其它地方的字母的权值最低。

如果一个字母在另一个匹配字母之后，它在此处的可能性就比之前字母的可能性低，所以就会使用之前的可能性。

最后，一个偏好因子会作用于一些常用的较短的匹配，其它的东西都是一样的。

- C-c M-i 或 M-x slime-fuzzy-complete-symbol

  根据当前光标处的缩写列出所有可能的补全。如果你将变量 slime-complete-symbol-function 的值设为这个命令，则可以通过 M-TAB 使用模糊补全。

## 8.7 slime-autodoc-mode

Autodoc 模式是一个用来自动显示光标附近符号的相关信息的 minor-mode。对于函数名，参数列表会被显示，对于全局变量，则显示它的值。Autodoc 是通过 Emacs 的 eldoc-mode 来实现的。

该模式可以通过你~/.emacs 文件里的 slime-setup 调用来默认开启：

```emacs-lisp
(slime-setup '(slime-autodoc))
```

- M-x slime-arglist NAME

  显示函数 NAME 的参数列表

- M-x slime-autodoc-mode

  根据参数的值开启或关闭 autodoc-mode。当没有参数时，触发该模式。

如果变量 slime-use-autodoc-mode 被设置（默认情况），Emacs 会启动一个计时器，否则信息只会在按 SPC 之后显示。

## 8.8 ASDF

ASDF 是一个流行的“系统构建工具”。slime-asdf 扩展包提供了一些命令来从 Emacs 里加载和编译这些系统。ASDF 本身没有被包含在 Slime 里，你必须自己把它加载到 Lisp 里。还有，你必须在连接之前加载 ASDF，否则你会收到关于符号缺失的错误。

- M-x slime-load-system NAME

  编译并加载 ASDF 系统。默认的系统名字是从当前目录下第一个符合 *.asd 的文件里获得的。

- M-x slime-open-system NAME &optional LOAD

  打开系统里的所有文件，如果 LOAD 不是 nil 的话则加载进来。

- M-x slime-browse-system NAME

  使用 Dired 浏览系统里的所有文件。

该扩展包也加载了一些新的 REPL 快捷命令（见 8.2.3 快捷命令）；

- load-system

  编译（根据需要）并加载一个 ASDF 系统

- compile-system

  编译（但不加载）一个 ASDF 系统

- force-compile-system

  重新编译（但不加载）一个 ASDF 系统

- force-load-system

  重新编译并加载一个 ASDF 系统

- open-system

  打开系统里的所有文件

- browse-system

  使用 Dired 打开系统里的所有文件

## 8.9 导航条

slime-banner 扩展包在当前 REPL 缓冲区安装一个位于窗口顶端的横条。开始的时候还会播放一段动画。

通过将 slime-startup-animation 设置为 nil，你可以关闭动画，而 slime-header-line-p 可以设置横条。

## 8.10 编辑命令

slime-editing-commands 扩展包提供了一些命令来编辑 Lisp 表达式。

- C-c M-q 或 M-x slime-reindent-defun

  重新缩进当前的 defun，或者重排当前段落。如果光标在一段注释里，那么光标附近的文本会被当做一个段落，然后用 fill-paragraph 重排。否则，它会被当做 Lisp 代码，当前 defun 会被重新缩进。如果当前 defun 有没匹配的括号，在重新缩进前会尝试修复。

- C-c C-] 或 M-x slime-close-all-parens-in-sexp

  补全当前光标处未闭合的 S 表达式的括号。插入足够多的右括号，使得跟它的左括号数量匹配。删除多余的左括号，将结尾处的括号格式化为 Lisp 形式。

  如果 REGION 是 true，对该区域操作。否则对顶层环境光标前的表达式操作。

- M-x slime-insert-balanced-comments

  在包含光标的表达式里插入对称的注释。如果该命令被重复调用（多次调用之间没有其它命令了），注释逐渐从里面的表达式向外扩展。如果调用的时候有前缀参数，S 表达式的参数列表会有一个对称的注释。

- M-C-a 或 M-x slime-beginning-of-defun

- M-C-e 或 M-x slime-end-of-defun

## 8.11 更好的检查器

有一个默认检查器的替代物，由 slime-fancy-inspector 扩展包提供。该检查器更加了解 CLOS 对象和方法。它提供很多用来使 Lisp 代码检查对象的行为。例如，为了展示一个泛函数，检查器会以纯文本的形式显示其文档，而对于每个方法则会列出它的超链接和一个你可以调用的“除去该方法”行为。它的键绑定跟默认检查器是一样的。

## 8.12 对象描述

在 Slime 里，一个“对象描述” [[2]](#2-1) 指的是跟一个 Lisp 对象有关的一块文本。右键点击文本会弹出操作该对象的一个菜单。有些操作，例如查看，对所有对象都适用，但对象也可以有自己特有的操作。例如，路径对象有 Dired 相关的操作。

更加重要的是，可以使用所有标准的 Emacs 命令来剪切和粘贴这些描述（也就是 Lisp 对象，而不仅仅是打印出来的样子）。通过这种方式，可以剪切和粘贴 REPL 里之前计算出来的结果。这对不可读对象来说十分重要。

slime-presentations 扩展包在 REPL 里安装这种对象描述，也就是求值命令的结果会被显示出来。使用这种方法，相关描述会生成标准 Common Lisp REPL 历史变量 `*`，`**`，`***` 的用法。例如：

```
CL-USER> (find-class 'standard-class)
#<STANDARD-CLASS STANDARD-CLASS>
CL-USER>
```

在缓冲区里描述会以红色显示。使用标准的 Emacs 命令，描述可以被复制进 REPL 内的一个新的输入里：

```
CL-USER> (eql '#<STANDARD-CLASS STANDARD-CLASS> '#<STANDARD-CLASS STANDARD-CLASS>)
T
```

当你复制了一个不完整的描述，或者编辑描述里的文本，该描述会变为纯文本，丢失与 Lisp 对象之间的关联。在缓冲区里，这会通过其颜色从红色变回黑色来表示，而且不能撤销。

对象描述也可以在查看器（所有可以查看的部分都是对象描述）和调试器（所有的本地变量都是对象描述）里使用。这样就可以使用出现在调试窗口里的对象来在 REPL 里求值。这比使用 M-x sldb-eval-in-frame 更加方便。警告：从查看器和调试器而来的对象只在相关窗口打开的时候才是可用的。否则的话会引起错误或者混淆。

对于某些 Lisp 实现，你还可以安装 slime-presentation-streams 包，它让对象描述适用于 *standard-output* 流和其它流。这意味着不只是计算的结果，而是某些对象都可以通过与对象描述相关联来打印到标准输出（作为计算的副作用）。目前所有的不可读对象和路径都被作为对象描述打印出来。

```sh
CL-USER> (describe (find-class 'standard-object))
#<STANDARD-CLASS STANDARD-OBJECT> is an instance of
    #<STANDARD-CLASS STANDARD-CLASS>:
  The following slots have :INSTANCE allocation:
    PLIST                   NIL
    FLAGS                   1
    DIRECT-METHODS          ((#<STANDARD-METHOD
                                SWANK::ALL-SLOTS-FOR-INSPECTOR
                                (STANDARD-OBJECT T)>
```

这也使得可以复制粘贴、查看这些对象。

除了标准 Emacs 命令，还有一些键盘命令，一个 menu-bar 菜单，一个上下文菜单来操作对象描述。我们在下面解释了这些键盘命令，它们也可以通过 menu-bar 访问。

- C-c C-v SPC 或 M-x slime-mark-presentation

  如果光标在描述内，将其移到描述的最前并标记其末尾。这样就可以复制该描述。

- C-c C-v w 或 M-x slime-copy-presentation-at-point-to-kill-ring

  如果光标在描述内，将该描述复制到 kill ring 里。

- C-c C-v r 或 M-x slime-copy-presentation-at-point-to-repl

  如果光标在描述内，将该描述复制到 REPL 里。

- C-c C-v d 或 M-x slime-describe-presentation-at-point

  如果光标在描述内，显示相关对象的注释。

- C-c C-v i 或 M-x slime-inspect-presentation-at-point

  如果光标在描述内，在 Slime 查看器里查看该对象。

- C-c C-v n 或 M-x slime-next-presentation

  将光标移到缓冲区里的下一个描述处。

- C-c C-v p 或 M-x slime-previous-presentation

  将光标移到缓冲区里的上一个描述处。

相关的操作也可以在每一个描述的上下文菜单里找到。在一个描述处单击 mouse-3 打开上下文菜单会，会显示可用的命令。对于某些对象，某些特别的命令也是可用的。用户可以通过给 swank::menu-choices-for-presentation 定义方法来定义特殊的命令。

警告：对于没有弱哈希表的 Lisp 实现，所有跟对象描述相关联的对象都被垃圾回收保护起来。如果你的 Lisp 进程因此变得太大，使用 C-c C-v M-o（slime-clear-presentations）断开这些关联，这会清空 REPL 缓冲区，并且断开所有对象描述的关联。

警告：对象描述可能让新用户迷惑。

```sh
CL-USER> (cons 1 2)
(1 . 2)
CL-USER> (eq '(1 . 2) '(1 . 2))
T
```

可能有人会期望结果是 nil，因为这看起来像是两个新创建的 cons 在相互比较，而忽视了它们的对象身份。但是在上例中，对象描述 (1 . 2) 是被两次复制到 REPL 里的，所以 eq 确实是作用在相同的对象上的，也就是之前输入到 REPL 里的 cons 对象。

## 8.13 打印窗口

打印窗口是一个特殊的 Emacs 窗口，用来代替显示区域（mini 缓冲区）来显示 Slime 命令的信息。这是一个可选的特性。跟显示区域相比，打印窗口的优势是可以显示更多的文本，可以被滚动，而且当你按键时内容不会消失。所有可能的较长的信息都会被发送到打印窗口，例如参数列表、宏展开等等。

- M-x slime-ensure-typeout-frame

  保证打印窗口存在，如果需要就新建一个。

如果打印窗口关闭那么会重新使用显示区域。

如果要在启动时自动创建一个打印窗口，需要加载 slime-typeout-frame 扩展包。（见 8.1 加载扩展包）

slime-typeout-frame-properties 变量指定了打印窗口的长度和其它可能的特性。它的值会传给 make-frame。

## 8.14 TRAMP

slime-tramp 扩展包提供了一些为 TRAMP 进行文件名转换的函数。（见 7.1.3 设置路径名翻译）

## 8.15 文档链接

对于某些错误信息，SBCL 包含了 ANSI 标准或者 SBCL 用户手册相关的参考。slime-references 扩展包将这些参考变为可以点击的链接。这使得在 HyperSpec 里找到这些参考相关的章节更加容易。

## 8.16 交叉引用和类查看器

slime-xref-browser 扩展包提供了一个基础的类查看器。

- M-x slime-browse-classes

  该命令需要一个类的名字，它会显示出类的所有继承关系。

- M-x slime-browse-xrefs

  该命令显示一个符号及其交叉引用，即它的调用者。以该符号为根的引用树会在之后显示出来。

## 8.17 高亮编辑

slime-highlight-edits 是一个用来高亮显示 Lisp 源代码里被修改了的部分的 minor 模式。这对于快速找到那些需要重新编译（用 C-c C-c）的函数十分有用。

- M-x slime-highlight-edits-mode

  打开或关闭 slime-highlight-edits-mode

## 8.18 空白缓冲区

由 slime-scratch 扩展包提供的 Slime 的空白缓冲区，模仿 Emacs 的 *scratch* 缓冲区。如果 slime-scratch-file 被设置，它被用来备份空白缓冲区，使其变得可持久。它跟其它的 Lisp 缓冲区是一样的，除了绑定到 C-j 的命令。

- C-j 或 M-x slime-eval-print-last-expression

  对光标前的表达式求值，并将结果插入到当前缓冲区里。

- M-x slime-scratch

  创建一个 *slime-scratch* 缓冲区。在此缓冲区里你可以输入 Lisp 表达式并用 C-j 来求值，类似于 Emacs 的 *scratch* 缓冲区。

## 8.19 slime-sprof

slime-sprof 扩展包用来集成 SBCL 的静态分析器，sb-sprof。

slime-sprof-exclude-swank 变量控制是否显示 swank 函数，默认值是 nil。

- M-x slime-sprof-start

  开始分析。

- M-x slime-sprof-stop

  停止分析。

- M-x slime-sprof-browser

  报告分析结果

下面的命令在 slime-sprof-browser 模式里定义：

- RET 或 M-x slime-sprof-browser-toggle

  打开或折叠函数的详细信息（调用者、调用）

- v 或 M-x slime-sprof-browser-view-source

  查看函数源码

- d 或 M-x slime-sprof-browser-disassemble-function

  拆开该函数

- s 或 M-x slime-sprof-toggle-swank-exclusion

  标记 swank 函数使其不在报告里

## 8.20 元包 slime-fancy

slime-fancy 包是一个用来加载那些最受欢迎的包的元包。

## 脚注

### [1]

这种类型的补全由 Chris McConnell 在 completer.el 里被构建。该包跟 ILISP 绑定在一起。

### [2]

对象描述是来自于 Lisp 机的特性。可以通过定义 present 方法来适用于不同的设备，例如将对象绘到位图显示屏或者将文本写到字符流。

# 九、致谢

## 黑客们

Slime 是由 Eric Marsden 写的 Slim 的扩展版。在撰写的时候，Slime 的作者和核心贡献者是：

<table>
<tbody><tr align="left"><td valign="top" width="33%">Helmut Eller </td><td valign="top" width="33%">Tobias C. Rittweiler </td><td valign="top" width="33%">Luke Gorrie
<br></td></tr><tr align="left"><td valign="top" width="33%">Matthias Koeppe </td><td valign="top" width="33%">Marco Baringer </td><td valign="top" width="33%">Alan Ruttenberg
<br></td></tr><tr align="left"><td valign="top" width="33%">Stas Boukarev </td><td valign="top" width="33%">Nikodemus Siivola </td><td valign="top" width="33%">Edi Weitz
<br></td></tr><tr align="left"><td valign="top" width="33%">Juho Snellman </td><td valign="top" width="33%">Peter Seibel </td><td valign="top" width="33%">Martin Simmons
<br></td></tr><tr align="left"><td valign="top" width="33%">Geo Carncross </td><td valign="top" width="33%">Christophe Rhodes </td><td valign="top" width="33%">Attila Lendvai
<br></td></tr><tr align="left"><td valign="top" width="33%">Douglas Crosher </td><td valign="top" width="33%">Mark Evenson </td><td valign="top" width="33%">Daniel Barlow
<br></td></tr><tr align="left"><td valign="top" width="33%">Wolfgang Jenkner </td><td valign="top" width="33%">Michael Weber </td><td valign="top" width="33%">Lawrence Mitchell
<br></td></tr><tr align="left"><td valign="top" width="33%">GÃ¡bor Melis </td><td valign="top" width="33%">LuÃ­s Oliveira </td><td valign="top" width="33%">Brian Downing
<br></td></tr><tr align="left"><td valign="top" width="33%">Bill Clementson </td><td valign="top" width="33%">Andras Simon </td><td valign="top" width="33%">Zach Beane
<br></td></tr><tr align="left"><td valign="top" width="33%">Espen Wiborg </td><td valign="top" width="33%">Antonio Menezes Leitao </td><td valign="top" width="33%">Utz-Uwe Haus
<br></td></tr><tr align="left"><td valign="top" width="33%">Thomas Schilling </td><td valign="top" width="33%">Thomas F. Burdick </td><td valign="top" width="33%">Takehiko Abe
<br></td></tr><tr align="left"><td valign="top" width="33%">Richard M Kreuter </td><td valign="top" width="33%">Matthew Danish </td><td valign="top" width="33%">James Bielman
<br></td></tr><tr align="left"><td valign="top" width="33%">Harald Hanche-Olsen </td><td valign="top" width="33%">Andreas Fuchs </td><td valign="top" width="33%">Willem Broekema
<br></td></tr><tr align="left"><td valign="top" width="33%">Terje Norderhaug </td><td valign="top" width="33%">Taylor R. Campbell </td><td valign="top" width="33%">Stelian Ionescu
<br></td></tr><tr align="left"><td valign="top" width="33%">Raymond Toy </td><td valign="top" width="33%">Lars Magne Ingebrigtsen </td><td valign="top" width="33%">John Paul Wallington
<br></td></tr><tr align="left"><td valign="top" width="33%">Joerg Hoehle </td><td valign="top" width="33%">Bryan O'Connor </td><td valign="top" width="33%">Alan Shutko
<br></td></tr><tr align="left"><td valign="top" width="33%">Travis Cross </td><td valign="top" width="33%">Tobias Rittweiler </td><td valign="top" width="33%">Tiago Maduro-Dias
<br></td></tr><tr align="left"><td valign="top" width="33%">Stefan Kamphausen </td><td valign="top" width="33%">Sean O'Rourke </td><td valign="top" width="33%">Robert Lehr
<br></td></tr><tr align="left"><td valign="top" width="33%">Robert E. Brown </td><td valign="top" width="33%">Nathan Bird </td><td valign="top" width="33%">Mark Harig
<br></td></tr><tr align="left"><td valign="top" width="33%">Jouni K Seppanen </td><td valign="top" width="33%">Ivan Toshkov </td><td valign="top" width="33%">Ian Eslick
<br></td></tr><tr align="left"><td valign="top" width="33%">Gary King </td><td valign="top" width="33%">Eric Blood </td><td valign="top" width="33%">Eduardo MuÃ±oz
<br></td></tr><tr align="left"><td valign="top" width="33%">David Reitter </td><td valign="top" width="33%">Christian Lynbech </td><td valign="top" width="33%">Chris Capel
<br></td></tr><tr align="left"><td valign="top" width="33%">BjÃ¸rn NordbÃ¸ </td><td valign="top" width="33%">Ariel Badichi </td><td valign="top" width="33%">Anton Vodonosov
<br></td></tr><tr align="left"><td valign="top" width="33%">Alexey Dejneka </td><td valign="top" width="33%">Alan Caulkins </td><td valign="top" width="33%">Yaroslav Kavenchuk
<br></td></tr><tr align="left"><td valign="top" width="33%">Wolfgang Mederle </td><td valign="top" width="33%">Wojciech Kaczmarek </td><td valign="top" width="33%">William Bland
<br></td></tr><tr align="left"><td valign="top" width="33%">Tom Pierce </td><td valign="top" width="33%">Tim Daly Jr. </td><td valign="top" width="33%">Sven Van Caekenberghe
<br></td></tr><tr align="left"><td valign="top" width="33%">Svein Ove Aas </td><td valign="top" width="33%">Steve Smith </td><td valign="top" width="33%">StanisBaw Halik
<br></td></tr><tr align="left"><td valign="top" width="33%">Russell McManus </td><td valign="top" width="33%">Rui PatrocÃ­nio </td><td valign="top" width="33%">Robert Macomber
<br></td></tr><tr align="left"><td valign="top" width="33%">R. Matthew Emerson </td><td valign="top" width="33%">Reini Urban </td><td valign="top" width="33%">Pawel Ostrowski
<br></td></tr><tr align="left"><td valign="top" width="33%">Paul Collins </td><td valign="top" width="33%">NIIMI Satoshi </td><td valign="top" width="33%">Neil Van Dyke
<br></td></tr><tr align="left"><td valign="top" width="33%">Mikel Bancroft </td><td valign="top" width="33%">Matt Pillsbury </td><td valign="top" width="33%">Matthew D. Swank
<br></td></tr><tr align="left"><td valign="top" width="33%">MÃ©szÃ¡ros Levente </td><td valign="top" width="33%">Masayuki Onjo </td><td valign="top" width="33%">Mark Wooding
<br></td></tr><tr align="left"><td valign="top" width="33%">Marco Monteiro </td><td valign="top" width="33%">Lynn Quam </td><td valign="top" width="33%">Levente MÃ©szÃ¡ros
<br></td></tr><tr align="left"><td valign="top" width="33%">Lasse Rasinen </td><td valign="top" width="33%">Knut Olav BÃ¸hmer </td><td valign="top" width="33%">Kai Kaminski
<br></td></tr><tr align="left"><td valign="top" width="33%">Julian Stecklina </td><td valign="top" width="33%">Juergen Gmeiner </td><td valign="top" width="33%">Jon Allen Boone
<br></td></tr><tr align="left"><td valign="top" width="33%">Johan BockgÃ¥rd </td><td valign="top" width="33%">Jan Rychter </td><td valign="top" width="33%">James McIlree
<br></td></tr><tr align="left"><td valign="top" width="33%">Ivan Shvedunov </td><td valign="top" width="33%">Ivan Boldyrev </td><td valign="top" width="33%">Ignas Mikalajunas
<br></td></tr><tr align="left"><td valign="top" width="33%">Hannu Koivisto </td><td valign="top" width="33%">Gerd Flaig </td><td valign="top" width="33%">Gail Zacharias
<br></td></tr><tr align="left"><td valign="top" width="33%">Frederic Brunel </td><td valign="top" width="33%">Dustin Long </td><td valign="top" width="33%">Didier Verna
<br></td></tr><tr align="left"><td valign="top" width="33%">Dan Weinreb </td><td valign="top" width="33%">Dan Pierson </td><td valign="top" width="33%">Daniel Koning
<br></td></tr><tr align="left"><td valign="top" width="33%">B.Scott Michel </td><td valign="top" width="33%">Brian Mastenbrook </td><td valign="top" width="33%">Brandon Bergren
<br></td></tr><tr align="left"><td valign="top" width="33%">Bob Halley </td><td valign="top" width="33%">Barry Fishman </td><td valign="top" width="33%">Aleksandar Bakic
<br></td></tr><tr align="left"><td valign="top" width="33%">Alain Picard
   <br></td></tr></tbody></table>

更不要说 hyperspec.el、CLOCC 和 CMU AI Repository 附带而来的代码。

许多在 slime-devel 邮件列表里的人虽然没有写代码但都为 Slime 做出了贡献。但是生活是残酷的：你必须提交代码才能将名字列在这里。:)

## 多谢

我们十分感谢 common-lisp.net 的人帮我们提供空间以及其它帮助，以及从“Sourceforge 地狱”里拯救我们。

我们支持的 Lisp 实现者都很有帮助。我们要感谢 CMUCL 维护者的很有帮助的回答，Franz 的 Craig Norvell 和 Kevin Layer 为 Slime 的开发提供了 Allegro CL 协议，Peter Graves 使 Slime 能跑在 ABCL 上。

对于加入到 Slime 开发里的 Lisp 实现者们，我们表示很高兴跟他们一起工作：Dan Barlow and Christophe Rhodes of SBCL, Gary Byers of OpenMCL, and Martin Simmons of LispWorks. 同时感谢 Alain Picard 以及 Memetrics 赞助 Martin LispWorks 后台的初期工作！
