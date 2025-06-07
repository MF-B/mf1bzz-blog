---
{"dg-publish":true,"permalink":"/zettelkasten/v-dso/"}
---

2025-05-22
Status: #idea
Tags: [[Zettelkasten/ArceOS宏内核\|ArceOS宏内核]]

# vDSO
## 分析
### 星绽Asterinas
#### 映射
进程创建时通过`map_vdso_to_vm`将vDSO文件映射到进程虚拟内存
设置进程的aux_vec指向AT_SYSINFO_EHDR指向vDSO文件的ELF头
#### 内容
Asterinas使用预制的vdso.so

## 原理


## 设计


___
# References