---
title: GDB的使用
date: 2021-02-10 14:19:54
tags:
categories: Linux
---

<p align="center">
    作为一个靠c语言吃饭程序猿，GDB调试程序是必须掌握的
</p>
<!--more-->


# GDB的简介

## GDB用途

GDB是一款程序调试器，可以再程序运行时，一步一步的运行代码从而确定自己的错误发生在哪一行([GDB官网](https://www.gnu.org/software/gdb/))。

GDB可以完成以下事情

1. 启动程序，传入参数
2. 打断点
3. 分析程序停下来时发生了什么
4. 在运行过程中改变事务

## GDB适用语言

GDB支持哪些语言：

* Ada

* Assembly（汇编）

- C
- C++
- D
- Fortran
- Go
- Objective-C
- OpenCL
- Modula-2
- Pascal
- Rust

# GDB的基本使用

## GDB调试前的准备

在gdb使用前，需要先编写好程序，同时启动gdb还要按照如下顺序

1. 查看是否安装好了gdb

   > gdb --version

2. 创建支持GDB调试的程序：

   > gcc -g text.c

3. 启动GDB调试

   > gdb a.out

## GDB的基本命令使用

当gdb开始运行后，进入调试界面，这时候通过输入各种命令就可以让程序实现不同的效果（相当于界面调试时点击的效果，如加断点，全速运行，单行运行）。具体命令如下所示：

| 命令/缩写         | 作用                                                   |
| :---------------- | ------------------------------------------------------ |
| run/r             | 让程序开始运行，或者重启该程序                         |
| quit              | 退出gdb模式                                            |
| break/b+行/函数名 | 添加断点，可以添加都某个函数前，某行前                 |
| list              | 查看程序，每list以下程序就展示向下10行（断点前看行数） |
| next/n            | 往下运行一行                                           |
| info b            | 查看断点的情况                                         |
| print/p 变量名    | 产看变量的信息（可以通过变量名=x的方式实现改变变量值） |
| step              | 进入该行的函数                                         |
| continue/c        | 让程序继续运行                                         |

## GDB的高级功能

GDB除了支持上述调试命令外，还支持一些高级命令

1. 运行shell的命令，在调试时可以执行shell命令查看外面的文件等

   > shell xxxx

2. 日志功能

   日志功能实现了对整个调试过程的记录，方便后续的分析

   > set logging on

3. 观察点

   通过设置观察点可以实现变量改变的监控，比如观察i变化，当程序运行时，监控的变量发生改变程序可以通知到控制台

   > watch *地址 // 监控某个地址的值
   >
   > info watchpoints //查看观察点

# GDB的进阶使用

## GDB调试core文件

1. 什么时core文件

   当一个程序崩溃时，如果在打开core文件生成的情况下，会在指定目录下生成一个core文件，主要用于调试。

2. 如果打开core文件的生成

   core文件生成默认状况下是关闭的，所以要开启core文件的生成权限

   1. 查看当前shell的限制（[ulimit介绍](https://www.runoob.com/linux/linux-comm-ulimit.html)，ps:英语好可以直接看man手册）

      > ulimit -a

   2. 解除对core文件生成的限制

      > ulimit -c unlimited

3. 如何调试core文件（可以直接产看错误的行和原因）

   > gdb xxx(运行的文件) core.xx(core文件)

## GDB调试正在运行的程序

当一个程序一直在运行时，可以通过gdb+进程号直接调试该程序

1. 程序后台运行

   > ./a.out &  // 即在程序后面添加&

2. 查看程序进程号（[ps命令介绍](https://www.runoob.com/linux/linux-comm-ps.html)）

   > ps -ef | grep xxx  // 根据进程的名字xxx查找进程号

3. gdb调试正在运行的进程

   > gdb -p 进程号