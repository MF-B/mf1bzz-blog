---
{"dg-publish":true,"permalink":"/zettelkasten/r-core-ch5/"}
---

2025-05-04
Status: #idea
Tags: [[Zettelkasten/OS Camp Stage 2\|OS Camp Stage 2]],[[Schedule\|Schedule]]

# rCore ch5
本章主要是实现进程这一抽象,并支持了进程相关的一些基本的系统调用fork/exec/wait等

难度比上一章要低很多,因此没有很多要记录的点
## TCB
TCB是进程管理的核心数据结构,操作系统通过TCB来管理进程的资源
同时也通过修改TCB来支持各种系统调用,比如fork/exec等

## 进程调度算法
本章最后介绍了很多进程调度算法,可以作为以后的参考.


___
# References
[进程调度 - rCore-Tutorial-Book-v3 3.6.0-alpha.1 文档](https://rcore-os.cn/rCore-Tutorial-Book-v3/chapter5/4scheduling.html)