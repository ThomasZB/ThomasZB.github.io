---
title: verilog基本语法和vivado基本使用
date: 2022-09-21 19:24:26
tags:
categories: 嵌入式
---

<p align="center">
    记录一下上课和平时积累的
</p> 

<!--more-->

# 语法

verilog的语法相比其他语言比较少，用于描述硬件，要实现功能主要还是模块之间的组合

## 基本结构和语句要求

每个.v文件就是一个module，代码写再`module`和`endmoudule`之间，`moudule`后跟模块名，以及模块的输入输出，模块内部实现功能。输入输出有两种写法，看自己习惯：

* 放在括号内

```verilog
module a(input a, b, output c, d);
    ...
endmodule
```

* 放在括号外

```verilog
module a(a,b,c,d);
    input a,b;
    output c,d;
    ...
endmodule
```

## 数据类型

1. `parameter`：常量

   * 常量位宽和进制设置

   ```verilog
   parameter a = 3'b101
   ```

2. `reg`：寄存器，主要在时序逻辑电路中，生成出来主要是D触发器

3. `wire`：可以理解为线

4. 设置位宽

```verilog
reg [3:0] a
```

## 逻辑功能描述

1. `assign`：如果逻辑能用一句话描述清楚可以使用assign语句

   ```verilog
   assign x = (b & ~c)
   ```

2. `always`：如果逻辑比较复杂，使用always块描述

   * 敏感列表：在`always @()`中的括号里面，也就是该always块触发条件，常见的条件有：
     * `posedge`：信号上升沿
     * `negedge`：信号下降沿
     * `*`：所有，一般描述组合逻辑

   ```verilog
   always @(posedge clk) begin // 每当时钟上升沿到来时执行
           if (load)
               out = data;
           else
               out = data + 1 + cin;
       end
   ```

## 关键字

关键字是实现定义好的符号

1. 门结构
   * and/xand：与/与非
   * not：非
   * or/xor：或/或非
   * buf：

2. 选择结构

   * `if-else`结构：综合出来一般带优先级，多级的选择结构

   ```verilog
   if (load)
       out = data;
   else
       out = data + 1 + cin;
   ```

   * `case`结构

   * `casex`结构

3. 其他

   * assign、always、while。。。等等

## 运算符

|         运算符         |     符号      |                          功能                          |
| :--------------------: | :-----------: | :----------------------------------------------------: |
|       关系运算符       | <，>，<=，>=  |                      和c语言一样                       |
| 归约运算符和按位运算符 | &、^、\|、~\| |  一元运算符，归约运算，所有bit项相与；二元时，按位与   |
|       逻辑运算符       |  !、&&、\|\|  |                      和c语言一样                       |
|        位运算符        |  ~、&、\|、^  |                      和c语言一样                       |
|        移位运算        |    <<、>>     |                      和c语言一样                       |
|       条件运算符       |      ?:       |         和c语言一样（一般在assign语句中使用）          |
|      位拼接运算符      |      {,}      | 实现增长位宽的作用，将多个数据拼接，eg：d = {a, b, c}; |

## 模块化设计

常用的一些模块往往设计成一个module，其他模块里面可以直接调用，使用方法如下：

1. 写好需要的module

2. 在要用的模块里面实例化：对input和output进行连线，output只能连到wire类型的线上

   ```verilog
   /* 实例化，将线连接到自己的例程 */ 
   key_filter u_key_filter(            // 模块名
       .clk        (clk)       ,   // 时钟输入信号
       .key        (key)       ,   // 复位信号，低电平有效
       .rst_n      (rst_n)     ,
       .key_out    (key_out)
   );
   ```

3. 如果当前文件是最终的，设置为顶层后再全部编译

## 技巧

1. 可以把输出定义为`wire`，赋值使用`reg`，在外面用`assign`对`wire`赋值

## 其他默认规则

1. `if`和`case`语句：最好把所有状态描述完（写全），不然综合会出现锁存器（有些状态没描述，锁存上一次状态）
2. 在`always`中赋值的变量，一定要定义为`reg`类型
3. `if-else`比`switch-case`更占空间，没优先级最好用`switch-case`

# 常见模块

## 状态机

状态机可以实现基本所有功能

## 多路复用器Multiplexer

1. 画出原理图

   ![image-20220921151541434](https://pic-1302177449.cos.ap-chongqing.myqcloud.com/blog_pic/%20image-20220921151541434.png)

2. 写输入输出

   ```verilog
   module Mux8 (input sel, input [7:0] data1, data0, output [7:0] bus1);
       
   endmodule
   ```

3. 写逻辑

   * 简单的

   ```verilog
   assign bus1 = sel ? data1: data0;
   ```

   * 复杂的

   ```verilog
   always @(*)
       begin
           case(sel)
               0:
               1:
               default:
           endcase
       end
   ```

4. 其他结构（带优先级的）

   ![image-20220921152341565](https://pic-1302177449.cos.ap-chongqing.myqcloud.com/blog_pic/%20image-20220921152341565.png)

   * 代码实现

   ```verilog
   always @(*)
       begin
           if();
           else if();
           else;
       end
   ```

## 编码器和译码器Encoder/Decoder

1. 确定输入输出关系

2. 代码实现

   * 确定输入输出

   ```verilog
   module Code42(F, I, en);
       output [1:0] F;
       input [3:0] I;
       input en;
       reg [1:0] t;
       assign F = en ? t ： 2‘bz;
   endmodule;
   ```

   * 普通

   ```verilog
   always @(I)
       begin
           case(I)
               1: t = 0;
               2: t = 1;
           default: t=0;
        end
   ```

   * 带优先级的（`if-else`）

   ```verilog
   always @ (I)
       begin
           if(I[3]) t = 2'b11;
           else if(I[2]) t = 2'b10;
           else t = 2'b00;
       end
   ```

   * 带优先级的（`switch-case`）

   ```verilog
   always @ (I)
       begin
           casex(Data)
               3'b1xx: t = 2'b11;
               3'b01x: t = 2'b10;
               default: t = 2'bx;
           endcase
       end
   ```

## 加法器

1. 写真值表

2. 代码描述

   * 普通

   ```verilog
   module fulladder(input a, b, cin, output sum, cout);
       assign sum = a ^ b ^ cin;
       assign cout = (a & b) | (a & cin) | (b & cin);
   endmodule
   ```

   * 知道结构（结构化描述）

   ![image-20220921160512260](https://pic-1302177449.cos.ap-chongqing.myqcloud.com/blog_pic/%20image-20220921160512260.png)

   ```verilog
   module Add_half(sum, c_out, a, b);
       output sum, c_out;
       input a, b;
       xor(sum, c, b);
       and(c_out, a, b);
   endmodule
   ```


# vivado使用

