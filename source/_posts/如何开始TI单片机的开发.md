---
title: 如何开始TI单片机的开发
date: 2020-07-05 20:31:45
tags:
categories: MSP系列单片机
---

<p align-"center">
    瞎写的垃圾博客，改天删了
</p>

<!--more-->

## 下载开发环境

1. 准备好[gcc](https://osdn.net/projects/mingw/releases/)和[java](https://www.java.com/en/download/manual.jsp)环境
2. 安装[ccs](http://software-dl.ti.com/ccs/esd/documents/ccs_downloads.html)(Code Composer Studio)  
3. 安装[驱动](http://www.ti.com/tool/download/SMARTRFTM-STUDIO)
4. 安装[对应单片机的SDK](https://www.ti.com.cn/zh-cn/design-resources/embedded-development/embedded-software.html)（软件开发包）  
ccs安装的时候选择offline版本的，安装时根据单片机型号选择要安装的开发环境。

当然并不是所有ti的都用ccs，对于cortexm内核的单片机使用keil开发对我来说是个更好的选择，这里记录下cortexm内核的单片机怎么使用keil开发：
1. 安装好keil
2. 安装[对应单片机的SDK](https://www.ti.com.cn/zh-cn/design-resources/embedded-development/embedded-software.html)（软件开发包） 
3. 再keil上下载对应单片机的支持包
4. 再sdk的目录下找到例程跑一跑(开发板/examples/nortos/开发板/driverlib/gpio_toggle_output)

sdk目录结构如下
![](https://pic-1302177449.cos.ap-chongqing.myqcloud.com//blog_pic/20210308192949.png)


## 开始开发（keil）
### 点亮开发板上的灯
点灯相当于是单片机开发上的Hello World，在开始设计前，写一个程序点灯看看吧！


1. 打开示例里面的io操作例程  
2. 编译可得到如下结果
![](https://pic-1302177449.cos.ap-chongqing.myqcloud.com//blog_pic/20210308193003.png)  
    （1）. code 是编译后代码的大小，存储在flash里面  
    （2）. ro-data 为read only数据，存储在flash里面
    （3）. rw-data 为已经初始化的全局变量,存储在ram里面
    （4）. zi-data 未初始化的全局变量，存储在ram里面
    （5）. 实际中单片机初始化时，代码从flash转移到ram里面，所以烧录时Total ROM size（Code + RO Data +RW Data）
3. 下载代码，使用arm提供的通用cmsis可以快速下载。
### 了解开发板的结构
大概了解一下开发板由哪些构成，这个可以参考电路图，或则TI开发板的小卡片，上面写各个接口的位置，以及可以复用的功能。如下图所示：  

<img src="https://pic-1302177449.cos.ap-chongqing.myqcloud.com//blog_pic/20210308193011.png" width = 50% height = 50% />  

### 开始了解时钟树
时钟是单片机的心脏，开发前很有必要了解，在对应型号的手册上有对应的时钟树，以及对应的每一个寄存器。根据寄存器和时钟树的拓扑图，构建好整个树上的配置，根据配置总结好最后寄存器的配置，写在单片机开头。
### 了解中断系统
中断基本上是单片机开发上必不可少的东西，所以需要看一下