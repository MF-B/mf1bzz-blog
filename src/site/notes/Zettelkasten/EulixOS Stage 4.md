---
{"dg-publish":true,"permalink":"/zettelkasten/eulix-os-stage-4/"}
---

2025-05-06
Status: #idea
Tags: [[Readme\|Readme]]

# EulixOS Stage 4
## 系统环境
## 环境准备

## [PyTorch构建](https://github.com/pytorch/pytorch#from-source)
1. python
	- version >=3.9
	- dnf install python3
2. 开发工具
	- dnf group install "Development Tools"
3. 虚拟环境
	- 使用pyvenv
	- dnf install -y python3-virtualenv
	- update-alternatives --install /usr/bin/python python /usr/bin/python3 10
	- python -m venv .venv && source .venv/bin/activate
4. 源码编译
```bash
git clone https://github.com/pytorch/pytorch
cd pytorch
# if you are updating an existing checkout
git submodule sync
git submodule update --init --recursive
```
## 构建流程
## 问题



___
# References