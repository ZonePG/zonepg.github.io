---
title: "轻量级Verilog仿真环境搭建iVerilog+GTKWave"
date: "2021-03-25"
tags: [Verilog, Computer Architecture]
---

由于体系结构课程要用到`verilog`来实现`MIPS`CPU，之前上组成原理课程与数字逻辑课程用过`ModelSim`与`Vivado`，这两软件实在是太笨重了，同学们基本都是只拿来做做仿真运行就行了，`ModelSim`软件`bug`巨多，而`Vivado`编译运行速度实在是一言难尽。所以在网上找了一些轻量级`Verilog`仿真运行方案，`Icarus Verilog + GTKWave`似乎是一个不错的选择

`iVerilog`开源、支持各平台，有语法检查，仿真，可以满足课程需求。

这里给出Windows环境搭建，并给出我个人的食用方案。

## iVerilog 安装
[iverlog](https://bleyer.org/icarus/)，我个人下载的是`iverilog-v11-20201123-x64_setup.exe [18.1MB]`即可（不知道为什么2021版的突然突然44M，不过比 Vivado 10个G总舒服多了)。一路点下去，记得选中设置环境变量。  

将会安装好以下 3 个工具：
- `iverlog`：编译 `verilog`代码
- `vvp`：将可执行文件生成仿真波形
- `gtkwave`：打开仿真波形文件，显示波形

安装好后，可以在windows命令行中输入`iverilog`, `vvp`, `gtkwave`检查环境变量是否设置成功。

## 食用方式
`iverilog`可以指定一些参数编译运行代码，下面给出我个人的食用方式，以下是文件目录树，verilog代码参考《自己动手写CPU》第二章 demo代码：
```
├── pc_reg.v(PC寄存器实现)
├── rom.v(指令存储器实现)
├── rom.data(指令存储器数据)
├── inst_fetch.v(综合模块实现)
├── inst_fetch_tb.v(测试模块实现)
├── run.bat(编译运行脚本)
├── rmfile.bat(删除输出文件脚本)
```
- `pc_reg.v`
```verilog
module pc_reg(input wire clk,
              input wire rst,
              output reg[5:0] pc,
              output reg ce); // 指令存储器使能信号
    
    always @(posedge clk) begin
        if (rst == 1'b1) begin
            ce <= 1'b0; // 在复位信号有效的时候，指令存储器使能信号无效
        end else begin
            ce <= 1'b1; // 复位信号无效的时候，指令存储器使能信号有效
        end
    end
    
    always @(posedge clk) begin
        if (ce == 1'b0) begin
            pc <= 6'h00;    // 指令存储器使能信号有效时，pc保持为0
        end else begin
            pc <= pc + 1'b1;
        end
    end

endmodule
```
- `rom.v`
```verilog
module rom (input wire ce,
            input wire[5:0] addr,   // 要读取的指令地址
            output reg[31:0] inst); // 读出的指令
    reg[31:0] rom[63:0];

    initial begin
        $readmemh("rom.data", rom);
    end

    always @(*) begin
        if (ce == 1'b0) begin
            inst <= 32'h0;
        end else begin
            inst <= rom[addr];
        end
    end
endmodule
```
- `rom.data`
```
01010101
02020202
03030303
04040404
```
- `inst_fetch.v`
```verilog
module inst_fetch (input wire clk,
                   input wire rst,
                   output wire[31:0] inst_o);
    wire[5:0] pc;
    wire rom_ce;
    
    // PC 模块的实例化
    pc_reg pc_reg0(.clk(clk), .rst(rst), .pc(pc), .ce(rom_ce));
    
    // 指令存储器ROM的实例化
    rom rom0(.ce(rom_ce), .addr(pc), .inst(inst_o));
    
endmodule
```
- `inst_fetch_tb.v`
```verilog
module inst_fetch_tb;

    /*iverilog */
    initial
    begin            
        $dumpfile("wave.vcd");        //生成的vcd文件名称
        $dumpvars(0, inst_fetch_tb);    //tb模块名称
    end
    /*iverilog */

    /*
     * 第一段：数据说明
     */
    reg clk;    // 激励信号 clk
    reg rst;    // 激励信号 rst
    wire[31:0] inst; // 显示信号 inst, 取出的指令

    /*
     * 第二段：激励向量定义
     */
    // 定义 clk，每隔 10 个时间单位，翻转，即 50 MHz的时钟
    // 仿真的时候，一个时间单位默认是 1ns
    initial begin
        clk = 1'b0;
        forever begin
            #10 clk = ~clk;
        end
    end

    // 定义 rst 信号，最开始为 1，复位有效，过了 195个时间单位
    // 设置 rst 信号的值为0， 复位信号无效，复位结束，再运行1000ns，暂停仿真
    initial begin
        rst = 1'b1;
        #195 rst = 1'b0;
        #1000 $stop;
    end


    /*
     * 第三段：待测试模块实例化
     */
    inst_fetch inst_fetch0(
        .clk(clk),
        .rst(rst),
        .inst_o(inst)
    );
    
endmodule
```
- `run.bat`: 这是一个简单的编译运行批处理脚本，这三个工具还有各种参数，后续再探索
```batch
% Start compiling %
iverilog -o wave %*
% Generating wave file %
vvp -n wave -lxt2
% Opening wave file %
gtkwave wave.vcd
```
- `rmfile.bat`: 删除生成的文件，方便重新编译
```batch
del wave *.vcd
```
### 编译运行仿真
命令行输入`.\run.bat file1 file2`即可，这里是`.\run.bat .\inst_fetch_tb.v .\inst_fetch.v .\pc_reg.v .\rom.v `

{{< figure src="https://i.loli.net/2021/03/25/wxJjlt48QAvzBYO.png" title="运行结果" >}}

`Insert`=>`Signals`选中信号，即可显示波形，鼠标滚轮移动波形时序位置。

## Reference
- [全平台轻量开源verilog仿真工具iverilog+GTKWave使用教程](https://zhuanlan.zhihu.com/p/95081329)
- 《自己动手写CPU》第二章