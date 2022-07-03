---
title: Cmake使用
date: 2021-03-14 19:36:33
tags:
categories: C/C++
---

<img align="center" src="https://pic-1302177449.cos.ap-chongqing.myqcloud.com//blog_pic/20210314211041.png" height="300px">

<p align="center"> 
    众所周知，Makefile可以方便的进行项目的编译等等，可是对于很大的项目（不同文件放在不同地方），要写一个makefile出来可不容易，所以就有了Cmake，它可以用来方便的构建c/c++的makefile，更方便项目的管理
</p>

<!--more-->

# Cmake简介

## 功能

CMake是一个开源的工具，通过编写几行简单的CMakeLists.txt，就可以快速生成Makefile。同时，cmake还支持编译生成库、安装程序到指定目录等操作。

## 安装

### linux安装

一般系统自带，可以通过`cmake --version`查看cmake版本，如果发现没有cmake，则通过包管理工具安装即可，如：

> sudo apt-get install cmake

### windows安装

如果用的cgwin安装的mingw的话，直接从新点开cgwin的安装程序勾选上cmake，安装即可：

![image-20220409130435479](https://pic-1302177449.cos.ap-chongqing.myqcloud.com/blog_picimage-20220409130435479.png)

也可以官网下载：

1. 
官网下载最新的cmake：[CMake](https://cmake.org/)

![image-20220409130816662](https://pic-1302177449.cos.ap-chongqing.myqcloud.com/blog_picimage-20220409130816662.png)

2. 
安装时最好选择添加到环境变量（也可以后面自己添加），如果电脑是自己的，选择添加到所用用户即可

![未命名图片](https://pic-1302177449.cos.ap-chongqing.myqcloud.com/blog_pic%E6%9C%AA%E5%91%BD%E5%90%8D%E5%9B%BE%E7%89%87.png)

3. 在命令行检测是否安装成功：`cmake --version`

# 基本语法和结构

CmakeLists.txt的编写方法通过一个一个的指令构成，通常先指定项目名称、Cmake版本号；再通过添加源文件到一个变量，添加头文件目录、添加链接库、生成可执行文件等步骤，就完成了对一个简单工程的构建。

简单的CmakeLists构成只需要项目名和源文件名、可执行文件名即可

```cmake
PROJECT(main)
ADD_EXECUTABLE(hello main.c)
```

## 基本语法

### 基本原则

1. 变量使用${}取值，IF控制语句中直接使用变量名
2. 关键字(参数一 参数二 …)，参数用圆括号括起来，参数之间使用空格或分号分开
3. 关键字大小写无关，参数和变量区分大小写，推荐大写关键字

### 注意事项

1. SET(SRC_LIST main.cpp)可以写成SET(SRC_LIST “main.cpp”) ，如果有空格则必须加双引号
2. ADD_EXECUTABLE(hello main)，main.cpp的后缀可以不写

## 常用指令

### PROJECT

1. 功能：指定工程名字和支持的语言（默认支持所有语言）。同时，该指令隐式定义了两个变量：
   * _BINARY_DIR，本例中是：HELLO_BINARY_DIR（等于PROJECT_BINARY_DIR），指向当前工作目录。
   * _SOURCE_DIR，本例中是：HELLO_SOURCE_DIR（等于PROJECT_SOURCE_DIR），指向当前工作目录。
2. 参数：
   * 工程名
   * 语言

```cmake
PROJECT (HELLO)             # 支持所有语言
PROJECT (HELLO C CXX)  		# 支持c和c++
```

### SET

1. 功能：显式的指定变量，一般用于将多个源文件用一个变量表示，也可以用于设置某些变量（如目录）的值
2. 参数：
   * 变量名
   * 添加的文件

```cmake
SET(SRC_LIST main.cpp hello.cpp)  # SRC_LIST包含了main.cpp和hello.cpp
SET(EXECUTABLE_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/bin)  # 设置bin文件生成路径
```

### ADD_EXECUTABLE

1. 功能：生成可执行文件
2. 参数：
   * 可执行文件名
   * 源文件，通常结合上一个SET命令创建的文件列表

```cmake
ADD_EXECUTABLE(hello ${SRC_LIST})  # 前面为可执行文件的名字，后面为源文件
```

### ADD_SUBDIRECTORY

1. 功能：向当前工程添加存放文件的子目录（一般用在最外层CMakeLists.txt上）
   * 说明：cmake可以在每个目录下面都写CMakeLists.txt，写上这个目录中要干的事情，一般是编译哪些文件之类的。然后最外层的CMakeLists.txt通过指令将其他层目录加入进来，里面的CMakeLists.txt也会执行。
2. 参数：
   * source_dir：源文件目录
   * bin_dir：可选，生成的二进制文件目录
   * EXCLUDE_FROM_ALL：可选，将source_dir目录从源文件中删除

```cmake
ADD_SUBDIRECTORY(src bin)
```

### INCLUDE_DIRECTORIES

1. 功能：添加头文件
2. 参数：
   * 头文件目录，可以多个

```cmake
include_directories(Core/Inc Drivers/STM32F1xx_HAL_Driver/Inc
                    Drivers/STM32F1xx_HAL_Driver/Inc/Legacy
                    Drivers/CMSIS/Device/ST/STM32F1xx/Include
                    Drivers/CMSIS/Include
                    Drivers/GUI
                    Drivers/GUI/lvgl
                    Drivers/GUI/lv_examples
                    Drivers/GUI/lvgl_driver
                    Drivers/GUI_APP)
```

### LINK_DIRECTORIES

1. 功能：添加链接库的目录，相当于gcc的-L选项
2. 参数：
   * 库目录，可以多个

``` cmake
LINK_DIRECTORIES("/opt/MATLAB/R2012a/bin/glnxa64")
```

### TARGET_LINK_LIBRARIES

1. 功能：链接库，加在ADD_SUBDIRECTORY后面。
2. 参数：
   * 可执行文件，链接生成库的位置
   * 库名，可以有多个

```cmake
target_link_libraries(vipc vips)
target_link_libraries(vipc glib-2.0)
target_link_libraries(vipc gobject-2.0)
```

### FILE

1. 功能：文件操作，可以用来读文件之类的。我主要用来递归添加所有源文件

2. 参数：
   * 操作：可以选择递归获得源文件，即GLOB_RECURSE
   * 变量名
   * 目录
   
   ```cmake
   # 导入源文件
   file(GLOB_RECURSE SOURCE_DRIVERS ${PROJECT_SOURCE_DIR}/src/lv_sim_vscode_sdl/GUI/lv_drivers/*.c)
   file(GLOB_RECURSE SOURCE_EXAMPLES ${PROJECT_SOURCE_DIR}/src/lv_sim_vscode_sdl/GUI/lv_examples/src/*.c)
   file(GLOB_RECURSE SOURCE_LVGL ${PROJECT_SOURCE_DIR}/src/lv_sim_vscode_sdl/GUI/lvgl/src/*.c)
   ```
   
3. 其他操作：[ CMake file文件操作命令_Huo的藏经阁的博客-CSDN博客_cmake file](https://blog.csdn.net/weixin_42730667/article/details/122568165)

### 其他指令

https://www.cnblogs.com/lidabo/p/7359217.html

## 基本结构

cmake管理的工程目录一般包含以下几个部分：

- src：源文件目录
- inc：头文件目录
- lib：库目录
- build：构建生成的文件目录，里面一般包含bin目录，可执行文件一般生成在这个bin目录

通常，cmake生成的文件很多，生成的位置一般是你执行cmake命令的位置，所以一般在工程里面先创建一个build目录，在目录里面执行cmake ..

### 环境变量

cmake有个可以设置独立的环境变量`CMAKE_INCLUDE_PATH`，在Linux下可以用`export CMAKE_INCLUDE_PATH=xxx`，来让这个库加入cmake的环境变量。

# 构建

一般使用`cmake [CMakeLists.txt目录]`就可以完成构建，构建的文件在执行这个命令的目录下

## 注意事项

1. windows下如果用的mingw的话，编译要加上`-G"MinGW Makefiles"`（不然会调用vs的nmake），则编译指令（如果编译过的话就需要删除之前的编译文件重新编译）：

   > cmake -G”MinGW Makefiles” ..

2. 可调试版本生成（可用gdb调试）：

   > cmake .. -DCMAKE_BUILD_TYPE=debug
