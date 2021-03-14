---
title: 如何写markdown
date: 2020-05-14 10:55:53
tags:
categories: 程序猿基础技能
---
<p align="center">
    写博客必备，以前使用vscode，现在一般都用typora了
</p>
<!--more-->

## 使用vscode打开markdown

vscode默认集成了markdown文档编辑插件
使用vscode打开.md文件后使用ctrl+shift+p调出主命令框，输入markdown，选择打开侧边预览  
![](https://pic-1302177449.cos.ap-chongqing.myqcloud.com//blog_pic/20210308193653.png)
这样就可以愉快的一遍编辑文档一边看效果啦！！！

___
## 附一些markdown常用语法
### 1. 特殊符号不被识别为命令
在符号前加入\\转义字符如\\# \\>
### 2. 粘贴代码

1. 单行代码

   使用"\>"后面的输入当作代码处理 

>print("hello world")
2. 代码块

\```c（即三个点加所需要的语言）

```c
#include "stdio.h"

int main()
{
    printf("hello world");
    return 0;
}
```

3. 插入网址\[xxx](www.baidu.com)

### 4. 插入图片

插入图片现在通常先把图片放在图床里面，再通过图床放入到博客里面。我一般使用Picgo作为图床的工具，

使用Github图床。

使用picgo+typora配置如下图所示：

![](https://pic-1302177449.cos.ap-chongqing.myqcloud.com//blog_pic/20210308193637.png)