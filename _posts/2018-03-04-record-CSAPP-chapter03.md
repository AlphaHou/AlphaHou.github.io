---
layout:     post
title:      reading-note-csapp
date:       2018-03-04 21:48:00
author:     "Hou"
header-img: "img/post-bg-computer-science.jpg"
tags:
    - note
    - csapp
---
第三章-程序的机器级表示小结
============


### 3.1 历史观点

Intel处理器系列俗称x86。最早，它是第一代单芯片、16位微处理器之一。


### 3.2 程序编码

*3.2.1 机器级代码*

- 程序计数器：给出将要执行的下一条指令在内存中的地址。

- 指数寄存器文件包含16个命名的位置，分别存储64位的值。这些寄存器可以存储地址或整数数据。

- 条件码寄存器保存着最近执行的算术或逻辑指令的状态信息。它们用来实现控制或数据流中的条件变化。

- 一组向量寄存器可以存放一个或多个整数或浮点数值。

### 3.4 访问信息

*3.4.1 操作数指示符*

大多数指令有一个或多个操作数（operand），指示出执行一个操作中要使用的源数据值，以及放置结果的目的位置。源数据值可以以常数形式给出，或是从寄存器或内存中读出。结果可以存放在寄存器或内存中。因此各种不同的操作数的可能性被分为三种类型。第一种是立即数（immediate），用来表示常数值。第二种类型是寄存器（register），它表示某个寄存器的内容，第三种操作数是内存引用，它会根据计算出来的地址访问某个内存地址。

![](/img/post/post-2018-02-28-10.jpg)

*3.4.2 数据传送指令*

最频繁使用的指令是将数据从一个位置复制到另一个位置的指令。

![](/img/post/post-2018-02-28-11.jpg)

源操作数制定的值是一个立即数，存储在寄存器中或内存中。目的操作数指定一个位置，要么是一个寄存器，幺妹是一个内存地址

*3.4.4 压入和弹出栈数据*

后进先出的数据结构，可以删除或添加。将一个四字节值压入栈中，首先要将栈指针减8，然后将值写到新的栈顶地址。弹出一个四字节值，首先读取数据，再将栈指针加8。


### 3.5 算术和逻辑操作

*3.5.1 加载有效地址*

leaq（load effective address）指令是从内存读数据到寄存器，但实际上它根本就没有引用内存。它的第一个操作数是将有效地址写入到目的操作数


### 3.6 控制

*3.6.1 条件码*

除了整数寄存器。CPU还维护者一组单个位的条件码（condition code）寄存器，它们描述了最近的算术和逻辑操作的属性。最常用的条件码：

- CF：进位标志。最近的操作使最高位产生了进位。可用来检查无符号操作的溢出。

- ZF：零标志。最近的操作得出的结果为0.

- SF：符号标志。最近的操作得到的结果为负数。

- OF：溢出标志。最近的操作导致一个补码溢出————正溢出或负溢出。

*3.6.6 用条件传送来实现条件分支*

之所以不再使用控制的条件转移，是因为在现代处理器上，它会非常低效。替代的方式是数据的条件转移。这种方法计算一个条件操作的两种结果，然后再根据条件是否满足从中选取一个。而后者优于前者的原因是，前者需要等待分支条件求值完成后才能决定分支往哪边走。

但上述逻辑并不总是正确的。如条件分支会产生副作用、条件分支的运行依赖判断、条件分支的计算量大于预测错误带来的后果。


### 3.7 过程

过程是软件中一种很重要的抽象。它提供了一种封装代码的方式，用一组指定的参数和一个可选的返回值实现了某种功能。然后可以在程序中不同的地方调用这个函数。

*3.7.1 运行时栈*

当x86-64过程需要的存储空间超出寄存器能够存放的大小时，就会在栈上分配空间。这个部分称为过程的栈帧（stack frame）。

*3.7.3 数据传递*

x86-64中，大部分过程间的数据传送是通过寄存器实现的。如果一个函数由大于6个整型参数，超出6个的部分就要通过栈来传递。假设过程P调用过程Q，有n个整型参数，且n>6。那么P的代码分配的栈帧必须要能容纳7到n号参数的存储空间。要把参数1~6复制到对应的寄存器，把参数7~n放到栈上，而参数7位于栈顶。通过栈传递参数时，所有的数据大小都向8的倍数对齐。参数到位以后，程序就可以执行call指令将控制转移到过程Q了。过程Q可以通过寄存器访问参数，有必要的话也可以通过栈访问。

*3.7.4 栈上的局部存储*

大部分时候，局部数据存放在寄存器中，但下列情况，必须存放在内存中：

- 寄存器不足够存放所有的本地数据。

- 对一个局部变量使用地址运算符‘&’，因此必须能够为它产生一个地址。

- 某些局部变量是数组或结构，因此必须能够通过数组或结构引用被访问到。


### 3.8 数组分配和访问


### 3.9 异质的数据结构

*3.9.3 数据对齐*

许多计算机系统对基本数据类型的合法地址做出了一些限制，要求某种类型对象的地址必须是某个值K（通常是2，4，8）的倍数。这种对齐限制简化了形成处理器和内存系统之间借口的硬件设计。


### 3.10 在机器级程序中将控制与数据结合起来

*3.10.3 内存越界引用和缓冲区溢出*

C对于数组引用不进行任何边界检查，而且局部变量和状态信息（例如保存的寄存器值和返回地址）都存放在栈中。这两种情况结合到一起就能导致严重的程序错误，对越界的数组元素的写操作会破坏存储在栈中的状态信息。当程序使用这个被破环的状态，试图重新加载寄存器或执行ret指令时，就会出现严重的错误。

一种特别常见的状态破坏称为缓冲区溢出（buffer overflow）。通常，在栈中分配某个字符数组来保存一个字符串，但是字符串的长度超出了为数组分配的空间（如分配了24个字符，实现方式是指针减24，最后以指针加24的方式把分配的内存取回，这就导致如果字符超过24，加24之后指针指向的仍为字符串的一部分）。

缓冲区溢出的一个更加致命的使用就是让程序执行它本来不愿意执行的函数。通常，输入给程序一个字符串，这个字符串包含一些可执行代码的字节编码，称为攻击代码（exploit code），另外，还有一些字节会用一个指向攻击代码的指针覆盖返回地址。那么执行ret指令的效果就是跳转到攻击代码。

在一种攻击形式中，攻击代码会使用系统调用启动一个shell程序，给攻击者提供一组操作系统函数。在另一种攻击形式中，攻击代码会执行一些未授权的任务，修复对栈的破坏，然后第二次执行ret指令，（表面上）正常返回给调用者。

*3.10.4 对抗缓冲区已出攻击*

1. 栈随机化：为了在系统中插入攻击代码，攻击者既要插入u代码，也要插入指向这段代码的指针，这个指针也是攻击字符串的一部分。产生这个指针需要知道这个字符串放置的栈地址。在过去，程序的栈地址非常容易预测。栈随机化的实现方式是：程序开始时，在栈上分配一段0~n字节之间的随机大小的空间。然而一个执着的攻击者总是能够用蛮力客服随机化，他可以反复地用不同的地址进行攻击。一个产检的把戏就是在实际的攻击代码前插入很长一段的nop（no operation的缩写）指令。执行这种指令除了对程序计数器加一，使之指向吓一跳指令之外，没有任何的效果。只要攻击者能够猜中这段序列中的某个地址，程序就会经过这个序列到达攻击代码。这个序列常用的术语是“空操作雪橇（nop sled）”，意思是程序会“滑过”这个序列。

2. 栈破坏检测：最近的GCC版本在产生的代码中加入了一种栈保护者（stack protector）机制，来检测缓冲区越界。其思想是在栈帧中任何局部缓冲区与栈状态之间存储一个特殊的金丝雀（canary）值。这个金丝雀值，也称为哨兵值（guard value），是在程序每次运行时随机产生的，因此，攻击者没有简单的办法能够知道它是什么。在回复寄存器状态和从函数返回之前，程序检查这个金丝雀值是否被该函数的某个操作或者该函数调用的某个函数的某个操作改变了。如果是的，那么程序异常终止。

3. 限制可执行代码区域：一种方法是限制哪些内存区域能够存放可执行代码。在典型的程序中，只有保存编译器产生的代码的那部分内存才需要是可执行的。其他部分可以被限制为只允许读和写。

