---
{"dg-publish":true,"permalink":"/zettelkasten/os-camp-stage-2/"}
---

2025-05-04
Status: #MOC
Tags: [[Zettelkasten/OS Camp\|OS Camp]]

# OS Camp Stage 2

## 感想

二阶段是rCore学习
rCore一共九章,篇幅很大,前前后后大概花了我20天的时间,整个过程还是非常有意思的
我是通过观看[rCore-Tutorial-Book](https://rcore-os.cn/rCore-Tutorial-Book-v3/index.html)来学习的,同时写了一个loongarch架构的rCore---[TuLoongOS](https://github.com/MF-B/TuLoongOS).
不过看了两章之后发现已经有大佬做过类似工作了[rCoreloongArch](https://github.com/Godones/rCoreloongArch)
不过TuLoongOS原本的目的就是学习用,所以也就继续写了下去,同时也参考rCoreloongArch的实现

二阶段对我个人的提升很大,由于之前没有学过操作系统,rCore的学习让我理解操作系统是什么,之前一些对计算机的模糊概念也渐渐清晰了起来,也顺便提高了我的Rust编程能力

看的比较多的就是[龙芯的参考手册](https://www.loongson.cn/uploads/images/2023041918133323805.%E9%BE%99%E8%8A%AF%E6%9E%B6%E6%9E%84%E5%8F%82%E8%80%83%E6%89%8B%E5%86%8C%E5%8D%B7%E4%B8%80_r1p03.pdf)和Risc-V的参考手册了 

## 学习记录

- [[Zettelkasten/rCore ch1\|第一章：应用程序与基本执行环境]]
- [[Zettelkasten/rCore ch2\|第二章：批处理系统]]
- [[Zettelkasten/rCore ch3\|第三章：多道程序与分时多任务]]
- [[Zettelkasten/rCore ch4\|第四章：地址空间]]
- [[Zettelkasten/rCore ch5\|第五章：进程]]
- [[Zettelkasten/rCore ch6\|第六章：文件系统]]
- [[Zettelkasten/rCore ch7\|第七章：进程间通信与 I/O 重定向]]
- [[Zettelkasten/rCore ch8\|第八章：并发]]

## 实验记录
以下内容不公开,主要是因为荣誉准则的内容
- [[Zettelkasten/rCore lab1\|lab1]] - 第三章练习
- [[Zettelkasten/rCore lab2\|lab2]] - 第四章练习
- [[Zettelkasten/rCore lab3\|lab3]] - 第五章练习
- [[rCore lab4\|lab4]] - 第六章练习
- [[rCore lab5\|lab5]] - 第八章练习


___
# References
[rCore-Tutorial-Book-v3 3.6.0-alpha.1 文档](https://rcore-os.cn/rCore-Tutorial-Book-v3/index.html)
[Godones/rCoreloongArch](https://github.com/Godones/rCoreloongArch)
[2023041918133323805.龙芯架构参考手册卷一_r1p03.pdf](https://www.loongson.cn/uploads/images/2023041918133323805.%E9%BE%99%E8%8A%AF%E6%9E%B6%E6%9E%84%E5%8F%82%E8%80%83%E6%89%8B%E5%86%8C%E5%8D%B7%E4%B8%80_r1p03.pdf)
[RISC-V手册](http://staff.ustc.edu.cn/~llxx/cod/reference_books/RISC-V-Reader-Chinese-v2p12017.pdf)
[riscv-spec-v2.1中文版.pdf](http://file.whycan.com/files/members/7090/riscv-spec-v2.1%E4%B8%AD%E6%96%87%E7%89%88.pdf)