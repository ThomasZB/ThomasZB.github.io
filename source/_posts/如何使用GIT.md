---
title: 如何使用GIT
date: 2020-05-19 00:17:36
tags:
categories: 程序猿基础技能
---
<p align="center">
    程序猿必备技能
</p>
<!--more-->

## Github的介绍

github是一个代码托管平台，github可以实现对工程的托管，开源，版本备份，协同开发，比较著名的开源项目如Tensorflow等都在github上可以下载，对于版本控制来说非常重要。对于工科来讲特别重要。

### Git和Github的关系

github是一个代码的托管平台，我们可以直接使用github网页端上传和备份自己的工程。但是对于程序员来说，如果更新一次版本都需要跑到网页端，然后自己根据修改了哪些文件选择文件上传，这是非常麻烦的。所以就可以使用Git这个管理工具，通过几行命令，就可对比该仓库目前哪些变化了，然后添加到仓库，再选择一个远程仓库提交，方便了工程的管理。
## Git基本使用
### Git配置命令
git的系统配置保存在 `Git/etc/gitconfig`  
git的全局配置保存在 `C:用户/xxx(用户名)/.gitconfig`  
`git config -l` 参看所有配置（l表示list）  
`git config --system --list` 查看系统配置  
`git config --global --list` 产看全局配置（一般是账户名和邮箱）  
`git config --global user.name "xxxx"` 编辑全局用户名  
`git config --global user.email "xxx"` 编辑全局邮箱  
`git config http.proxy http://127.0.0.1:10809`设置git当前代理
`git config --global http.proxy http://127.0.0.1:10809`设置git全局代理
`git config  --unset http.proxy`取消git当前代理
`git config --global --unset http.proxy`取消git全局代理

git的基本配置很简单，只需配置好全局邮箱和全局用户名就行，具体命令如上所述。
### Git基本使用命令
Git在平时的使用中，分为4个（层）目录，通过命令使自己的文件在不同地方保存。

* Workspace：工作目录，平时自己使用的文件夹
* index/Stage：暂存区，并不存在
* Repository：资源仓库，资源目录，分布式版本控制中本地的仓库，其中HEAD（.git隐藏文件夹里面）指向最新放入仓库的版本
* Remote：远程的Github服务器或Gitee的服务器（看自己爱好）

git操作一般流程

正向：

`git status`查看当前文件的状态
`git add xxx`将xxx添加到暂存区（.代表所有文件）  
`git commit -m "xxx"`将暂存区文件提交到本地资源仓库，xxx代表当前版本的说明（提交的信息）  
`git push`将本地资源仓库的文件提交到远程服务器

反向：

`git checkout`将暂存区里面的东西取消暂存 
`git reset xxx`将本地仓库里面某一个历史版本回滚  
`git pull`从远程仓库下载  

### Git的忽略

有时候我们在提交时，不想所有文件都提交到git仓库里面（比如训练集），就必须要忽略这些文件。忽略文件步骤如下

1. 在主目录下建立“.gitignore"文件
2. 添加忽略规则：
   1. 注释：”#“
   2. 通配符："*"
   3. 例外规则，将不被忽略：”!”
   4. 某个路径：”/“

### Git远程仓库

自己的项目除了在本地保存外，还可以提交到远程服务器。远程服务器可以根据自己需要选择，如：Github、Gitee、自己搭建Gitlab。这里我主要使用Github。

1. 免密码登录（使用ssh）

   所谓"公钥登录"，原理很简单，就是用户将自己的公钥储存在远程主机上。登录的时候，远程主机会向用户发送一段随机字符串，用户用自己的私钥加密后，再发回来。远程主机用事先储存的公钥进行解密，如果成功，就证明用户是可信的，直接允许登录shell，不再要求密码。这种方法要求用户必须提供自己的公钥。如果没有现成的，可以直接用ssh-keygen生成一个。

   * 配置过程

     * 去本地查看ssh的目录下有无key，即查看是否已经存在id_rsa.pub或id_dsa.pub

       > cd ~./ssh
       >
       > ls
     
   * 创建一个ssh key

     > ssh-keygen -t rsa -C "xxxx"

     这一步是就是创建ssh密钥，包括公钥和私钥，各个参数含义：

     -t 指定加密类型，这里用rsa加密算法

     -C 用于识别这个密钥的注释，随便填就行，一般填自己邮箱

     这一步后面会弹出几个问题，如文件存储位置，push密码（git push时要输入的密码），一般一直回车就可以，为空时更方便

     * 拷贝id_rsa.pub文件里面的公钥放到git的sshkey下面

       ![](https://pic-1302177449.cos.ap-chongqing.myqcloud.com//blog_pic/20210308193100.png)
       
     * 到这一步显然已经完了，但是如果已经使用过git，则需要对本地的一些git仓库进行更改

       1. 查看当前地址：

          > git remote -v

          注意看上面的连接，如果时https开头说明之前是用https进行连接的，需要更换为ssh模式

       2. 移除https方式

          > git remote rm origin

       3. 添加新的方式

          > git remote add origin xxx

          地址为下图所示地址

          ![](https://pic-1302177449.cos.ap-chongqing.myqcloud.com//blog_pic/20210308193119.png)

2. 再Github上先建一个仓库

   直接在首页的左边有一个New，可以选择新建仓库

   ![](https://pic-1302177449.cos.ap-chongqing.myqcloud.com//blog_pic/20210308193127.png)

   ![](https://pic-1302177449.cos.ap-chongqing.myqcloud.com//blog_pic/20210308193205.png)

   3. 将创建的仓库克隆到本地，然后开始自己的工程（不然的话容易出现版本冲突）。克隆下来后可以直接将自己的工程文件复制到该目录下继续工作。（或则将克隆下来的全部文件复制到工程目录下）

## GIT分支（合作时常用）

### 分支的作用

分支常用来合作、以及发行不同版本（如开发板、稳定版）等。可以保证代码不冲突，共存多个版本。

### 使用分支的常用命令

`git branch`查看当前的分支
`git branch -r`查看远程仓库上的分支
`git branch xxx`新建一个名为xxx的分支
`git checkout xxx`切换到xxx分支
`git checkout -b xxx`新建一个xxx分支，并切换到该分支
`git branch -d xxx`删除xxx分支
`git push origin --delet xxx`删除远程仓库中的xxx分支
`git branch -dr xxx`和上一个命令一样

通常，克隆后只有master分支，还要手动创建其他分支，具体步骤如下

`git checkout -b xxx origin`创建远程的xxx分支到本地的xxx分支
`git push origin xxx`提交当前代码到xxx分支（默认是主分支）

### 分支的合并

通常不同项目组开发一个项目时，有些项目组要调用另一个项目组的代码，所以就需要合并，合并后修改代码后提交可能会冲突，选择保留哪一个即可。

`git merge xxx`合并指定分支到当前分支（合并时有可能会遇到冲突，选择要保留的既可）