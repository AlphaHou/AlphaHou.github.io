---
layout:     post
title:      reading-note-csapp
date:       2018-03-13 21:48:00
author:     "Hou"
header-img: "img/post-bg-computer-science.jpg"
tags:
    - note
    - csapp
---
第七章-链接小结
============

>链接（linking）是将各种代码和数据片段收集并组合成一个单一文件的过程，这个文件可被加载（复制）到内存并执行。链接器使得分离编译（separate compilation）成为可能，这使得我们可以用多个模块组成一个大型应用程序。


### 7.1 编译器驱动程序

![](/img/post/post-2018-02-28-26.jpg)


### 7.2 静态链接

静态连接器以一组可重定位目标文件和命令行参数作为输入，生成一个完全链接的、可以加载和运行的可执行目标文件作为输出。为了构造可执行文件，链接器必须完成两个主要任务：

- 符号解析（symbol resolution）。目标文件定义和引用符号，每个符号对应于一个函数、一个全局变量或一个静态变量。

- 重定位（relocation）。编译器和汇编器生成从地址0开始的代码和数据节。链接器通过把每个符号定义与内存位置关联起来，从而重定位这些节，然后修改所有对这些符号的引用，使得它们指向这个内存位置。


### 7.3 目标文件

目标文件有三种形式。

- 可重定位目标文件。包含二进制代码和数据，其形式可以在编译时与其它可重定位目标文件合并起来，创建一个可执行目标文件。

- 可执行目标文件。包含二进制代码和数据，其形式可以被直接复制到内存并执行

- 共享目标文件。一种特类型的可重定位目标文件，可以在加载或者运行时被动态地加载进内存并链接。

编译器和汇编器生成可重定位目标文件，链接器生成可执行目标文件。一个目标模块（object module）就是一个字节序列，而一个目标文件（object file）就是一个以文件形式存放在磁盘中的目标模块。目标文件是按照特定的目标格式来组织的，各个系统的目标文件格式都不相同。


### 7.4 可重定位目标文件

ELF（Executable and Linkable Format）头以16字节的序列开始，这个序列描述了生成该文件的系统的字的大小和字节顺序。ELF头剩下的部分包含帮助链接器语法分析和解释目标文件的信息。其中包括ELF头的大小、目标文件的类型（如可重定位、可执行或者共享的）、机器类型（如x86-64）、节头部表的文件便宜、以及节头部表中条目的大小和数量。不同节的位置和大小是由节头部表描述的，其中目标文件中每个节都有一个固定大小的条目（entry）。

![](/img/post/post-2018-02-28-27.jpg)


### 7.5 符号和符号表

每个可重定位目标模块m都有一个符号表，它包含m定义和引用的符号的信息。在链接器的上下文中，有三种不同的符号：

- 由模块m定义并能被其他模块引用的全局符号。全局链接器符号对应于非静态的c函数和全局变量。

- 由其他模块定义冰杯模块m引用的全局符号。这些符号称为外部符号，对应于在其它毛孔中定义的非静态C函数和全局变量。

- 只被模块m定义和引用的局部符号。他们对应于带static舒心的C函数和全局变量。这些符号在模块m中任何位置都可见，但是不能被其他模块引用。


###7.6 符号解析

链接器解析符号引用的方法是将每个引用与它输入的可重定位目标文件的符号表中的一个确定的符号定义关联起来。当编译器遇到一个不是在当前模块中定义的符号（变量或函数名）时，会假设该符号是在其他某个模块中定义的，生成一个链接器符号表条目，并把它交给链接器处理。

*7.6.1 链接器如何解析多重定义的全局符号*

链接器的输入是一组可重定位目标模块。

在编译时，编译器向汇编器输出每个全局符号，或者是强（strong）或者是弱（weak），而汇编器把这个信息隐含地编码在可重定位目标文件的符号表里。函数和已初始化的全局变量是强符号，未初始化的全局变量是弱符号。

对于多重定义的符号名，Linux有以下规则;

- 不允许有多个同名的强符号。

- 如果有一个强符号和多个弱符号同名，那么选择强符号。

- 如果有多个弱符号同名，那么从这些弱符号中任意选择一个。

*7.6.2 与静态库链接*

所有的编译系统都提供一种机制，将所有相关的目标模块打包成为一个单独的文件，称为静态库（static libarary），它可以用作链接器的输入。

![](/img/post/post-2018-02-28-28.jpg)


### 7.7 重定位

*7.7.1 重定位条目*

当汇编器生成一个目标模块时，它并不知道数据和代码最终将放在内存中的什么位置。它也不知道这个模块引用的任何外部定义的函数或者全局变量的位置。所以，无论何时汇编器遇到对最终位置未知的目标引用，它就会生成一个重定位条目，告诉链接器在将目标文件合并成可执行文件时如何修改这个引用。代码的重定位条目放在.rel.text中。已初始化数据的重定位条目放在.rel.data中。

*7.7.2 重定位符号引用*


### 7.8 可执行目标文件

![](/img/post/post-2018-02-28-29.jpg)


### 7.9 加载可执行目标文件

![](/img/post/post-2018-02-28-30.jpg)


### 7.10 动态链接共享库

静态库目前的问题是需要不断维护更新，以及标准/O函数经常使用，每次都要复制到每个运行进程的文本段中是对内存系统资源的极大浪费。

共享库（shared library）是一个目标模块，在运行或加载时，可以加载到任意的内存地址，并和一个在内存中的程序链接起来。这个过程称为动态链接（dynamic linking），是由一个叫做动态链接器（dynamic linker）的程序来执行的。共享库也成为共享目标i（shared object）。微软的操作系统大量地使用共享库，它们成为DDL（动态链接库）

![](/img/post/post-2018-02-28-31.jpg)


### 7.11 从应用程序中加载和链接共享库

应用程序可能在它运行时要求动态链接器加载和链接某个共享库，而无需在编译时将那些库链接到应用中。


### 7.12 位置无关代码

利用位置无关代码，无限多个进程可以共享一个共享模块的代码段的单一副本。

在一个x86-64系统中，对同一个目标模块中符号的引用是不需要特殊处理使之成为PIC（Position-Independent Code）。

1. PIC数据引用

想要生成对全局变量PIC引用的编译器利用了无论在内存中的何处加载一个目标模块，数据段与代码段的距离总是保持不变的事实，在数据段开始的地方创建了一个表，叫做全局偏移量表（Global Offset Table）。在GOT中，每个被这个目标模块引用的全局数据目标都有一个8字节条目。

2. PIC函数调用

把函数地址的解析推迟到它实际被调用的地方，能避免动态链接器在加载时进行成百上千个其实并不需要的重定位，这叫延迟绑定（lazy binding）


### 7.13 库打桩机制

库打桩（library interpositioning）是指，截获对共享库函数的调用，取而代之执行自己的代码。打桩可以发生在编译时，链接时或当程序被加载和执行的运行时。



