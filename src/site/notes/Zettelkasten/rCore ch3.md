---
{"dg-publish":true,"permalink":"/zettelkasten/r-core-ch3/"}
---

2025-05-04
Status: #idea
Tags: [[Zettelkasten/OS Camp Stage 2\|OS Camp Stage 2]]

# rCore ch3

和上一章相比,本章修改了应用程序运行的地址,把多个应用程序依次加载到一块空间里,同时打开了时钟中断,添加了任务队列和抢占逻辑

本章没有特别难的点,跟着教程走即可

## TuLoongOS
要打开时钟中断需要查看一下loongarch手册

___
# References
[2023041918133323805.龙芯架构参考手册卷一_r1p03.pdf](https://www.loongson.cn/uploads/images/2023041918133323805.%E9%BE%99%E8%8A%AF%E6%9E%B6%E6%9E%84%E5%8F%82%E8%80%83%E6%89%8B%E5%86%8C%E5%8D%B7%E4%B8%80_r1p03.pdf)