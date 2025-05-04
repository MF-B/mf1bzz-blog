---
{"dg-publish":true,"permalink":"/zettelkasten/tu-loong-os/"}
---

2025-05-04
Status: #idea
Tags: [[Zettelkasten/rCore ch2\|rCore ch2]]

# TuLoongOS与向量指令集

在TuLoongOS对rCore ch2的适配时,出现了卡死的情况
## 原因与解决办法
由于对问题发生的原因没有什么头绪,于是就直接开始用gdb调试,发现运行到以v开头的向量指令时会直接卡死
本来我还以为是qemu对loongarch支持不完全导致的,于是更换了新的qemu版本(9.2.3),结果依然是卡死.
于是我查看了arceos编译出来的镜像有没有向量指令,结果是没有;秉持着解决不了qemu就解决编译器的想法,我修改编译器到了老版本``nightly-2025-01-18``,虽然依然很新,但是确实不会再生成向量指令集了,TuLoongOS也能正常运行了.
可惜我错怪qemu了,qemu卡死的真正原因是我没有打开龙芯cpu对向量指令集的支持,龙芯cpu有个专门的寄存器控制向量指令的运行,正确设置之后才能执行向量指令.设置之后就能成功运行TuLoongOS了,顺便把rustc又换回了最新版


___
# References