---
title: conda创建虚拟环境
date: 2020-06-03 09:52:27
tags:
---
## 虚拟环境的作用
有时候要使用一个python库的不同版本，如tensorflow1和tensorflow2，这时候就需要创建两个虚拟环境，将不同的版本安装到不同的虚拟环境，然后根据需要，在不同的环境下进行工作。
## 更新conda
> conda update conda

## 创建一个虚拟环境
### 创建
使用该命令创建一个虚拟环境

> conda create -n xxx python=3.x
### 查看虚拟环境
查看的时候使用前面带*号的为当前激活的环境

> conda env list
### 激活虚拟环境
> conda activate xxx
### 删除虚拟环境
> conda remove -n xxx --all
### 删除虚拟环境某个包
> conda remove --name env_name package_name
## 启用虚拟环境的jupyter
* 查看jupyter notebook kernel
> jupyter kernelspec list
* 删除jupyter notebook kernel
> jupyter kernelspec remove kernelname
* 在该环境下安装ipykernel
> conda install ipykernel
* 创建jupyter notebook kernel文件
> conda install -n env_name ipykernel
* 激活conda环境
> conda activate env_name
* 将环境写入notebook的kernel中
> python -m ipykernel install --user --name env_name --display-name "在jupyter中显示的环境名称"
* 在改环境下启动jupyter notebook
> jupyter notebook