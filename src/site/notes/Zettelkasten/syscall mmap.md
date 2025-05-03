---
{"dg-publish":true,"permalink":"/zettelkasten/syscall-mmap/"}
---

2025-05-03
Status: #idea
Tags: [[Linux\|Linux]],[[Zettelkasten/OS Camp Stage 3\|OS Camp Stage 3]]

# syscall mmap
作用是映射文件到一个虚拟地址空间里
应用程序提供要映射文件的地址与长度等,由操作系统负责映射
mmap使用懒加载,即使用时再分配空间
## api
```rust
fn sys_mmap(
    addr: *mut usize, // 如果不为NULL，内核会在此地址创建映射；否则，内核会选择一个合适的虚                          拟地址。大部分情况不指定虚拟地址，意义不大，而是让内核选择返回一个                           地址给用户空间使用。
    length: usize,    // 表示映射到进程地址空间的大小。
    prot: i32,        // 内存区域的读/写/执行属性。
    flags: i32,       // 内存映射的属性，共享、私有、匿名、文件等。
    fd: i32,          // 表示这是一个文件映射，fd是打开文件的句柄。如果是文件映射，需要指定                          fd；匿名映射就指定一个特殊的-1。
    _offset: isize,   // 在文件映射时，表示相对文件头的偏移量；返回的地址是偏移量对应的虚拟                           地址。
) -> isize
```
## ArceOS实现步骤
1. addr为NULL时获取分配的虚拟地址
	1. 在当前任务的地址空间中寻找可用的虚拟地址范围,查询范围是用户可用的虚拟地址空间
	2. 将找到的虚拟地址赋值给addr
2. 获取文件内容
	1. 设置懒加载标志is_lazy,默认为true
	2. fd != -1代表这是文件映射,由于需要读取文件内容到缓冲区并写入用户地址空间.这里设置is_lazy为false
3. 解析映射权限
	1. 设置MappingFlags::USER
	2. 使用MmapProt来解析,分别对应到读/写/执行的MappingFlags
4. 如果不是懒加载并且fd!=-1,加载文件到用户地址空间

___
# References
[Linux内存管理 (9)mmap(补充) - ArnoldLu - 博客园](https://www.cnblogs.com/arnoldlu/p/9367253.html)
[Linux 内存映射函数 mmap（）函数详解 - 知乎](https://zhuanlan.zhihu.com/p/477641987)