---
{"dg-publish":true,"permalink":"/zettelkasten/file-system/"}
---

2025-05-02
Status: #idea
Tags: [[Zettelkasten/OS Camp Stage 3\|OS Camp Stage 3]]

# file system
## ArceOS
本文使用自顶向下分析ArceOS文件系统的组成
### app
以u_8_0为例
通过axstd::fs来调用进行文件操作
### axstd
对外提供dir和file的api
axstd实际调用了arceos_api::fs的接口
### arceos_api
对外提供了一些文件系统的类型和接口
具体来说就是把axfs的一些文件类型包装成新的类型,并实现部分方法提供给上层
### axfs
fops.rs使用了axfs_vfs包提供的文件系统来定义File和Dir类型对外提供
root.rs定义了ROOT_DIR
#### root
root目录数据结构包含main_fs和mounts
main_fs是实现了VfsOps接口的一个文件系统
mounts是一个MountPoint数组
ROOT_DIR由axruntime通过调用root.rs的init_rootfs来初始化
可以看出main_fs体现了系统使用的文件系统类型(有myfs和fatfs两种)
初始化时还mount了/dev,/tmp,/proc,/sys这些MountPoint
##### mount
MountPoint是由{path, fs}组成的,它和根目录一样都维护了一个文件系统,但是它不维护mounts列表,所以它不支持mount操作
根节点的mount操作,是在/目录下新建了以/开头的一个目录
并对传入的新创建的文件系统进行mount操作
以fatfs+ramfs为例,它将ramfs的root设置为根目录
在新建一个MountPoints并维护到根目录的mounts列表中
##### rename
使用lookup_mounted_fs进行操作
具体来说这个lookup_mounted是将操作传递到了root目录的mounts列表中的其他文件系统上(或者自身)

### ramfs_rename
题目要求支持ramfs的rename操作,由以上分析我们可以看到rename操作的传递路径是:
axstd->arceos_api->axfs::root:rename
所以是通过root来进行重命名操作的
以/开头的路径为例,操作被传递到了ROOT_DIR
之后又通过lookup_mounted_fs传递到了挂载/tmp的文件系统上,即ramfs
查找定义可知里面没有实现rename方法
我们在arceos目录的Cargo.toml里给axfs_ramfs打上patch
使用arceos目录里的axfs_ramfs
在其dir.rs中添加rename方法
即可实现题目要求
#### rename方法
- 路径问题
	- 由于axfs中根目录的rename方法只处理了源路径,所以需要把目的路径也处理一下(去除mount路径)
- ramfs的rename方法实现可以参照remove
- 为节点添加rename_node方法
	- 去除children中的旧名称节点,添加新名称节点

___
# References
ArceOS ppt