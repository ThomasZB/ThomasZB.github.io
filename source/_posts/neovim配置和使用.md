---
title: neovim配置和使用
date: 2022-09-02 10:36:48
tags:
categories: 程序猿基础技能
---

<img src="https://pic-1302177449.cos.ap-chongqing.myqcloud.com/blog_pic/%20image-20220902104135889.png" align="center" height=500>

<p align="center">
    使用ipad进行远程开发的时候只能使用vim，我主要使用neovim加插件的方式方便开发
</p>
<!--more-->

# neovim安装和基本使用

在debian家族的linux下，neovim的安装可以直接使用apt包管理工具安装，但一般源里只有较低版本，要使用neovim最新版本需要使用neovim官方源

```shell
sudo add-apt-repository ppa:neovim-ppa/stable
sudo apt-get update
sudo apt-get install neovim
```

## 使用

目前体验下来，neovim和vim基本一致，差别主要在于插件。vim的教程可以大概看一下man手册，vim在操作时主要分为以下四种模式：

|        模式         |                             简介                             |
| :-----------------: | :----------------------------------------------------------: |
|  正常模式（nomal）  | 打开文件时的默认模式，任何模式下按`esc`进入，按键不能输入，主要映射为一些功能，比如hjkl为移动等 |
| 命令模式（command） | 在正常模式下，输入冒号`:`或`/`进入，进入后可以使用`w`写入`q`退出等操作 |
| 插入模式（insert）  |    在正常模式下，输入`i`进入，在插入模式下，可以编辑文本     |
| 可视模式（visual）  | 在正常模式下，输入`v`进入，进入后hjkl可以选中多行，主要用于多行操作 |

下面，对不同模式的快捷键操作做详细介绍

### 正常模式

* 移动光标：

|           功能            |           按键           |
| :-----------------------: | :----------------------: |
| 移动单行/列，左/下/上/右  |  h/j/k/l（或者方向键）   |
| 移动到下/上一个单词的末尾 |           e/b            |
|    向上移动一页（up）     |          ctrl+u          |
|   向下移动一页（down）    |          ctrl+d          |
|        移动到行尾         |         0或者end         |
|        移动到行首         |        $或者home         |
|        移动到开头         |            gg            |
|        移动到末尾         |            G             |
|       移动到某一行        | n+gg，比如跳到第6行，6gg |

* 删除：

|          功能           | 按键 |
| :---------------------: | :--: |
| 删除光标后/前的一个字符 | x/X  |
|     剪切光标所在行      |  dd  |

* 复制：yy，复制一行

* 粘贴：p/P，在光标后/前粘贴

* 撤回：u

* 其他技巧：
  * 数字加上述命令可以多次执行。如5j，向下5行，3x，删除3个。

### 命令模式

命令行模式除了输入下述命令外，还可以输入所安装插件支持的命令。

* 文件保存和退出
  * 退出：q
  * 保存：w
  * 保存并退出：wq
* 查找：`/xxx`，查找xxx，输入后按回车进入普通模式，再通过n/N可以向下/上跳转匹配对象
* 显示行号：`set nu`开启，`set nonu`关闭
* 替换：
  * `:n1,n2/word1/word2/g`：将n1到n2行之间的word1替换为word2，不加g则只替换每行的第一个word1，加g则搜到的word1全部替换为word2
  * `:1,$s/word1/word2/g`：将文章中的word1替换为word2，不加g则只替换每行的第一个word1
* 其他
  * 强制执行命令：加上`!`

### 插入模式

插入模式根据需求，可以用不同种方式进入

| 进入方式 |                             效果                             |
| :------: | :----------------------------------------------------------: |
|    i     |          在光标所在字符前开始输入文字并进入插入模式          |
|    I     | 在行首开始输入文字并进入插入模式。此行首指第一个非空白字符处。 |
|    a     |          在光标所在字符后开始输入文字并进入插入模式          |
|    A     |               在行尾开始输入文字并进入插入模式               |
|    o     |   在光标所在行的下面单独开一新行，来输入文字并进入插入模式   |
|    O     |    在光标所在行的上面单独开一新行来输入文字并进入插入模式    |
|    s     |               删除光标所在的字符并进入插入模式               |
|    S     |                 删除光标所在行并进入插入模式                 |

### 可视模式

正常模式下v进入字符文本，V进入行文本，进入后可以选中文本，选中后常用的快捷键如下

|    功能    |                  快捷键                  |
| :--------: | :--------------------------------------: |
|    删除    |                    d                     |
|    复制    |                    y                     |
|    粘贴    |                    p                     |
|    替换    |                    r                     |
| 大小写转换 | gu转换为小写，gU转换为大写，g~大小写互换 |

常用快捷键基本就是以上这些，当然快捷键还可以自己设置，结合neovim安装的插件可以让开发效率更快。

# neovim插件安装

neovim可以使用lua文件来进行插件的配置，插件一般在github上可以搜索到。neovim可以使用packer插件来进行插件的管理（一个管理插件的插件>_>）

neovim常用的插件可以在[rockerBOO/awesome-neovim: Collections of awesome neovim plugins. (github.com)](https://github.com/rockerBOO/awesome-neovim)网站中查找

## 安装packer

packer插件的github官网为：

[wbthomason/packer.nvim: A use-package inspired plugin manager for Neovim. Uses native packages, supports Luarocks dependencies, written in Lua, allows for expressive config (github.com)](https://github.com/wbthomason/packer.nvim)

安装时跟着官网的教程走即可

## 其他常用插件

1. 主题
2. 代码提示
3. 侧边栏
4. 打开的文件栏
5. 文件搜索

# neovim快捷键设置



# 参考文档

[(14条消息) Vim的4种模式_还能坚持的博客-CSDN博客_vim模式](https://blog.csdn.net/qq_35091353/article/details/119644074)

[Neovim 的快捷键配置 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/435680085)

