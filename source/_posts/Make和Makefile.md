---
title: Make和Makefile
date: 2021-03-14 19:29:35
tags:
categories: C/C++
---

<img align="center" height="330px" src="https://pic-1302177449.cos.ap-chongqing.myqcloud.com//blog_pic/20210314211630.png">

<p align="center">
    make和makefile用于方便开发c语言，让编译和管理项目变得简单，最重要的是makefile可以根据时间戳自动判断哪些文件是更改过的，编译时就只用编译需要编译的，大大减少了编译等待的时间（摸鱼的时间）
</p>

<!--more-->

# 介绍

* Makefile的性质和功能
  * makefile是一种脚本语言，通过该目录下的make运行该脚本语言
* 功能：可以自动完成编译工作
  * 如果修改了某几个源文件，则只重新编译这几个源文件
  * 如果某个头文件被修改了，则重新编译所有包含头文件的源文件

# 基本语法和结构

注释：`#`

## 显式规则：

* `目标文件：依赖文件`换行`[TAB]命令`

  * 说明：上面一行是目标和依赖，下面一行为指令，这里需要注意，如果依赖没有就去执行**目标文件为依赖文件**的指令，否则就直接执行，所以通常第一个目标为最终目标

  * 例：

    ```makefile
    hello:hello.o
    	gcc hello.o -o hello
    
    hello.o:hello.s
    	gcc -C hello.s -o hello.o
    
    hello.s:hello.i
    	gcc -S hello.i -o hello.s
    	
    hello.i:hello.c
    	gcc -E hello.c -o hello.i
    ```

    注：这里为什么要分步骤写？

    ​		当某个文件改动时，如果没有中间文件，那么编译时就会重新编译所有文件，非常浪费时间，但有中间文件时，编译会编译修改的文件，相当于只用重新链接一下

* 伪目标：`.PHONY:`换行`参数`换行`[TAB]命令`

  * 说明：make时跟上这些参数就可以执行相应的命令

  * 例：

    ```makefile
    # 使用make clear清除生成的中间文件
    .PHONY:
    clear:
    	rm -rf hello.o hello.s hello.i hello
    ```

* 多文件编译

  * 说明：其实跟单文件步骤差不多

  * 例：

    ```makefile
    last:funciton.o main.o
    	gcc function.o main.o -o last
    function.o:function.c
    	gcc -c function.c -o function.o
    main.o:main.c
    	gcc -c main.c -o main.o
    ```

## 变量

* 简单的替换：=（使用$()取值）

  * 说明：直接用该变量后面的所有内容替换相应的定义

  * 例：

    ```makefile
    TAR = test
    OBJ = circle.o cube.o main.o
    CC := gcc
    
    $(TAR):$(OBJ)
    	$(CC) $(OBJ) -o $(TAR)
    
    circle.o:circle.c
    	$(CC) -c circle.c -o circle.o
    
    ......
    ```

* 追加：+=

  * 说明：在变量后面再加东西，相当于python的两个字符串相加

  * 例：

    ```makefile
    TAR = test
    OBJ = circle.o cube.o main.o
    CC := gcc
    # 添加了新的文件
    OBJ += func.o
    
    $(TAR):$(OBJ)
    	$(CC) $(OBJ) -o $(TAR)
    
    circle.o:circle.c
    	$(CC) -c circle.c -o circle.o
    
    ......
    ```

* 恒等于：:=（即不能追加和修改）

## 隐含规则

* 任意名称：%

  * 说明：使用时直接用%代替任意的名称，常用再目标文件和依赖文件，表示任意文件都执行下述命令

  * 例：

    ```makefile
    TAR = test
    OBJ = circle.o cube.o main.o
    CC := gcc
    
    $(TAR):$(OBJ)
    	$(CC) $(OBJ) -o $(TAR)
    
    %.o:%.c
    	$(CC) -c %.c -o %.o
    ```

* 所有名称：*

  * 说明：用于指定编译该文件夹下的所有文件，如*.c

## 通配符

| 符号 | 含义                                               |
| ---- | -------------------------------------------------- |
| $@   | 所有目标文件                                       |
| $^   | 所有依赖文件                                       |
| $<   | 所有依赖文件的第一个文件                           |
| $+   | 所有的依赖文件，以空格分开，可能包含重复的依赖文件 |
| $*   | 不含拓展名的目标文件名称                           |
| $?   | 所有时间戳比目标文件晚的依赖文件，空格分开         |

# 补充

## c语言编译过程

1. 预处理：
   * 把.h .c展开形成一个文件
   * 宏定义直接替换，库文件直接打开
   * 形成.i文件
   * 对应的gcc指令：gcc -E hello.c hello.i
2. 编译：
   * 生成汇编文件（即.s文件）
   * 对应的gcc指令： gcc -S hello.i -o hello.s
3. 汇编：
   * 将汇编语言转化为机器码
   * 生成二进制文件（编译生成的二进制文件，可由计算机直接执行）
   * 对应的gcc指令： gcc -S hello.i -o hello.s
4. 链接：
   * 生成最终可执行文件（程序）
   * 对应的gcc命令：gcc hello.o -o hello

## gcc其他指令

1. 添加宏定义：`-D`
2. 链接库：`-l`
3. 编译程序位数32位或则64位程序：`-m32`