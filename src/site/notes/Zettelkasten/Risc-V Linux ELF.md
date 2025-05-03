---
{"dg-publish":true,"permalink":"/zettelkasten/risc-v-linux-elf/"}
---

2025-05-03
Status: #idea
Tags: [[Linux\|Linux]],[[Risc-V\|Risc-V]]

# Risc-V Linux ELF
ELF(Executable and Linkable Forma), 是Linux大多数应用程序的默认格式
## 可执行文件
### ELF header
#### Offset and VirtAddr
> 不知道是不是Risc-V特有

offset是指该段在文件中的地址
而virtaddr是定义了程序加载到内存中时期望的虚拟地址
#### FileSize and MemSize
FileSize指该段的文件大小,而MemSize指系统使用该段需要的空间大小,MemSize有可能大于FileSize
比如当该段需要额外的BSS空间时,此时FileSize就会小于MemSize

#### 加载应用到系统
对每一段:
映射virtaddr.align_down到(virtaddr+memsize)这段虚拟地址
对文件需要将(offset..offset+filesize)这段内容加载到虚拟地址空间的(virtaddr..virtaddr+filesize)这段空间
注意用户栈的初始化:
![Pasted image 20250503182814.png](/img/user/Files/Pasted%20image%2020250503182814.png)

___
# References
ArceOS ppt