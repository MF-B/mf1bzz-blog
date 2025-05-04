---
{"dg-publish":true,"permalink":"/zettelkasten/r-core-ch2/"}
---

2025-05-04
Status: #idea
Tags: [[Zettelkasten/OS Camp Stage 2\|OS Camp Stage 2]]

# rCore ch2

本章是批处理系统的实现

主要难点在于理解CPU的特权级机制
以及__alltrap和__restore的理解

另外还有一个卡了我不少时间的[[Zettelkasten/TuLoongOS与向量指令集\|Bug]]
## 特权级
特权级区分是通过CPU设置某个CSR寄存器来实现的
CPU只能运行当前特权级允许的指令,否则就会进入异常
不过这只是进入异常的其中一个原因,实际上还有syscall指令/非法地址/指令不存在等等一系列操作都会引发异常
### loongarch
cargo有loongArch64包提供loongarch架构的寄存器类型
龙芯不需要保存类似risc-v的sstatus寄存器,因为龙芯有个专门的prmd寄存器,记录了原本的特权级,每次异常时恢复成prmd寄存器记录的特权级

## 应用程序
本部分参考教程即可,没有卡住的点


___
# References