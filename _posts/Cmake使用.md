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

# 基本语法和常用指令

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
project (HELLO)             # 支持所有语言
project (HELLO C CXX)  		# 支持c和c++
```

### SET

1. 功能：显式的指定变量，一般用于将多个源文件用一个变量表示，也可以用于设置某些变量（如目录）的值
2. 参数：
   * 变量名
   * 添加的文件

```cmake
set(SRC_LIST main.cpp hello.cpp)  # SRC_LIST包含了main.cpp和hello.cpp
set(EXECUTABLE_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/bin)  # 设置bin文件生成路径
```

### ADD_EXECUTABLE

1. 功能：生成可执行文件
2. 参数：
   * 可执行文件名
   * 源文件，通常结合上一个SET命令创建的文件列表

```cmake
add_executable(hello ${SRC_LIST})  # 前面为可执行文件的名字，后面为源文件
```

### ADD_SUBDIRECTORY

1. 功能：向当前工程添加存放文件的子目录（一般用在最外层CMakeLists.txt上）
   * 说明：cmake可以在每个目录下面都写CMakeLists.txt，写上这个目录中要干的事情，一般是编译哪些文件之类的。然后最外层的CMakeLists.txt通过指令将其他层目录加入进来，里面的CMakeLists.txt也会执行。
2. 参数：
   * source_dir：源文件目录
   * bin_dir：可选，生成的二进制文件目录
   * EXCLUDE_FROM_ALL：可选，将source_dir目录从源文件中删除

```cmake
add_subdirectory(src bin)
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
link_directories("/opt/MATLAB/R2012a/bin/glnxa64")
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

其他操作：[ CMake file文件操作命令_Huo的藏经阁的博客-CSDN博客_cmake file](https://blog.csdn.net/weixin_42730667/article/details/122568165)

### MESSAGE

1. 功能：打印消息
2. 参数：

### 其他指令

https://www.cnblogs.com/lidabo/p/7359217.html

## 环境变量

cmake有个可以设置独立的环境变量`CMAKE_INCLUDE_PATH`，在Linux下可以用`export CMAKE_INCLUDE_PATH=xxx`，来让这个库加入cmake的环境变量。

1. 跟`project`有关的环境变量
   * `{PROJECT_NAME}_BINARY_DIR`：
   * `{PROJECT_NAME}_SOURCE_DIR`：
2. `bin`文件生成目录的环境变量
   * `EXECUTABLE_OUTPUT_PATH`：可以通过`set`命令修改
3. 安装相关的环境变量
   * `CMAKE_INSTALL_PREFIX`：安装路径，可以通过`set`命令指定，也可以在`cmake`时用`-D CMAKE_INSTALL_PREFIX=xxx`指定，默认是在`usr/local`
4. 跟系统环境变量有关：
   * `CMAKE_INCLUDE_PATH`：可以将自己的头文件写在里面
   * `CMAKE_LIBRARY_PATH`：可以将自己的库写在里面

# 构建和结构

一般使用`cmake [CMakeLists.txt目录]`就可以完成构建，构建的文件在执行这个命令的目录下

## 内部构建和外部构建

内部构建就是在本目录下直接构建，比如`cmake .`，这样构出来很多临时文件和自己的文件混在一起，如下图所示，不方便项目的构建和管理。

![image-20221202164106227](https://pic-1302177449.cos.ap-chongqing.myqcloud.com/blog_pic/image-20221202164106227.png)

而外部构建一般是指将生成的文件放在另一个目录（即不和CMakeLists.txt一个目录），一般创建一个文件夹，再在里面进行编译：

```shell
mkdir build
cd build
cmake ..
make
```

这样生成的文件就会在`build`下面，使整个工程看起来更整洁。

![image-20221202170032146](https://pic-1302177449.cos.ap-chongqing.myqcloud.com/blog_pic/image-20221202170032146.png)

## 基本结构

cmake管理的工程目录按照不同习惯有很多种，根据实际开发或需要做调整，常见的结构如下：

- src：源码目录
- doc：说明文档，可以通过cmake安装到`/usr/share/doc/cmake/`目录下
- COPYRIGHT,README：版权和说明，同样可以安装到上述目录下
- build：构建生成的文件目录，里面一般包含bin目录，可执行文件一般生成在这个bin目录

构建好的基本结构如下所示：

![image-20221202172604136](https://pic-1302177449.cos.ap-chongqing.myqcloud.com/blog_pic/image-20221202172604136.png)

这里使用了多级目录下的`CMakeLists.txt`，最外层的`CMakeLists.txt`如下所示：

```cmake
# 定义工程名
project(main)
# 关联子目录，并设置生成的东西放入bin目录
add_subdirectory(src bin)
```

`src`目录下的`CMakeLists.txt`如下所示：

```cmake
# 子目录中的CMakeLists.txt只用管该目录下需要生成的文件（也可以设置工程名）
set(SRC_LIST helloworld.cc)
add_executable(hello ${SRC_LIST})
```

## 使用CMake来安装

安装一般是指将编译好的文件安装（或者说是移动）到某个目录，一般有两种方法指定目录：

* 简单的指定目录（安装时指定）：`make install DESTDIR=/tmp/test`
* 高级的指定目录（生成`makefile`时指定）：`./configure -prefix=/usr`，后面安装时`make install`

### INSTALL命令简介

安装需要用到`INSTALL`指令，该指令具体介绍在上面，接下来简单介绍一下该指令

1. 功能：用于安装，`INSTALL`的安装可以包括二进制、动态库、静态库以及文件、目录、脚本等
2. 参数：
   * 类型：
     * `FILES`：安装文件，后面根安装的文件名
     * `PROGRAMS`：非目标文件的可执行程序安装（比如脚本文件）
     * `DIRECTORY`：安装目录，后面根目录名`abc`表示`abc`被安装到目标目录，`abc/`表示`abc`里面的问题被安装到目标目录
     * `TARGETS`：安装目标文件，一般要和生成这个目标文件的`CMakeLists.txt`在同一级目录下，否则会找不到目标文件
   * `DESTINATION`：安装位置，一般需要写绝对路径，也可以写相对路径，会自动在前面加上默认路径的环境变量，比如`${CMAKE_INSTALL_PREFIX}/abc`

### 安装具体操作

在安装前，通常的目录结构如下所示：

![image-20221202174759355](https://pic-1302177449.cos.ap-chongqing.myqcloud.com/blog_pic/image-20221202174759355.png)

然后一般在最外层的`CMakeLists.txt`添加安装命令

```cmake
# 定义工程名
project(main)
# 关联子目录，并设置生成的东西放入bin目录
add_subdirectory(src bin)
# 安装文件
# 将copyright和readme安装在/usr/local/share/doc/cmake下面
install(FILES COPYRIGHT README DESTINATION share/doc/cmake)
# 安装脚本（实际位置为/usr/locar/bin）
install(PROGRAMS runhello.sh DESTINATION bin)
# 安装目录
install(DIRECTORY doc/ DESTINATION share/doc/cmake)
```

然后执行编译，安装：

```shell
cmake ..
make
sudo make install 
```

## 构建共享库

首先，什么是库？库就是别人可以直接调用的，编译好的文件，只需要使用库提供的`.h`文件就可以使用库里面的函数，需要注意的是，使用库必须链接到库，否则会出现`xxx未定义`等错误。

静态库和动态库的区别：

* 静态库拓展名一般为`.a`或`.lib`；动态库拓展名一般为`.so`或`.dll`
* 静态库会直接整合到目标程序中，编译成功的可执行文件可以独立运行
* 动态库编译时不会放到链接的目标程序中，即可执行文件无法单独运行

### ADD_LIBRARY命令简介

1. 功能：创建共享库
2. 参数：
   * 库名：库的名称`name`，生成的库会自动加上`lib`，所以最后的名字为`libname.so`
   * 库类型：
     * `SHARED`：动态库
     * `STATIC`：静态库
   * 源文件

下面以创建`HelloFunc`的库函数为例，建立动态库和静态库，并实现调用。

### 创建操作

首先，正常创建好平时使用的`.h`和`.cc`文件，并完成代码的编写，整体目录结构如下：

![image-20221202195708458](https://pic-1302177449.cos.ap-chongqing.myqcloud.com/blog_pic/image-20221202195708458.png)

完成外层`CMakeLists.txt`的编写，外层编写和前面的基本结构的外层一致，无需其他额外的操作，然后是完成内部的`CMakeLists.txt`的编写，具体如下所示：

```cmake
set(LIBHELLO_SRC hello.cc)
# 创建库
add_library(hello SHARED ${LIBHELLO_SRC})
```

如果要**同时构建动态库和静态库**，一种方法是给动态库和静态库取不同的名称，还有一种方法是**使用`SET_TARGE_PROPERTIES`指令**来重名名名称，该指令还可以指定动态库版本和API，具体如下所示：

```cmake
set(LIBHELLO_SRC hello.cc)

# 创建静态库
add_library(hello_static STATIC ${LIBHELLO_SRC})
# 重命名
set_target_properties(hello_static PROPERTIES OUTPUT_NAME "hello")
# 清除hello这个命名（实际生成的文件没有删除），后面才可以使用hello
set_target_properties(hello_static PROPERTIES CLEAN_DIRECT_OUTPUT 1)

# 创建动态库
add_library(hello SHARED ${LIBHELLO_SRC})
# 重命名
set_target_properties(hello PROPERTIES OUTPUT_NAME "hello")
set_target_properties(hello PROPERTIES CLEAN_DIRECT_OUTPUT 1)
```

生成的文件如下所示：

![image-20221202202215147](https://pic-1302177449.cos.ap-chongqing.myqcloud.com/blog_pic/image-20221202202215147.png)

接下来是**安装共享库**，第一步要安装头文件，第二部要安装库，具体要在内层（不然找不到目标文件）`CMakeLists.txt`里面加上如下两句：

```cmake
install(FILES hello.h DESTINATION include/hello)
# 分别安装动态库和静态库到lib目录下
install(TARGETS hello hello_static LIBRARY DESTINATION lib ARCHIVE DESTINATION lib)
```

然后安装目录修改为`usr/`这样才能保证能够直接调用：

```shell
cmake -D CMAKE_INSTALL_PREFIX=/usr ..
make
sudo make install
```

### 使用步骤

使用时，根据安装的位置不同，需要的工作也不同，头文件如果安装在`/usr/include/`下面（这个是在环境变量中的），那么头文件就可以直接使用。同理，如果库安装在`/usr/lib`下面，库也可以直接链接。

对于未安装在`/usr/include`下面的头文件，需要使用`INCLUDE_DIRECTORIES`将头文件目录加入。

对于库的链接有两种方法：

* `LINK_DIRECTORIES`：添加非标准共享库搜索路径（库没有安装在`usr/lib`下面，有些需要在`usr/lib64`下面）
* `TARGET_LINK_LIBRARIES`：添加标准库，直接写库的名称就行（注意要写在`add_executable`后面）

这里使用前面`HelloFunc`需要的`CMakeLists.txt`如下：

```cmake
# 加入头文件搜索路径
include_directories(/usr/include/hello)

add_executable(hello main.cc)
# 链接库
target_link_libraries(hello libhello.so)
```

这里的库可以使用`find`来找，这里先不做介绍

## FIND_PACKAGE

`find_package`常用来寻找库，得到库的一些信息，然后使用。注意：`find_package`只能寻找用`cmake`安装的库，本质是查找环境变量中对应库的`xxx.cmake`，如果库目录中没有该文件，显然是无法通过`find_package`来找的，不过还好大部分库都是通过`cmake`安装的。

### find_pacakge命令介绍

1. 功能：查找库
2. 参数：
   * `REQUEST`：可选，如果选择，找不到包时就终止`cmake`命令
   * 库：可以有多个

如果找到了想要的库，一般会得到以下几个环境变量：

* `<LibaryName>_FOUND`：返回值，是否找到，可以用在`if`语句进行某些操作
* `<LibaryName>_INCLUDE_DIR`：头文件目录
* `<LibaryName>_INCLUDES`：头文件
* `<LibaryName>_LIBRARY`：库目录
* `<LibaryName>_LIBRARIES`：库

### 使用步骤

首先通过`find_package`找需要的包

```cmake
find_package(CURL)
```

然后添加头文件和链接库

```cmake
target_include_directories(clib PRIVATE ${CURL_INCLUDE_DIR})
target_link_libraries(curltest ${CURL_LIBRARY})
```

# 注意事项

1. windows下如果用的mingw的话，编译要加上`-G"MinGW Makefiles"`（不然会调用vs的nmake），则编译指令（如果编译过的话就需要删除之前的编译文件重新编译）：

   > cmake -G”MinGW Makefiles” ..

2. 可调试版本生成（可用gdb调试）：

   > cmake .. -DCMAKE_BUILD_TYPE=debug

# 参考链接

https://zhuanlan.zhihu.com/p/97369704
