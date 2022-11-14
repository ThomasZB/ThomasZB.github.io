---
title: 基于图优化的SLAM
date: 2022-06-30 13:50:54
tags:
categories: 机器人
plugins:
  - mathjax
---

<p align="center">
    SLAM：同时定位和建图
</p>


<!--more-->

# SLAM简介

SLAM即同时定位和建图（simultaneous localization and mapping），目前主流有2种方法，一种是基于滤波器的SLAM，一种是基于图优化的SLAM。基于滤波器的SLAM由于误差会累计，目前更流行的方法为基于图优化的SLAM，本文也主要讲解基于图优化的SLAM。

## 环境理想下的机器人定位



SLAM中定位常用`scan-match`，建图目前我只了解了栅格地图的构建方法，栅格地图的构建主要分为覆盖栅格建图和计数建图。

## 几种scan-match

scan-match是匹配scan数据的一种方式，scan数据这里指的是range data，在2d激光雷达定位中指的通常是激光数据。激光数据通常可以用结构体表示，通常可以用以下数据结构表示

```c++
struct RangeData{
    Eigen::Vector3f origin;
    PointCloud returns;
    PointCloud misses;
};
```

其中origin为激光数据的中心位姿，returns为激光扫描到的数据，里面有激光相对于origin的vector数据。misses为激光发射出去，未返回的数据。也就是没射到障碍物。

scan-match分为以下几类：

1. scan-to-scan：激光数据和激光数据做对比，可以得到两帧激光数据的相对位姿
2. scan-to-map：可以得到激光数据在map中的相对位姿
3. map-to-map：可以得到两个地图的相对位姿

## SLAM建图的基本原理-覆盖栅格建图

1. 获得预测位姿

   获得预测位姿通常有多种方法，通常是通过里程计和IMU获得位姿的预测。也可以通过scan-match来。

2. 将激光数据插入地图中

   根据预测的位姿，将激光点加入到

## SLAM中的简单问题



## SLAM中的误差累计问题





# 基于图优化的SLAM

​		基于滤波器的SLAM误差会累积，基于图每次估计$x_0$到$x_t$不会累积误差（回到以前的地方时通过优化的方式去掉误差）。基于图优化的SLAM分为两个步骤，即前端构图和后端优化，接下来讲分别介绍两个部分，本文重点在于后端优化。

​		这部分主要讲的是对轨迹的优化！！！也就是定位的优化，有了更精确的定位，建图自然就精确了。

## 前端构图

​		这里的前端构图指的是数据结构中的图，而不是地图，这点一定要分清楚！

​		记一个东西，$z_{ij}$其实是j变换到i的变换矩阵（好像是吧，这里还不太确定），这个还是没太搞懂，最好看一下

## 后端优化

​		找到最满足约束条件的机器人位姿配置，本文讲解的方法为非线性最小二乘法，使用高斯-牛顿法迭代求解。

![image-20220630155617022](https://pic-1302177449.cos.ap-chongqing.myqcloud.com/blog_pic/%20image-20220630155617022.png)

​		图中的误差为两点之间的差，则误差为，至于怎么推导出来的，待会儿讲解：
$$
e_{ij}=t2v(Z_{ij}^{-1}(x_i^{-1} \cdot x_j))
$$
则有：
$$
e_{ij}=
\begin{bmatrix}
R_{z}^T(R_i^T(t_j-t_i)-t_{z}) \\
\theta_j-\theta_i-\theta_{z}
\end{bmatrix}
$$

式中，$R$为旋转矩阵，表示两个坐标系旋转前后的坐标变换关系，跟旋转角$\theta$的转换关系如下所示：
$$
R=
\begin{aligned}
    \begin{bmatrix}
        cos\theta & -sin\theta \\
        sin\theta & cos\theta
    \end{bmatrix}
\end{aligned}
$$


## 优化步骤

1. 计算误差：
   $$
   e_{ij}=t2v\left(Z_{ij}^{-1}(x_i^{-1} \cdot x_j)\right)
   $$
   则有：
   $$
   e_{ij}=
   \begin{bmatrix}
   R_{z}^T(R_i^T(t_j-t_i)-t_{z}) \\
   \theta_j-\theta_i-\theta_{z}
   \end{bmatrix}
   $$

2. 求出雅可比矩阵$J_{ij}$：
   $$
   J_{ij}=\left(\cdots\frac{\partial e_{ij}(x+\Delta x)}{\partial\Delta x_i}\cdots\frac{\partial e_{ij}(x+\Delta x)}{\partial\Delta x_j}\cdots\right)
   $$

3. 求出$H_{ij}$矩阵：
   $$
   H_{ij}=J_{ij}^T\Omega_{ij}J_{ij}
   $$

4. 求出$b_{ij}$向量：
   $$
   b_{ij}^T=e_{ij}^T\Omega_{ij}J_{ij}
   $$

   $$
   b_{ij}=\left(\left(b_{ij}^T\right)^T\right)
   $$
   
5. 更新$H$矩阵和$b$向量第$(i,j)$项：
   $$
   H=H+H_{ij}
   $$

   $$
   b=b+b_{ij}
   $$

6. 计算完所有的约束项后，根据下式计算$\Delta x$（一般不通过求逆，而是稀疏矩阵的求法，主要式为了减小运算量）：
   $$
   H\Delta x=-b
   $$

7. 更新$x$：
   $$
   x = x+\Delta x
   $$

## 实际情况的改进（算法具体实现）

​		实际情况中，由于雅可比行列式J是稀疏的，通常不用矩阵乘法，直接计算出特定位置的值即可，这里根据实际情况改进上述对应的步骤。

1. 求出雅可比矩阵（只用求i项和j项）：

   由于$\Delta x$有3项，这里对$\Delta x$求偏导也是求出其雅可比行列式，求出的结果如下式所示：
   $$
   A_{ij}=\frac{\partial e_{ij}(x+\Delta x)}{\partial\Delta x_i}=
   \begin{bmatrix}
   -R_z^TR_i^T & R_z^T\frac{\partial R_i^T}{\partial \theta_i}(t_j-t_i) \\
   0 & -1
   \end{bmatrix}
   $$

   $$
   B_{ij}=\frac{\partial e_{ij}(x+\Delta x)}{\partial\Delta x_j}=
   \begin{bmatrix}
   R_z^TR_i^T & 0 \\
   0 & 1
   \end{bmatrix}
   $$

2. 更新$H$（直接求出$H$矩阵中对应的元素加上去）：
   $$
   H[ii] +=A_{ij}^T\Omega_{ij}A_{ij}
   $$

   $$
   H[ij] +=A_{ij}^T\Omega_{ij}B_{ij}
   $$

   $$
   H[ji] +=B_{ij}^T\Omega_{ij}A_{ij}
   $$

   $$
   H[jj] +=B_{ij}^T\Omega_{ij}B_{ij}
   $$

3. 更新$b$（直接求出$b$对应位置的元素加上去）：
   $$
   b[i]+=A_{ij}^T\Omega_{ij}e_{ij}
   $$

   $$
   b[j]+=B_{ij}^T\Omega_{ij}e_{ij}
   $$

4. 上述步骤循环到$C$（所有约束的集合）计算完

# 参考文献

