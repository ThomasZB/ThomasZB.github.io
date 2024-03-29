---
title: STM32 HAL学习记录
date: 2020-07-22 12:08:55
tags:
categories: 嵌入式
---


<p align="center">
stm32系列从f7系列开始就已经不支持标准库了，为了更上时代的步伐，了解以下hal库的使用非常有必要。同时hal库开始，代码的移植将变得非常简单，stm32的各个系列开发使用的库函数基本都相同
</p> 
<!--more-->

## SD卡使用过程

 在32上使用HAL库配置SD卡很简单，唯一要注意的就是时钟问题，48Mhz的时钟一般没有什么问题

### SD卡使用简介

SD卡的使用分为3个步骤

1. 初始化 
初始化时发送不同命令询问sd卡信息等，确认初始化sd卡可用（初始化一般要求时钟频率低）
2. 写
3. 读

所有步骤都是通过指令实现，SD卡的四线模式如下图所示，CMD引脚走的是命令，可以通过该引脚配置SD卡，具体命令可以网上查找

![](https://pic-1302177449.cos.ap-chongqing.myqcloud.com//blog_pic/20210308191936.png)

### SD卡配置过程

1. 使能SD卡传输 
sd卡的使能很简单，如下配置就行

![](https://pic-1302177449.cos.ap-chongqing.myqcloud.com//blog_pic/20210308192001.png)

2. 配置SD卡时钟 
使能SD卡后可以看到有个SDMMC CLOCK（名字差不多就行），然后这里我将他的时钟配置为48Mhz（实测50M或者更高在我的这款SD卡上会出错）配置如下图所示

![](https://pic-1302177449.cos.ap-chongqing.myqcloud.com//blog_pic/20210308192056.png)

![](https://i.loli.net/2020/07/22/KGa6x42kgnpFOme.png)

3. 使用sd卡 
sd卡的操作一般有读、写、擦除等，这些函数在HAL库都有提供，如下图所示

![](https://pic-1302177449.cos.ap-chongqing.myqcloud.com//blog_pic/20210308192211.png)

这里要注意擦除后最好延时一段时间再读，测试时直接读有出错。

---
## FATFS的移植

### FATFS简介

FATFS可以根据需求添加文件系统的构成，可以移植到ROM很小的单片机上（功能可能很少）。  
FATFS文件系统就是提供了一套操作SD卡文件的API，可以直接调用提供的API对SD卡中的文件进行读写操作（而不是原始的对块进行操作）。将FATFS移植到单片机，所有文件操作的逻辑等都可以通过函数实现。

### 配置过程

配置过程说起来很复杂，需要将读写sd卡等常用的函数写道fatfs的driver里面，然后通过修改一些宏定义决定是否要启用某些功能。但使用cubemx只需要根据自己的需求使能或者关闭相关的功能即可，如下图

![](https://pic-1302177449.cos.ap-chongqing.myqcloud.com//blog_pic/20210308192247.png)

操作时遵循一下过程 

1. 挂载SD卡
2. 判断挂载是否成功（不成功可能是没有格式化成FAT32格式）