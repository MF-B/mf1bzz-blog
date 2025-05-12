---
{"dg-publish":true,"permalink":"/zettelkasten/save-fp-state-for-loongarch/"}
---

2025-05-12
Status: #idea
Tags: [[Zettelkasten/ArceOS宏内核\|ArceOS宏内核]]

# Save FP_STATE for loongarch
## 浮点寄存器
共32个,记为f0~f31,通常情况下FR位宽为64比特
`pub fp: [u64; 32]`

## 条件标志寄存器
CFR共8个,记为fcc0~fcc7,位宽为1比特
`pub fcc: usize

## 浮点控制状态寄存器
FCSR共4个,记为fcsr0~fcsr,位宽为32比特;但是实际只保存FCSR0即可
`pub fcsr: usize`


___
# References
