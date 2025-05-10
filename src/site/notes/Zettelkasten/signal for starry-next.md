---
{"dg-publish":true,"permalink":"/zettelkasten/signal-for-starry-next/"}
---

2025-05-09
Status: #idea
Tags: [[Zettelkasten/ArceOS宏内核\|ArceOS宏内核]]

# signal for starry-next
## 进程信号和线程信号

详见Linux-UNIX系统编程手册P591(书内为P562)
## 实现
### Process新增字段

- sig_pending - 共享未决信号队列
- sig_actions - 信号处理函数(进程全局)
> `进程全局`指该操作对该进程的所有线程都有效,信号的处理效果是全局有效的
### Thread新增字段

- sig_pending - 信号队列
- sig_mask - 线程私有掩码
- sig_stack - 信号栈
### 系统调用支持
#### kill
```rust
pub fn sys_kill(
    pid: i32, // pid=0时,发送给当前进程组;pid=-1时,为广播信号;pid< -1时,发送给组id为|pid|的                  进程组
    sig: i32,
) -> LinuxResult<isize>;
/* 
返回值: 失败时: ESRCH(pid匹配失败)
*/
```
#### sys_rt_sigprocmask
```rust
pub fn sys_rt_sigprocmask(
    how: i32,
    set: UserConstPtr<SignalSet>,
    oldset: UserPtr<SignalSet>,
    _sigsetsize: usize,
) -> LinuxResult<isize>;
```
实现思路:
	根据how参数设置当前线程的掩码即可
#### sys_rt_sigaction
```rust
pub fn sys_rt_sigaction(
    signum: i32,
    act: UserConstPtr<SignalAction>,
    oldact: UserPtr<kernel_sigaction>,
    _sigsetsize: usize,
) -> LinuxResult<isize>;
```
### 信号处理
模仿PAGE_FAULT注册成starry定义的函数
#### 处理进程
#### 处理线程pending
- 检查是否有pending信号: sig_pending.dequeue()
- 对每一个pending的信号,进行相应处理,通过进程的sig_actions,获得对应的disposition
	- 忽略: 跳过此次循环
	- 默认: 使用信号默认处理
	- Handler: 使用用户设置的处理程序

___
# References
[深入理解Linux信号机制(1.0) - 知乎](https://zhuanlan.zhihu.com/p/537431439#:~:text=%E4%BF%A1%E5%8F%B7%E6%9C%BA%E5%88%B6%E6%98%AFUNIX%E7%B3%BB%E7%BB%9F%E6%9C%80%E5%8F%A4%E8%80%81%E7%9A%84%E6%9C%BA%E5%88%B6%E4%B9%8B%E4%B8%80%EF%BC%8C%E5%AE%83%E4%B8%8D%E4%BB%85%E6%98%AF%E5%86%85%E6%A0%B8%E5%A4%84%E7%90%86%E7%A8%8B%E5%BA%8F%E5%9C%A8%E8%BF%90%E8%A1%8C%E6%97%B6%E5%8F%91%E7%94%9F%E9%94%99%E8%AF%AF%E7%9A%84%E6%96%B9%E5%BC%8F%EF%BC%8C%E8%BF%98%E6%98%AF%E7%BB%88%E7%AB%AF%E7%AE%A1%E7%90%86%E8%BF%9B%E7%A8%8B%E7%9A%84%E6%96%B9%E5%BC%8F%EF%BC%8C%E5%B9%B6%E4%B8%94%E8%BF%98%E6%98%AF%E4%B8%80%E7%A7%8D%E8%BF%9B%E7%A8%8B%E9%97%B4%E9%80%9A%E4%BF%A1%E6%9C%BA%E5%88%B6%E3%80%82%20%E4%BF%A1%E5%8F%B7%E6%9C%BA%E5%88%B6%E7%94%B1%E4%B8%89%E9%83%A8%E5%88%86%E6%9E%84%E6%88%90%EF%BC%8C%E9%A6%96%E5%85%88%E6%98%AF%E4%BF%A1%E5%8F%B7%E6%98%AF%E6%80%8E%E4%B9%88%E4%BA%A7%E7%94%9F%E7%9A%84%EF%BC%8C%E6%88%96%E8%80%85%E8%AF%B4%E6%98%AF%E8%B0%81%E5%8F%91%E9%80%81%E7%9A%84%EF%BC%8C%E7%84%B6%E5%90%8E%E6%98%AF%E4%BF%A1%E5%8F%B7%E6%98%AF%E6%80%8E%E4%B9%88%E6%8A%95%E9%80%92%E5%88%B0%E8%BF%9B%E7%A8%8B%E6%88%96%E8%80%85%E7%BA%BF%E7%A8%8B%E7%9A%84%EF%BC%8C%E6%9C%80%E5%90%8E%E6%98%AF%E4%BF%A1%E5%8F%B7%E6%98%AF%E6%80%8E%E4%B9%88%E5%A4%84%E7%90%86%E7%9A%84%E3%80%82,%E4%B8%8B%E9%9D%A2%E6%88%91%E4%BB%AC%E5%85%88%E7%9C%8B%E4%B8%80%E5%BC%A0%E5%9B%BE%EF%BC%9A%20%E4%BB%8E%E5%9B%BE%E4%B8%AD%E6%88%91%E4%BB%AC%E5%8F%AF%E4%BB%A5%E7%9C%8B%E5%88%B0%E4%BF%A1%E5%8F%B7%E7%9A%84%E4%BA%A7%E7%94%9F%E6%96%B9%E5%BC%8F%E4%B9%9F%E5%B0%B1%E6%98%AF%E5%8F%91%E9%80%81%E6%96%B9%E6%9C%89%E4%B8%89%E7%A7%8D%E3%80%82)
[sigprocmask(2) - Linux manual page](https://www.man7.org/linux/man-pages/man2/sigprocmask.2.html)
[深入理解Linux内核信号处理机制原理(含源码讲解) - 知乎](https://zhuanlan.zhihu.com/p/463100471)
[kill(2) - Linux manual page](https://www.man7.org/linux/man-pages/man2/kill.2.html)
Linux-UNIX系统编程手册P362(书内为P333)-kill