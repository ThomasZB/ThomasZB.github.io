---
title: LIO-SAM激光配准部分推导
date: 2023-05-25 23:35:19
tags:
categories: 机器人
plugins:
  - mathjax
---

![image-20230526000112361](https://pic-1302177449.cos.ap-chongqing.myqcloud.com/blog_picimage-20230526000112361.png)

<p align="center">
   学习下大佬们对高斯牛顿法在实际中的应用o(*￣▽￣*)ブ
</p>

<!--more-->

# 简介

LIO-SAM中，大部分代码都比较好理解，稍微有点麻烦的就是LMOptimization，表面上说是LM优化，但这里其实用的是高斯牛顿优化（优化相关的方法后面会另开博客讲解）。

> LM优化相关的原理后面会另开优化博客讲解，这里主要是相当于一个LM优化的应用吧。

# 推导

> 下面的推导是我参考一些博客和书籍自己推了一遍，**如果看到有错误的地方欢迎指出~**

对于任何一个优化方法，优化前都需要构建其误差函数，这里的误差函数由点到面的距离构成，表达式如下所示：
$$
\arg\min_{^M_LT}\left\|f(^M_LT)\right\|^2_2=\arg\min_{^M_LT}\left\|\sum_is_i\left\{(\overrightarrow{n}_i)^Tz_i+D_i\right\}\right\|^2_2=\arg\min_{^M_LT}\left\|\sum_is_ie_i\right\|^2_2
$$
其中$z_i$为点云在地图的位姿，$\overrightarrow{n}_i=[A,B,C]^T$为$z_i$点云所匹配平面的法向量，**$^M_LT$为Lidar在Map中的位姿，也就是要优化的变量。**这里平面方程如下所示，很容易理解，上面的误差函数就是每个点到面的距离加权。
$$
Ax+By+Cz+D=0
$$
然后就是$z_i$的计算，优化变量也存在$z_i$里面，设$\tilde{z}_i$为点云在Lidar坐标系下的位姿，那么$z_i$的计算公式如下所示：
$$
z_i=^{M}_{L}T\times\tilde{z}_i=^M_LR\times\tilde{z}_i+^MP_L
$$
其中$^M_LR$为Map下Lidar的姿态，可以用欧拉角计算（欧拉角只有3个变量方便优化？），众所周知机器人欧拉角一般使用rpy角（可以参考我之前一篇博客）。在LIO-SAM中，这里先将Lidar坐标系转化为相机坐标系，对应关系如下所示：

![雷达坐标系和相机坐标系](https://pic-1302177449.cos.ap-chongqing.myqcloud.com/blog_pic%E9%9B%B7%E8%BE%BE%E5%9D%90%E6%A0%87%E7%B3%BB%E5%92%8C%E7%9B%B8%E6%9C%BA%E5%9D%90%E6%A0%87%E7%B3%BB.png)

>根据图像可以得到其平移：x->z，y->x，z->y
>
>其旋转的yaw角（Lidar下为rz）：rz->ry（其他角同理）

那么可以得到$^M_LR$的计算公式如下（这里简写成$R$）：
$$
\begin{eqnarray}
R&=&R_y\times R_p\times R_r=R_{rz}\times R_{ry}\times R_{rx} \nonumber \\
~&=&\begin{aligned}\begin{bmatrix}
crz\cdot cry&-srz\cdot crx+crz\cdot sry\cdot srx&srz\cdot srx+crz\cdot sry\cdot crx \nonumber  \\
srz\cdot cry&crz\cdot crx-srz\cdot sry\cdot srx&-crz\cdot srx+srz\cdot sry\cdot crx \nonumber \\
-sry&cry\cdot srx&cry\cdot crx \nonumber
\end{bmatrix}\end{aligned} \nonumber
\end{eqnarray}
$$
然后就是求雅可比矩阵，根据优化的变量，雅可比矩阵如下所示：
$$
J=\frac{\partial{f(^M_LT)}}{\partial^M_LT}=\left[\frac{\partial e}{\partial rx},\frac{\partial e}{\partial ry},\frac{\partial e}{\partial rz},\frac{\partial e}{\partial x},\frac{\partial e}{\partial y},\frac{\partial e}{\partial z}\right]^T
$$
对于求和的雅可比矩阵，我们通常看单个变量$e_i$，再对其求和，对于单个变量，首先给出对角度$rx$的偏导：
$$
\begin{eqnarray}
\frac{\partial e_i}{\partial rx}&=&\overrightarrow{n}_i^T\cdot\frac{\partial z_i}{\partial rx}=\overrightarrow{n}_i^T\cdot\frac{\partial^M_LR}{\partial rx}\cdot\tilde{z}_i \nonumber \\
~&=&\overrightarrow{n}_i^T\cdot\begin{aligned}\begin{bmatrix}
0&srz\cdot srx+crz\cdot sry\cdot crx&srz\cdot crx-crz\cdot sry\cdot srx  \\
0&-crz\cdot srx-srz\cdot sry\cdot crx&-crz\cdot crx-srz\cdot sry\cdot srx \\
0&cry\cdot crx&-cry\cdot srx
\end{bmatrix}\end{aligned}\cdot\tilde{z}_i \nonumber
\end{eqnarray}
$$
然后是对平移$x$的偏导：
$$
\begin{eqnarray}
\frac{\partial e_i}{\partial rx}&=&\overrightarrow{n}_i^T\cdot\frac{\partial z_i}{\partial x}=\overrightarrow{n}_i^T\cdot\frac{\partial ^MP_L}{\partial x} \nonumber\\
~&=&\overrightarrow{n}_i^T\cdot\begin{aligned}\begin{bmatrix}
1 \\0 \\0
\end{bmatrix}\end{aligned} \nonumber
\end{eqnarray}
$$
其他偏导求法同理，得到了单个变量的偏导，则雅可比矩阵可以求和得到，得到了雅可比矩阵后，即可计算增量：
$$
H\Delta x=g
$$
$$
J^T(x)J(x)\Delta x=-J^T(x)f(x)
$$

计算出$\Delta x$后，不断迭代即可求出Lidar到Map的坐标变换，即Lidar的定位。

> 我这里写的对$J_i$求和，实际做时往往是把$J$排列成一个矩阵$J=[J_1,J_2,...J_n]$，这样$J^TJ$其实就相当于进行了一次求和（这种更快？）。
