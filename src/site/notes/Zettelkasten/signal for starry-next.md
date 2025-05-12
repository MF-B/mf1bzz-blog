---
{"dg-publish":true,"permalink":"/zettelkasten/signal-for-starry-next/"}
---

2025-05-09
Status: #idea
Tags: [[Zettelkasten/ArceOS宏内核\|ArceOS宏内核]]
> 基于axsignal
# signal for starry-next
## 进程信号和线程信号

详见Linux-UNIX系统编程手册P591(书内为P562)
## 实现
### Process新增字段
```rust
sig_manager: Arc<ProcessSignalManager<RawMutex, MyWaitQueue>>
```
### Thread新增字段

```rust
sig_manager: Arc<ThreadSignalManager<RawMutex, MyWaitQueue>>
```
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
    act: UserConstPtr<kernel_sigaction>,
    oldact: UserPtr<kernel_sigaction>,
    _sigsetsize: usize,
) -> LinuxResult<isize>;
```
#### waitpid系统调用改进
将crate::sys_sched_yield()更换为sig_manager.wait_signal()
意思是父进程等待子进程的信号
#### do_exit改进
进程退出前向父进程发送SIGCHLD信号


### 信号处理
模仿PAGE_FAULT注册成starry定义的函数
#### 处理进程
> 处理线程信号时会自动处理进程信号,所以不需要做额外处理
##### WaitQueue接口实现
沿用axtask的WaitQueue即可(需要再封装一层)
#### 处理线程
- 调用thread_sig_manager的check_signals方法即可,它返回一个SignalOSAction,我们需要根据返回值做相应处理(即exit,并修改exit_code)
- 对于用户自定义的Handler,check_signal时会自动将信号栈压好,handle_signal结束返回用户程序后会自动跳转到handler,但我们仍然需要处理返回的情况.
#### sig_return处理
进程的sig_manager保存有一个默认的信号返回地址,并且管理器中的actions中,每一个action也都保存有信号返回地址.
简单起见,先忽略action中的返回地址
自定义信号handler执行完毕后,会尝试跳转到ra寄存器的地址,而ra寄存器在check_signal时被设置为进程默认的信号返回地址
##### trampoline页的实现
参考别人的实现
- 在axhal的各arch添加signal.S汇编,执行系统调用(对齐到页开头,通过修改linker实现)
- 实现map_trampoline,将用户的signal-trampoline(0x4001_0000)映射到signal.S汇编函数的实现上.
- 修改axmm::aspace,添加以下代码: (目的是为了跳过trampoline页)
```rust
if matches!(backend, Backend::Linear { .. }) {
    continue;
}
```


___
# References
[深入理解Linux信号机制(1.0) - 知乎](https://zhuanlan.zhihu.com/p/537431439#:~:text=%E4%BF%A1%E5%8F%B7%E6%9C%BA%E5%88%B6%E6%98%AFUNIX%E7%B3%BB%E7%BB%9F%E6%9C%80%E5%8F%A4%E8%80%81%E7%9A%84%E6%9C%BA%E5%88%B6%E4%B9%8B%E4%B8%80%EF%BC%8C%E5%AE%83%E4%B8%8D%E4%BB%85%E6%98%AF%E5%86%85%E6%A0%B8%E5%A4%84%E7%90%86%E7%A8%8B%E5%BA%8F%E5%9C%A8%E8%BF%90%E8%A1%8C%E6%97%B6%E5%8F%91%E7%94%9F%E9%94%99%E8%AF%AF%E7%9A%84%E6%96%B9%E5%BC%8F%EF%BC%8C%E8%BF%98%E6%98%AF%E7%BB%88%E7%AB%AF%E7%AE%A1%E7%90%86%E8%BF%9B%E7%A8%8B%E7%9A%84%E6%96%B9%E5%BC%8F%EF%BC%8C%E5%B9%B6%E4%B8%94%E8%BF%98%E6%98%AF%E4%B8%80%E7%A7%8D%E8%BF%9B%E7%A8%8B%E9%97%B4%E9%80%9A%E4%BF%A1%E6%9C%BA%E5%88%B6%E3%80%82%20%E4%BF%A1%E5%8F%B7%E6%9C%BA%E5%88%B6%E7%94%B1%E4%B8%89%E9%83%A8%E5%88%86%E6%9E%84%E6%88%90%EF%BC%8C%E9%A6%96%E5%85%88%E6%98%AF%E4%BF%A1%E5%8F%B7%E6%98%AF%E6%80%8E%E4%B9%88%E4%BA%A7%E7%94%9F%E7%9A%84%EF%BC%8C%E6%88%96%E8%80%85%E8%AF%B4%E6%98%AF%E8%B0%81%E5%8F%91%E9%80%81%E7%9A%84%EF%BC%8C%E7%84%B6%E5%90%8E%E6%98%AF%E4%BF%A1%E5%8F%B7%E6%98%AF%E6%80%8E%E4%B9%88%E6%8A%95%E9%80%92%E5%88%B0%E8%BF%9B%E7%A8%8B%E6%88%96%E8%80%85%E7%BA%BF%E7%A8%8B%E7%9A%84%EF%BC%8C%E6%9C%80%E5%90%8E%E6%98%AF%E4%BF%A1%E5%8F%B7%E6%98%AF%E6%80%8E%E4%B9%88%E5%A4%84%E7%90%86%E7%9A%84%E3%80%82,%E4%B8%8B%E9%9D%A2%E6%88%91%E4%BB%AC%E5%85%88%E7%9C%8B%E4%B8%80%E5%BC%A0%E5%9B%BE%EF%BC%9A%20%E4%BB%8E%E5%9B%BE%E4%B8%AD%E6%88%91%E4%BB%AC%E5%8F%AF%E4%BB%A5%E7%9C%8B%E5%88%B0%E4%BF%A1%E5%8F%B7%E7%9A%84%E4%BA%A7%E7%94%9F%E6%96%B9%E5%BC%8F%E4%B9%9F%E5%B0%B1%E6%98%AF%E5%8F%91%E9%80%81%E6%96%B9%E6%9C%89%E4%B8%89%E7%A7%8D%E3%80%82)
[sigprocmask(2) - Linux manual page](https://www.man7.org/linux/man-pages/man2/sigprocmask.2.html)
[深入理解Linux内核信号处理机制原理(含源码讲解) - 知乎](https://zhuanlan.zhihu.com/p/463100471)
[kill(2) - Linux manual page](https://www.man7.org/linux/man-pages/man2/kill.2.html)
Linux-UNIX系统编程手册P362(书内为P333)-kill