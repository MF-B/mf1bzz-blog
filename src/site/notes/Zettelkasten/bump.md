---
{"dg-publish":true,"permalink":"/zettelkasten/bump/"}
---

2025-04-30
Status: #idea
Tags: [[Memory Allocator\|Memory Allocator]],[[Zettelkasten/OS Camp Stage 3\|OS Camp Stage 3]]

# bump
## Bump 内存分配算法
![Pasted image 20250430162700.png](/img/user/Files/Pasted%20image%2020250430162700.png)
## 要点
### dealloc(以Byte分配器为例)
dealloc使用计数器实现,alloc时计数+1,dealloc时-1.
计数为0时则重置next到start
### 对齐
给定一个Layout,包含size和align
size和align都是以字节为单位的,而内存地址也是以字节为单位的
我的实现是只考虑前端对齐
即每次alloc时将对齐后的地址发送给用户
#### 对齐算法
给定地址addr和align即可
aligned_addr = (((addr - start) + align)/align) * align + start
得出相对地址->(以align分块)得到当前地址所在的块的下一个块的编号->得到下一个块的相对地址->得到绝对地址
实现了向高地址对齐




___

# References
[Bump 分配器：简单高效的内存分配器 - ByteZoneX社区](https://www.bytezonex.com/archives/th-d7RCV.html)
[11. 内存分配器设计 - 《用 Rust 编写操作系统(Writing an OS in Rust中文版)》 - 书栈网 · BookStack](https://www.bookstack.cn/read/writing-an-os-in-rust/11-allocator-designs.md)
