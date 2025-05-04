---
{"dg-publish":true,"permalink":"/zettelkasten/r-core-ch7/"}
---

2025-05-04
Status: #idea
Tags: [[Zettelkasten/OS Camp Stage 2\|OS Camp Stage 2]]

# rCore ch7

本章主要实现了进程间的管道通信以及命令行参数

## 命令行参数
是通过读取栈上的数据来实现的,exec系统调用时将用户栈设置好,用户程序就能正确读取参数了


___
# References