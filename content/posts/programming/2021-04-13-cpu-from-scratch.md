---
title: "OpenMIPS处理器"
date: "2021-04-13"
tags: [Verilog, Computer Architecture, CPU]
---

最近在看《自己动手写CPU》，实现一个 5级流水线的 OpenMIPS处理器。这篇文章记录保存实现过程中用到的元器件接口以及系统结构结构图，主要为图片内容，方便查阅。

## 教学版OpenMIPS处理器蓝图

### 外部接口
{{< figure src="https://i.loli.net/2021/04/13/aFhj4JSXHNQOuRY.png" title="教学版OpenMIPS处理器的外部接口" >}}

### 文件说明
{{< figure src="https://i.loli.net/2021/04/13/NwtnShksB3OdIQf.png" title="流水线各个阶段对应的模块、文件" >}}

### 数据相关
{{< figure src="https://i.loli.net/2021/04/13/a3Np1xCTHBtYskR.png" title="逻辑、移位指令的数据相关" >}}
{{< figure src="https://i.loli.net/2021/04/13/Z2sEGkg6qRu519l.png" title="HI、LO寄存器带来的数据相关" >}}

### 数据流图
{{< figure src="https://i.loli.net/2021/04/13/PbHqGALiNfJZa9E.png" title="只实现一条指令的ori时的数据流图" >}}
{{< figure src="https://i.loli.net/2021/04/13/Lai8EHwzjxX7ZGq.png" title="添加了数据前推的OpenMIPS数据流图（回写、译码数据相关通过判断即可解决）" >}}
{{< figure src="https://i.loli.net/2021/04/13/ETu3b12zFl5VUMX.png" title="添加了移动操作指令后的数据流图" >}}
{{< figure src="https://i.loli.net/2021/04/13/W9IbPADV4hCjuQo.png" title="解决HI、寄存器带来的数据相关问题后的数据流图" >}}
{{< figure src="https://i.loli.net/2021/04/13/bUsrY8mgJ1jwuNS.png" title="实现mips32指令集中所有整数指令之后的数据流图" >}}

### 系统结构图
{{< figure src="https://i.loli.net/2021/04/13/IJyOg4CcMqLXlmk.png" title="原始的OpenMIPS五级流水线系统结构图（用于实现ori指令）" >}}
{{< figure src="https://i.loli.net/2021/04/13/UnOLuGprvg1jCK2.png" title="为实现数据前推而对OpenMIPS结构所做的修改" >}}
{{< figure src="https://i.loli.net/2021/04/13/CrVJicbT15NqD4h.png" title="为实现移动操作而对OpenMIPS结构所做的修改" >}}

### SOPC 实现
{{< figure src="https://i.loli.net/2021/04/13/sYwiIKGdkCN9zBH.png" title="最小SOPC实现" >}}

### 确定指令种类
{{< figure src="https://i.loli.net/2021/04/13/Zjyk9boYhcsgr6e.png" title="逻辑、移位操作与空指令" >}}
{{< figure src="https://i.loli.net/2021/04/13/zVPDNA4UGbCXdln.png" title="移动操作指令" >}}
{{< figure src="https://i.loli.net/2021/04/17/VJj1usxN4a5mivO.png" title="算术操作指令" >}}
