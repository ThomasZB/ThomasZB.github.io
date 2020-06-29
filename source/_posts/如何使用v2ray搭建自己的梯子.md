---
title: 如何使用v2ray搭建自己的梯子
date: 2020-05-18 09:47:28
tags:
---

## 购买一台外网服务器
想要访问被墙掉的网页，就要有可以访问的外网服务器帮自己转发，所以需要买一台外网的服务器，外网的服务器有很多种选择，服务器选择[点这里](https://zhuanlan.zhihu.com/p/111891823)查看。
## 服务器端操作
### 安装v2ray
使用`bash <(curl -L -s https://install.direct/go.sh)`命令安装，`bash`命令会自动下载，安装。昨天试的时候安装不了甚至还自动给我下载了`unzip`，特别方便。当然也可以使用其他命令安装，这里不做介绍。
### 设置开机自启
这一步其实不太需要，因为服务器一般一直都是开机状态不会关机。不过想要设置的话可以使用以下两个命令设置。
1. `systemctl start v2ray`
2. `systemctl enable v2ray`

第一个命令为启动一个服务，第二个命令为开机自动启动一个服务，这一步结束了就相当于已经启动v2ray服务器端进程了（[systemctl](https://blog.csdn.net/moonsheep_liu/article/details/50184255)命令介绍）。
### 启动v2ray
除了上一步介绍的启动方法之外，还可以使用`service v2ray start/stop/status`来启动、关闭或者查看状态。一般启动后都要用`service v2ray status`查看是否启动成功。
### 查看v2ray信息
通过`cat /etc/v2ray/config.json`查看配置信息，配置信息里面信息也可以自己更改，默认的模式是vmess模式，可以根据需求自己再更改（vmess模式目前不是最安全的，会被识别，可以用websocket+tls伪装更安全）。这里的配置信息需要记住`"inbounds"`里面的信息，包括`port`，`id`，`alterID`三个信息，待会儿客户端连接时需要用到。  
{% asset_img 01.png 查看v2ray信息 %}
## 客户端操作(主要时windows)
### 安装v2ray
安装v2ray有很多种方法，我只介绍一种，[点击这里下载](https://github.com/2dust/v2rayN/releases)客户端。选择`v2rayN-Core.zip`，这个安装包包含了v2rayN和v2ray-core，下载后可以直接使用。  
{% asset_img 02.png 选择v2ray %}
下载后选择一个目录解压，解压后得到一堆文件，运行以下文件  
{% asset_img 03.png 打开v2ray %}
### 配置客户端
打开客户端，选择下面配置，当然如果配置时不是用的vmess就选其它的  
{% asset_img 04.png 选择类型 %}
然后把服务器看到的信息按下面顺序填入客户端  
{% asset_img 05.png 填入信息 %}
### 启动客户端
配置完后启动开始上网吧  
{% asset_img 06.png 开启v2ray %}

