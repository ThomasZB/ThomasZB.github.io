---
title: 如何使用vscode写markdown
date: 2020-05-14 10:55:53
tags:
---
## 使用vscode打开markdown
vscode默认集成了markdown文档编辑插件  
使用vscode打开.md文件后使用ctrl+shift+p调出主命令框，输入markdown，选择打开侧边预览  
{% asset_img 01.png 使用vscode编写markdown %}
这样就可以愉快的一遍编辑文档一边看效果啦！！！
___
## 附一些不常用的操作
### 1. 特殊符号不被识别为命令
在符号前加入\\转义字符如\\# \\>
### 2. 粘贴代码
使用"\>"后面的输入当作代码处理  
\>print("hello world")  
的结果
>print("hello world")
### 3. 插入图片
使用hexo搭建的博客在上传图片时要安装插件
1. 把配置文件`_config.yml`里面`post_asset_folder:`这个选项设置为`ture`。意思时文章资源文件夹功能打开，这样每一次新建一个博客会自动生成同名的文件夹，图片的等资源可以放在里面。
2. 在hexo目录下执行 `cnpm install --save hexo-asset-image`，这是下载一个可以上传本地图片的插件
3. 在使用`hexo n`创建新博客时新建的资源文件夹中放入图片等文件
4. 使用`{\% asset_img test.jpg This is an test image %}`(注意：要自己去掉'\\')插入图片`test.jpg`为图片名，`This is an test image`为描述，可以更改，剩下的不要更改就没问题
