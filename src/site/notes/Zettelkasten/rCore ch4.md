---
{"dg-publish":true,"permalink":"/zettelkasten/r-core-ch4/"}
---

2025-05-04
Status: #idea
Tags: [[Zettelkasten/OS Camp Stage 2\|OS Camp Stage 2]]

# rCore ch4

本章是页表的实现
对我来说这是rCore最难的章节了,本章内容多,代码量多,而且还与硬件紧密相关,做TuLoongOS的适配花了我不少时间

## 基础知识

主要了解以下这些概念:
- 页表项
- 页表
- tlb
- 多级页表与对应的硬件实现
- 地址空间

## 虚拟地址到物理地址的转换流程

虽然了解了上述概念,但是我对页表机制的实现还是一团雾水
于是尝试梳理了一下[[Zettelkasten/虚拟地址到物理地址的转换流程\|虚拟地址到物理地址的转换流程]]
整理完之后稍微清晰了一些

## MemorySet的作用
整理完后我发现MemorySet其实不参与转换的工作,它是用来管理页表的
因为页表

___
# References