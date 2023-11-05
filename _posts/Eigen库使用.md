---
title: Eigen库使用
date: 2023-11-05 11:34:22
tags:
categories: C/C++
plugins:
  - mathjax
---

![00003](https://pic-1302177449.cos.ap-chongqing.myqcloud.com/blog_pic202311051459350.png)\

Eigen库主要用于矩阵运算，由于Eigen库全都是由`.h`文件构成，所以兼容性特别强，Eigen库在矩阵运算上，速度也是非常的快。除此之外，Eigen库还包几何模块，可以方便的用于机器人开发。

<!--more-->

>感觉这种代码上的文章写成博客是否有点多余了，需要什么函数直接去查文档不久行了吗。这里主要是一个记录，并不涉及Eigen内部的实现，以后有一些小技巧也会更新在上面。与其说这是博客，不如说是写给自己参考的笔记。（另外，这个蓝头发少女的图片时ai生成的，本来想生成一个大啥子，看一本Eigen的书，但是试了好多次都不太啥，凑合一下把）

# 安装和使用

## 安装

这一点往往是使用一个库最麻烦的步骤，不过Eigen库相较于其他库比较友好，在Ubuntu中，可以直接通过如下命令安装

```bash
sudo apt-get install libeigen3-dev
```

Eigen的头文件默认被安装在`/usr/include/eigen3/`

> 此外，可以通过`sudo updatedb`，`locate eigen3`两个命令来定位Eigen

## 使用

根据上面的说法，不难知道，想要使用Eigen3只用包含头文件，编译的时候带上对应头文件参数即可。

**头文件**

|头文件|用途|
|:------:|:-------:|
|Eigen/Core|Eige的核心部分|
|Eigen/Dense|稠密矩阵的代数运算（通用的）|
|Eigen/Geometry|几何模块|

为了更方便的理解使用，我举一个例子
```cpp
#include <Eigen/Core>
#include <iostream>

int main() {
  Eigen::Matrix<float, 2, 3> matrix_23;

  matrix_23 << 1, 2, 3, 4, 5, 6;
  std::cout << matrix_23 << std::endl;

  return 0;
}
```

**g++直接编译**

如果只是测试的小文件，可以直接通过`-I`添加头文件目录，即可编译通过

```bash
g++ test1.cc -I /usr/include/eigen3
```

**使用CMake**

对于较大项目，最好还是使用CMake，要在项目中使用Eigen也很简单，可以直接指定目录，不过考虑兼容性，最好还是通过`find_package`来寻找Eigen目录，再添加到头文件列表

对于上述的例子，可以用如下`CMakeLists.txt`编译通过

```CMake
cmake_minimum_required(VERSION 3.0)
project(eigen_test)

# Find Eigen library
find_package(Eigen3 REQUIRED)
include_directories(${EIGEN3_INCLUDE_DIR})

# Add executable
add_executable(eigen_test test1.cc)
```

# 基本操作

这一章节，我主要记录Eigen库作为矩阵运算库的常见操作，比如定义、基本运算、常用算法等。

## 矩阵定义

Eigen中，所有的向量和矩阵都是`Eigen::Matrix`。它是一个模板类，前三个参数分别为`类型`，`行`，`列`，下面直接通过代码来说明


```cpp
// 定义一个两行三列的float矩阵
Eigen::Matrix<float, 2, 3> matrix_23;

// 定义一个三维向量，类型为double，两种方式等价
Eigen::Vector3d v_3d;
Eigen::Matrix<double, 3, 1> vd_3d;

// 定义一个3x3的矩阵，初始化为0
Eigen::Matrix3d matrix_33 = Eigen::Matrix3d::Zero();
// 如果不确定矩阵大小，或者矩阵较大，可以使用动态大小的矩阵
Eigen::Matrix<double, Eigen::Dynamic, Eigen::Dynamic> matrix_dynamic(200,200);
// 和上面等价
Eigen::MatrixXd matrix_x;
matrix_x.resize(200,200);
```

有时候我们还需要创建一些特殊矩阵，比如单位阵，0矩阵、随机矩阵等等


```cpp
Eigen::Matrix<float, 2, 3> matrix_23;
matrix_23 << 1, 2, 3, 4, 5, 6;  // 初始化一个矩阵

// 定义单位矩阵
Eigen::Matrix3d matrix_33 = Eigen::Matrix3d::Identity();
Eigen::MatrixXd matrix_x = Eigen::MatrixXd::Identity(200, 200);
matrix_x.setIdentity();

// 定义0矩阵
Eigen::MatrixXd matrix_y;
matrix_y = Eigen::MatrixXd::Zero(3, 3);
matrix_y.setZero();
matrix_y.setZero(3, 3);  // 也可以跟参数

// 定义随机矩阵，生成的随机数范围在[-1,1]内
Eigen::Matrix<double, Eigen::Dynamic, Eigen::Dynamic> matrix_dynamic;
matrix_dynamic = Eigen::MatrixXd::Random(3, 3);
matrix_dynamic.setRandom();

// 定义全1矩阵
matrix_dynamic = Eigen::MatrixXd::Ones(3, 3);
matrix_dynamic.setOnes();

// 定义混合矩阵
Eigen::MatrixXd matrix_h(200, 200);
matrix_h << Eigen::MatrixXd::Random(100, 200),
    Eigen::MatrixXd::Zero(100, 200);
```

## 矩阵读取和分块

**读取某个元素**：这里介绍一下矩阵的读取，可以直接通过`(i,j)`进行索引：

```cpp
std::cout << matrix_h(0, 0) << std::endl;
```

**操作某一行或某一列**：操作某一行或者某一列时也很容易

```cpp
// 读取第2行数据
std::cout << matrix_10_10.row(1) << std::endl;
// 读取第二列数据
std::cout << matrix_10_10.col(1) << std::endl;
// 将第二列数据置0
matrix_10_10.col(1) = Eigen::VectorXd::Zero(10);
std::cout << matrix_10_10 << std::endl;
```

**矩阵基本分块操作**：用于获取矩阵某块子矩阵，可以用于赋值和操作，使用函数`block`，有以下两种方法使用：

|       动态size        |        固定size        |                         功能                          |
| :-------------------: | :--------------------: | :---------------------------------------------------: |
| matrix.block(i,j,p,q) | matrix.block<p,q>(i,j) | 从$(i,j)$开始（左上角为$0,0$），大小为$(p,q)$的矩阵块 |

这里举个例子

```c++
// 输出左上角的2x2大小的矩阵
std::cout << matrix_10_10.block(0, 0, 2, 2) << std::endl;
// 这样也同理，一般提前知道大小时可以用
std::cout << matrix_10_10.block<2, 2>(0, 0) << std::endl;
// 也可以进行赋值
matrix_10_10.block<2, 2>(0, 0) = Eigen::Matrix2d::Zero();
std::cout << matrix_10_10 << std::endl;
```

**角落块操作**：有时候我们取矩阵只取矩阵某个角落的块，这时候可以用下面的这些函数进行操作

|            动态size            |             固定size             |           功能            |
| :----------------------------: | :------------------------------: | :-----------------------: |
|   matrix.topLeftCorner(p,q);   |   matrix.topLeftCorner<p,q>();   | 取左上角$(p,q)$大小的矩阵 |
| matrix.bottomLeftCorner(p,q);  | matrix.bottomLeftCorner<p,q>();  | 取左下角$(p,q)$大小的矩阵 |
|  matrix.topRightCorner(p,q);   |  matrix.topRightCorner<p,q>();   | 取右上角$(p,q)$大小的矩阵 |
| matrix.bottomRightCorner(p,q); | matrix.bottomRightCorner<p,q>(); | 取右下角$(p,q)$大小的矩阵 |
|       matrix.topRows(q);       |       matrix.topRows<q>();       |          前$q$行          |
|     matrix.bottomRows(q);      |     matrix.bottomRows<q>();      |          后$q$行          |
|      matrix.leftCols(p);       |      matrix.leftCols<p>();       |          前$q$列          |
|      matrix.rightCols(q);      |      matrix.rightCols<q>();      |          后$q$列          |

这上面的函数使用方法和前面类似，这里就不再举例介绍了

**向量操作**：一维的向量想要取其中某部分数据时，也有对应的操作函数

|       动态size       |       固定size        |         功能         |
| :------------------: | :-------------------: | :------------------: |
|   vector.head(n);    |   vector.head<n>();   |   取向量前n个元素    |
|   vector.tail(n);    |   vector.tail<n>();   |   取向量后n个元素    |
| vector.segment(i,n); | vector.segment<n>(i); | 从i位置开始的n元素块 |

同理，这里我也不再举例了

## 矩阵基本运算

这一节，我将讲解一下矩阵的行列式、转置、求逆、四则运算等等

首先是**单矩阵的操作**

|            操作             |             方法             |
| :-------------------------: | :--------------------------: |
|            转置             |      matrix.transpose()      |
|            求逆             |       matrix.inverse()       |
|           行列式            |     matrix.determinant()     |
|             迹              |        matrix.trace()        |
|         各元素之和          |         matrix.sum()         |
|      各元素的平方根和       |        matrix.norm()         |
|       各元素的平方和        |     matrix.squaredNorm()     |
|       各元素的平均值        |        matrix.mean()         |
| 矩阵中各元素的最小值/最大值 | matrix.minCoeff()/maxCoeff() |
|         按行/列处理         |  matrix.rowwise()/colwise()  |

其他操作都比较简单，这里演示一下按行处理

```cpp
// 取每一行最大的值（输出为列向量）
std::cout << matrix_10_10.rowwise().maxCoeff() << std::endl;
```

然后时矩阵的四则运算，使用符号+-*/即可实现，注意：

* 加减之间只能在相同大小的矩阵之间进行
* 除法只能除以数
* 乘法的规则和矩阵乘法规则相同
* 矩阵之间操作，只能在相同类型间操作，不能float和double混用

这里为了方便解释，举一个矩阵转化的例子

```cpp
Eigen::Matrix3d matrix_3d;
matrix_3d = Eigen::Matrix3d::Ones();
Eigen::Matrix3f matrix_3f;
matrix_3f = Eigen::Matrix3f::Ones();

// 必须转化后才能进行运算
std::cout << matrix_3d + matrix_3f.cast<double>() << std::endl;
```

## 矩阵特殊操作

这一节，我将讲解一下求解矩阵的特征值、特征向量，解方程等等

首先是特征值，特征向量，和解基础的方程

```cpp
Eigen::Matrix<double, 10, 10> A = Eigen::MatrixXd::Random(10, 10);
A = A * A.transpose();  // 保证A至少是半正定、实对称的（否则后续LLT分解容易出错）
Eigen::Matrix<double, 10, 1> b = Eigen::VectorXd::Random(10);

// 求特征值和特征向量
Eigen::EigenSolver<Eigen::MatrixXd> es(A);
std::cout << "The eigenvalues of A are:" << std::endl
          << es.eigenvalues() << std::endl;
std::cout << "The matrix of eigenvectors, V, is:" << std::endl
          << es.eigenvectors() << std::endl
          << std::endl;

// 求方程Ax=b的解，1.直接求逆（较慢）
Eigen::Matrix<double, 10, 1> x1 = A.inverse() * b;
std::cout << "The solution of Ax=b is:" << std::endl
          << x1 << std::endl
          << std::endl;
// 2.利用矩阵分解求解（较快），首先是QR分解
Eigen::Matrix<double, 10, 1> x2 = A.colPivHouseholderQr().solve(b);
std::cout << "The solution of Ax=b is:" << std::endl
          << x2 << std::endl
          << std::endl;
// 3.如果A是正定，可以使用LLT（Lower-Upper Cholesky Decomposition）分解，速度最快
Eigen::Matrix<double, 10, 1> x4 = A.ldlt().solve(b);
std::cout << "The solution of Ax=b is:" << std::endl
          << x4 << std::endl
          << std::endl;
```

有时候，我们要求线性方程的最小二乘解，Eigen中没有对应的函数，不过我们知道$Ax=b$的最小二乘解为：$x=\left(A^TA\right)^{-1}A^Tb$，所以我们可以自己实现一下：

```cpp
Eigen::MatrixXd A(4, 2);  // 输入矩阵 A
A << 1, 2, 3, 4, 5, 6, 7, 8;
Eigen::VectorXd b(4);  // 输出向量 b
b << 1, 2, 3, 4;
Eigen::VectorXd x(2);  // 待求解向量 x

// 最小二乘求解方程
x = (A.transpose() * A).inverse() * A.transpose() * b;
std::cout << "Solution x:\n" << x << std::endl;
```

计算矩阵的n次方Eigen中也没有对应的实现，不过我们知道$A^n=P^{-1}\Lambda^nP$，当然这只在矩阵$A$可对角化时才可以用（比如实对称矩阵），然后我们就可以通过Eigen实现：

```cpp
Eigen::Matrix3d A1 = Eigen::Matrix3d::Random();
A1 = A1 * A1.transpose();  // 保证A1是实对称
Eigen::SelfAdjointEigenSolver<Eigen::Matrix3d> eigensolver(A1);
auto diag_A = eigensolver.eigenvalues();
auto P = eigensolver.eigenvectors();
// 计算A^100
Eigen::Matrix3d A100 = P * diag_A.array().pow(100).matrix().asDiagonal() *
                       P.transpose();  // 对角化后的A的100次方
std::cout << "A100:\n" << A100 << std::endl;
```

# 几何模块

Eigen中有方便进行几何运算的模块，方便我们进行欧式变换等，常用的旋转矩阵、旋转向量、四元数等都可以使用Eigen库中的几何模块进行运算。

## 定义和初始化

这里我先介绍一下几何中常用的各种变换的定义，包括旋转矩阵、旋转向量、四元数等，其实定义的方式不止如下几种，单常用的就如下：

```cpp
#include <Eigen/Core>
#include <Eigen/Dense>
#include <Eigen/Geometry>
#include <iostream>

int main() {
  // 定义旋转矩阵
  Eigen::Matrix3d rotation_matrix = Eigen::Matrix3d::Identity();
  // 旋转向量
  Eigen::AngleAxisd rotation_vector(M_PI / 4, Eigen::Vector3d(0, 0, 1));
  // 欧拉角
  Eigen::Vector3d euler_angles(M_PI / 4, M_PI / 2, M_PI / 4);
  // 欧式变换
  Eigen::Isometry3d T = Eigen::Isometry3d::Identity();
  T.rotate(rotation_vector);  // 按照rotation_vector进行旋转
  T.pretranslate(Eigen::Vector3d(1, 3, 4));  // 把平移向量设成(1,3,4)
  // 四元数
  Eigen::Quaterniond q(1, 0, 0, 0);                     // 顺序为w,x,y,z
  q = Eigen::Quaterniond(Eigen::Vector4d(0, 0, 0, 1));  // 顺序为x,y,z,w
  // 仿射变换
  Eigen::Affine3d T1 = Eigen::Affine3d::Identity();
  T1.rotate(rotation_vector);
  T1.pretranslate(Eigen::Vector3d(1, 3, 4));
  // 射影变换，使用Eigen::Projective3d即可，略

  // 打印输出
  std::cout << "rotation_matrix:\n" << rotation_matrix << std::endl;
  std::cout << "rotation_vector:\n" << rotation_vector.matrix() << std::endl;
  std::cout << "euler_angles:\n" << euler_angles << std::endl;
  std::cout << "Transform matrix:\n" << T.matrix() << std::endl;
  std::cout << "Quaternion:\n" << q.coeffs() << std::endl;
  std::cout << "Affine matrix:\n" << T1.matrix() << std::endl;

  return 0;
}
```

## 互相转换

有时候我们还涉及到上述不同类型之间的转换，大部分的转换使用构造函数即可完成转化，但像**旋转矩阵之类原始的矩阵需要用对应的函数进行转化**：

```cpp
// 旋转矩阵转换为欧拉角
Eigen::Vector3d euler_angles1 = rotation_matrix.eulerAngles(2, 1, 0);
// 旋转矩阵转换为旋转向量
Eigen::AngleAxisd rotation_vector1(rotation_matrix);
// 旋转矩阵转换为四元数
Eigen::Quaterniond q1(rotation_matrix);
// 四元数转换为旋转矩阵
Eigen::Matrix3d rotation_matrix3 = q1.matrix();
// 旋转向量转换为旋转矩阵
Eigen::Matrix3d rotation_matrix4 = rotation_vector1.toRotationMatrix();
// 旋转向量转换为四元数
Eigen::Quaterniond q2(rotation_vector1);
// 四元数转换为旋转向量
Eigen::AngleAxisd rotation_vector2(q2);
// 欧式变换提取旋转矩阵和平移向量
Eigen::Matrix3d rotation_matrix1 = Eigen::Isometry3d(T).rotation();
Eigen::Vector3d translation_vector = Eigen::Isometry3d(T).translation();
// 仿射变换提取旋转矩阵和平移向量
Eigen::Matrix3d rotation_matrix2 = Eigen::Affine3d(T1).rotation();
Eigen::Vector3d translation_vector1 = Eigen::Affine3d(T1).translation();
```

这里要注意一下欧拉角的转换，Eigen为了防止欧拉角万向锁问题，限制了第一个旋转范围为$[0,\pi]$，我们在获取欧拉角时，可以用自己的方法计算：

> 这里其实是欧拉角有冗余性，限制了第一个角的范围后，可以调整第二个角达到和第一个旋转相同的效果，比如yaw=315度，我们可以说欧拉角为$(135°,180°,180°)$，但是大部分情况下，特别是平面机器人运动时，我们要的是$roll=0,pitch=0$条件下$yaw$角，所以这里有时候会遇到bug

```cpp
template <typename T>
T GetYaw(const Eigen::Quaternion<T>& rotation) {
  /* 得到和x轴的夹角 */
  const Eigen::Matrix<T, 3, 1> direction =
      rotation * Eigen::Matrix<T, 3, 1>::UnitX();
  /* 计算xoy平面上和x的夹角 */
  return atan2(direction.y(), direction.x());
}
```

欧拉角转四元数或者其他旋转变换，可以根据欧拉角和旋转向量的定义完成：

```cpp
/* 可以用旋转向量表示 */
q = Eigen::AngleAxisd(ea[0], ::Eigen::Vector3d::UnitZ()) *
Eigen::AngleAxisd(ea[1], ::Eigen::Vector3d::UnitY()) *
Eigen::AngleAxisd(ea[2], ::Eigen::Vector3d::UnitX());
```

## 运算

由于Eigen进行了大量的运算符重载，除了欧拉角，其他变换都可以用左乘旋转矩阵的规则得到变换后的坐标，如下所示：

```cpp
// 对点进行坐标变换
Eigen::Vector3d v(1, 0, 0);
// 可以用四种方式进行坐标变换
Eigen::Vector3d v_rotated = rotation_matrix * v;
Eigen::Vector3d v_rotated1 = rotation_vector * v;
Eigen::Vector3d v_rotated2 = q * v;
Eigen::Vector3d v_rotated3 = T * v;
// 坐标变换之间也能相乘
Eigen::Vector3d v_rotated4 = q * (rotation_vector * (rotation_matrix * v));
// 可以先算出旋转矩阵，然后用旋转矩阵进行坐标变换
Eigen::Matrix3d rotation_matrix5 = (q * rotation_vector * rotation_matrix);
Eigen::Vector3d v_rotated5 = rotation_matrix5 * v;
```

# 平时的记录

1. 常用的一些量

   * 延z轴的单位向量 (0,0,1)：`Eigen::Vector3f::UnitZ()`

2. 返回$[1,0,0]$

   ```c++
   Eigen::Vector3f::Identity()
   ```
   


## 技巧

1. 将`laserscan`转为`pointcloud`

   ```c++
   /* 得到激光角度 */
   Eigen::AngleAxisf rotation(angle, Eigen::Vector3f::UnitZ());
   /* 根据距离投影到点上（first_echo为距离） */
   rotation * (first_echo * Eigen::Vector3f::UnitX());
   ```


# 参考文档

https://blog.csdn.net/hltt3838/article/details/126330805

https://zhuanlan.zhihu.com/p/36706885

[Eigen高阶操作总结 — 子矩阵、块操作_eigen库高级用法-CSDN博客](https://blog.csdn.net/mmLxfz/article/details/90444825)

视觉SLAM14讲
