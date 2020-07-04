---
title: 如何使用hexo搭建个人博客
date: 2020-05-15 20:06:42
tags:
---

## 准备工作
我这里使用github+hexo的组合搭建，所以要安装git和hexo，git安装我后面再建个博客写，这里我先介绍hexo的安装
### 下载[nodejs](https://nodejs.org/en/)
一般选择LTS（长期支持版）。安装完后有两个组件，一个是nodejs组件（hexo需要nodejs支持），一个是npm软件管理包。下载下来安装一直点next差不多就可以了。  
下载完后用`node -v`和`npm -v`查看是否安装成功。
### 安装cnpm（换成淘宝源）
由于npm服务器在国外，下载速度慢，所以换成cnpm。使用命令`npm install -g cnpm --registry=https://registry.npm.taobao.org`安装。
### 利用cnpm安装hexo博客
命令`cnpm install -g hexo-cli`，`-g`的意思是全局安装，就可以在命令行使用这个命令（相当于添加环境变量）。安装后使用`hexo -v`查看是否安装成功。

---
## 正式搭建hexo博客
### 创建博客的空间
创建一个存放博客文件的空间，以后博客的管理等等都在该空间
### hexo博客常用命令
* 初始化一个博客：`hexo init`
* 创建一篇博客，创建的博客默认保存在`source/_posts/`，该命令在博客目录执行：`hexo n "xxx"`
* 清理：`hexo clean`
* 生成：`hexo g`
* 启动：`hexo s`

### 将hexo博客同步到github云端
1. 在github上创建博客的仓库，名称为`username.github.io`
2. 安装git部署插件：`cnpm install --save hexo-deployer-git`
3.  配置`_config.yml`文件，在最后的Deployment配置如下  
<div align=left><img src ="https://raw.githubusercontent.com/ThomasZB/picture/master/deployment_set.png"/></div>
4. 部署到云端，现在默认下会使用https上传，输入github账户和密码就行了：`hexo d`
---
## 主题配置