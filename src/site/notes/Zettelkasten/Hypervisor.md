---
{"dg-publish":true,"permalink":"/zettelkasten/hypervisor/"}
---

2025-05-03
Status: #idea
Tags: [[Zettelkasten/OS Camp Stage 3\|OS Camp Stage 3]]

# Hypervisor
## 页表相关
Host主机使用hgatp来设置页表基址
hgatp: 
![Pasted image 20250504214618.png](/img/user/Files/Pasted%20image%2020250504214618.png)
### 地址转换流程
1. Guest虚拟地址--通过vsatp转换-->Guest物理地址
2. Guest物理地址--通过hgatp转换-->Host物理地址
2会发生在Guest发生页表访问错误时出现

## ArceOS exercise
### simple_hv
原本以为需要让HOST处理特权指令和缺页,
但是看ppt的要求输出貌似也没有处理,于是直接sepc+4并设置a0和a1
这样就能通过测例了.



___
# References
[RISC-V 特权模式：机器模式 | BalanceTWK的博客](https://balancetwk.github.io/2020/12/05/hexo_blog/RISC_V_Note/RISC-V%20%E7%89%B9%E6%9D%83%E6%A8%A1%E5%BC%8F%EF%BC%9A%E6%9C%BA%E5%99%A8%E6%A8%A1%E5%BC%8F/)