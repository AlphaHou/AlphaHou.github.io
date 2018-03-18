---
layout:     post
title:      reading-note-csapp
date:       2018-03-16 21:48:00
author:     "Hou"
header-img: "img/post-bg-computer-science.jpg"
tags:
    - note
    - csapp
---
第十章-系统级I/O小结
============

### 10.1 Unix I/O

所有的I/O设备都被模型化为文件，而所有的输入和输出都被当做堆想要文件的读和写来实质性。


### 10.2 文件

- 普通文件（regular file）包含任意数据。应用程序常常要区分文本文件（text file）和（binary file），文本文件是只含有ASCII或Unicode字符的普通文件；二进制文件是所有其他的文件。对内核而言，文本文件和二进制文件没有区别。

- 目录（directory）是包含一组链接（link）的文件，其中每个连接都将一个文件名（filename）映射到一个文件，这个文件可能是另一个目录。每个目录至少含有两个条目：“.”是到该目录自身的链接，以及“..”世道目录层次结构中父母路的链接 。

- 套接字（socket）是用来与另一个进程进行跨网络通信的文件。

其他文件类型包含命名通道（named pipe）、符号链接（symbolic link），以及字符和块设备（character and block device）。

作为其上下文的一部分，每个进程都有一个当前工作目录（current working directory）来确定其在目录层次结构中的当前位置。


### 10.8 共享文件

内核用三个相关的数据结构来表示打开的文件：

- 描述符表（descriptor table）。每个进程都有它独立的描述符表，它的表项是由进程打开的文件描述符来索引的。每个打开的描述符表项指向文件表中的一个表项。

- 文件表（file table）。打开文件的集合是由一张文件表来表示的，所有的进程共享这张表。每个文件表的表项组成包括当前的文件位置、引用计数（reference count），以及一个指向v-node表中对应表相的指针。关闭一个描述符会减少相应的文件表表项中的引用计数。内核不会删除这个文件表表项，直到它的计数为零。

- v-node表。同文件表一样，所有的进程共享这张表。每个表项包含stat结构中的大多数信息。

![](/img/post/post-2018-02-28-46.jpg)


