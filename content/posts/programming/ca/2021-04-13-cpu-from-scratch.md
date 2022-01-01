---
title: "OpenMIPS处理器"
date: "2021-04-13"
tags: [Verilog, Computer Architecture, CPU]
---

最近在看《自己动手写CPU》，实现一个 5级流水线的 OpenMIPS处理器。这篇文章记录保存实现过程中用到的元器件接口以及系统结构结构图，主要为图片内容，方便查阅。

## 教学版OpenMIPS处理器蓝图

### 外部接口
{{< figure src="/ca/2021-04-13-cpu-from-scratch/2021-04-13-cpu-from-scratch-1.png" >}}

### 文件说明
{{< figure src="/ca/2021-04-13-cpu-from-scratch/2021-04-13-cpu-from-scratch-2.png" title="流水线各个阶段对应的模块、文件" >}}

### 数据相关
{{< figure src="/ca/2021-04-13-cpu-from-scratch/2021-04-13-cpu-from-scratch-3.png" title="逻辑、移位指令的数据相关" >}}
{{< figure src="/ca/2021-04-13-cpu-from-scratch/2021-04-13-cpu-from-scratch-4.png" title="HI、LO寄存器带来的数据相关" >}}

### 数据流图
{{< figure src="/ca/2021-04-13-cpu-from-scratch/2021-04-13-cpu-from-scratch-5.png" title="只实现一条指令的ori时的数据流图" >}}
{{< figure src="/ca/2021-04-13-cpu-from-scratch/2021-04-13-cpu-from-scratch-6.png" title="添加了数据前推的OpenMIPS数据流图（回写、译码数据相关通过判断即可解决）" >}}
{{< figure src="/ca/2021-04-13-cpu-from-scratch/2021-04-13-cpu-from-scratch-7.png" title="添加了移动操作指令后的数据流图" >}}
{{< figure src="/ca/2021-04-13-cpu-from-scratch/2021-04-13-cpu-from-scratch-8.png" title="解决HI、寄存器带来的数据相关问题后的数据流图" >}}
{{< figure src="/ca/2021-04-13-cpu-from-scratch/2021-04-13-cpu-from-scratch-9.png" title="增加算术操作后的数据流图" >}}
{{< figure src="/ca/2021-04-13-cpu-from-scratch/2021-04-13-cpu-from-scratch-10.png" title="为实现转移指令而修改的数据流图" >}}
{{< figure src="/ca/2021-04-13-cpu-from-scratch/2021-04-13-cpu-from-scratch-11.png" title="实现mips32指令集中所有整数指令之后的数据流图" >}}

### 系统结构图
{{< figure src="/ca/2021-04-13-cpu-from-scratch/2021-04-13-cpu-from-scratch-12.png" title="原始的OpenMIPS五级流水线系统结构图（用于实现ori指令）" >}}
{{< figure src="/ca/2021-04-13-cpu-from-scratch/2021-04-13-cpu-from-scratch-13.png" title="为实现数据前推而对OpenMIPS结构所做的修改" >}}
{{< figure src="/ca/2021-04-13-cpu-from-scratch/2021-04-13-cpu-from-scratch-14.png" title="为实现移动操作而对OpenMIPS结构所做的修改" >}}
{{< figure src="/ca/2021-04-13-cpu-from-scratch/2021-04-13-cpu-from-scratch-15.png" title="为实现流水线暂停机制而对系统结构所做的修改" >}}
{{< figure src="/ca/2021-04-13-cpu-from-scratch/2021-04-13-cpu-from-scratch-16.png" title="为实现转移指令而对系统结构所做的修改" >}}

### SOPC 实现
{{< figure src="/ca/2021-04-13-cpu-from-scratch/2021-04-13-cpu-from-scratch-17.png" title="最小SOPC实现" >}}

### 确定指令种类
{{< figure src="/ca/2021-04-13-cpu-from-scratch/2021-04-13-cpu-from-scratch-18.png" title="逻辑、移位操作与空指令" >}}
{{< figure src="/ca/2021-04-13-cpu-from-scratch/2021-04-13-cpu-from-scratch-19.png" title="移动操作指令" >}}
{{< figure src="/ca/2021-04-13-cpu-from-scratch/2021-04-13-cpu-from-scratch-20.png" title="算术操作指令" >}}
{{< figure src="/ca/2021-04-13-cpu-from-scratch/2021-04-13-cpu-from-scratch-21.png" title="算术操作指令" >}}
{{< figure src="/ca/2021-04-13-cpu-from-scratch/2021-04-13-cpu-from-scratch-22.png" title="乘累加、乘累减指令" >}}
{{< figure src="/ca/2021-04-13-cpu-from-scratch/2021-04-13-cpu-from-scratch-23.png" title="转移指令" >}}
