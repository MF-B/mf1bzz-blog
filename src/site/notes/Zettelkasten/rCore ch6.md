---
{"dg-publish":true,"permalink":"/zettelkasten/r-core-ch6/"}
---

2025-05-04
Status: #idea
Tags: [[Zettelkasten/OS Camp Stage 2\|OS Camp Stage 2]]

# rCore ch6

本章介绍文件系统及简单的文件系统实现

内容还是比较多的,但相比页表机制来说要容易理解.

## 调用流程

应用程序进行文件操作时,首先调用操作系统引用的文件系统,即easyfs,然后easyfs经过层层抽象,最终调用的是操作系统实现的块设备接口函数(read_block, write_block)
## 文件系统的结构
自顶向下依次是:
1. 索引节点层 - vfs
2. 块管理器层 - 实际的文件系统结构
3. 磁盘数据结构层 - 定义了超级块/位图/索引节点等数据结构
4. 块缓存层 - 将部分块的内容存储到内存中,提高性能
5. 设备接口层 - 对实际设备的操作

## TuLoongOS
文件系统的部分和rCore类似,但是由于无法使用virtio-blk-device,于是就采用了PCI的方式来添加块设备.
由于没有相关知识,所以设置驱动卡了几天时间
不过好在最后是成功加载上了.
### 解决方案
采用了[virtio-drivers](https://github.com/rcore-os/virtio-drivers)提供的数据结构,通过扫描总线来查找VirtIO块设备,然后进行配置,就是设置bar


___
# References