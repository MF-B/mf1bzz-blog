---
{"dg-publish":true,"permalink":"/zettelkasten/r-core-ch1/"}
---

2025-05-04
Status: #idea
Tags: [[Zettelkasten/OS Camp Stage 2\|OS Camp Stage 2]]

# rCore ch1

本章比较简单
主要就是环境配置问题

## TuLoongOS
但是在loongarch64上实现还是要解决一些问题的
### bootloader
由于没有找到类似RustSBI这样的bootloader
于是去操作系统大赛官网查找了一下往年作品,发现有团队用的是uboot,于是我去下载了第三方做的[uboot-loongarch适配版本]((https://gitee.com/open-loongarch/u-boot/tree/master/board)),可惜上面只对实际的开发板做了适配,由于完全看不懂uboot,于是只能作罢
之后我去操作系统大赛的官网查找了一些资料,发现比赛方标注了一些适配多架构的OS,比如[starry-next](https://github.com/oscomp/starry-next),发现上面的qemu启动命令里并没有-bios参数,而是使用了-kernel,于是我效仿修改了TuLoongOS的实现，成功启动了TuLoongOS.
### 输出和关机
由于没有用RustSBI等bootloader,于是也就不能用sbi_rt等包,所以我只能自己实现控制台的输出和关机功能
输出功能通过询问ai得知可以用uart串口来实现,于是我新建了Uart.rs添加到项目中,并修改了Uart的地址(地址也由ai提供),成功输出字符到控制台
但是关机功能失败了,虽然设置成了ai给的地址,但是没有成功关机.
于是我去查找了starry-next的实现,通过它的[[Reference/loongarch64-qemu-virt.toml\|config]]文件,找到了loongarch64-qemu-virt平台的一系列接口地址,并修改了我的关机实现,成功关机!

___
# References