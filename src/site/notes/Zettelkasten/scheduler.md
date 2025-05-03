---
{"dg-publish":true,"permalink":"/zettelkasten/scheduler/"}
---

2025-04-30
Status: #idea
Tags: [[Zettelkasten/OS Camp Stage 3\|OS Camp Stage 3]]

# scheduler
## ArceOS调度原理
### 时钟中断
设置时钟中断函数,每次CPU发出时钟中断时都执行此函数
### 时钟中断函数
除了更新timer外,此函数最主要的功能为判断当前任务是否可抢占
判断逻辑由调度算法和当前任务类型决定(当前任务为IDLE时则不进行抢占)
以RR调度算法为例,每次判断时都会对当前任务的时间片-1,当当前任务持有的时间片<=1时则设置当前任务可抢占
### 抢占逻辑
以上操作只是开抢占,但并没有实际抢占
当时钟中断发生时,系统执行的处理函数是: 
```rust
fn handler_irq(irq_num: usize) -> bool {
    let guard = kernel_guard::NoPreempt::new();
    dispatch_irq(irq_num);
    drop(guard); // rescheduling may occur when preemption is re-enabled.
    true
}
```
其中``dispatch_irq(irq_num);``负责执行在axruntime设置的函数,
而let guard和drop(guard)则会执行真正的抢占逻辑.
在axtask中我们可以找到对kernel_guard内部方法的实现
原理是创建时执行disable_preempt,而drop时执行enable_preempt.
在enable_preempt时即可实现抢占,具体实现可在axtask中寻找

___
# References
ArceOS ppt