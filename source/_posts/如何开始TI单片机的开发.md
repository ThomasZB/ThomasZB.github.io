---
title: 如何开始TI单片机的开发
date: 2020-07-05 20:31:45
tags:
---

## 下载开发环境
1. 准备好[gcc](https://osdn.net/projects/mingw/releases/)和[java](https://www.java.com/en/download/manual.jsp)环境
2. 安装[ccs](http://software-dl.ti.com/ccs/esd/documents/ccs_downloads.html)(Code Composer Studio)

ccs安装的时候选择offline版本的，安装时根据单片机型号选择要安装的开发环境。
## 开始开发
### 点亮开发板上的灯
点灯相当于是单片机开发上的Hello World，在开始设计前，写一个程序点灯看看吧！

1. 在ccs中新建一个工程，选择单片机为对应的型号  
![](https://raw.githubusercontent.com/ThomasZB/picture/master/ccs_newprj.png)
2. 找到.h文件中的接口的定义，以及led灯对应的位置，写上一个流水灯程序

### 了解开发板的结构
大概了解一下开发板由哪些构成，这个可以参考电路图，或则TI开发板的小卡片，上面写各个接口的位置，以及可以复用的功能。如下图所示：  
<img src="https://raw.githubusercontent.com/ThomasZB/picture/master/msp432launchpad.jpg" width = 50% height = 50% />  
### 开始了解时钟树
时钟是单片机的心脏，开发前很有必要了解，在对应型号的手册上有对应的时钟树，以及对应的每一个寄存器。根据寄存器和时钟树的拓扑图，构建好整个树上的配置，根据配置总结好最后寄存器的配置，写在单片机开头。
### 了解中断系统
中断基本上是单片机开发上必不可少的东西，所以需要看一下