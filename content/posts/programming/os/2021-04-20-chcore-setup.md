---
title: "chcore 环境搭建(WSL2)"
date: "2021-04-19"
tags: [Operating System]
---

课程主页实验资料的[虚拟机镜像](https://www.cnmooc.org/study/unit/522947.mooc)打不开，无法下载，故选择自己用 `WSL2 Ubuntu20.04`(当然虚拟机的`Ubuntu20.04`或者直接系统，应该也没问题) 搭建 x86 运行的 ARM 环境。(由于网络环境限制，部分过程需要科学上网)

## WSL2 下载 Docker
```bash
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
sudo service docker start # 启动 docker, 关闭 docker 为 stop
```

## 安装交叉编译工具链
`chcore`的交叉编译工具链已经用 `docker` 封装好了，我们只需要下载镜像即可
```bash
sudo docker pull ipads/chcore_builder:v1.0
```

## Qemu 模拟器安装
我的使用的是 `Qemu 5.0.0` 版本，首先安装依赖包，并手动编译
```bash
sudo apt install autoconf automake autotools-dev curl libmpc-dev libmpfr-dev libgmp-dev \
              gawk build-essential bison flex texinfo gperf libtool patchutils bc \
              zlib1g-dev libexpat-dev pkg-config  libglib2.0-dev libpixman-1-dev git tmux python3
wget https://download.qemu.org/qemu-5.0.0.tar.xz
# 解压
tar xvJf qemu-5.0.0.tar.xz
# 编译安装并配置 RISC-V 支持
cd qemu-5.0.0
# 配置 arm 环境
./configure --target-list=aarch64-softmmu
make -j$(nproc)
```
如果你是其他版本的系统，上述依赖包可能不全，需要额外下载。

从源码编译安装的角度来说，个人不推荐使用 `sudo make install` 将编译好的 `二进制` 安装到 `/usr/local/bin` 目录下，容易导致冲突。更推荐的做法是，编译 `~/.bashrc`(如果使用的是 `bash`) ，加入以下：
```
# 请注意，qemu-5.0.0 的父目录可以随着你的实际安装位置灵活调整
export PATH=$PATH:/home/usrname/qemu-5.0.0
export PATH=$PATH:/home/qemu-5.0.0/aarch64-softmmu
```

## 启动 chcore
上述已经搭建好交叉编译环境与硬件模拟环境，现在启动 `chcore`:
```bash
git clone https://gitee.com/ipads-lab/chcore-lab.git
cd chcore-lab
sudo make build
```
这样 `chcore` 的实验环境就搭建完成了。

## Reference
[win10 利用 WSL2 安装 docker 的 2 种方式](https://zhuanlan.zhihu.com/p/148511634)
[rCore 实验环境配置(risc-v)](https://rcore-os.github.io/rCore-Tutorial-Book-v3/chapter0/5setup-devel-env.html)
[现代操作系统：原理与实现（银杏书）](https://ipads.se.sjtu.edu.cn/mospi/)