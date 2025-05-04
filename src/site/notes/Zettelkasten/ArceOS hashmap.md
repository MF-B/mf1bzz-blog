---
{"dg-publish":true,"permalink":"/zettelkasten/arce-os-hashmap/"}
---

2025-05-04
Status: #idea
Tags: [[Zettelkasten/OS Camp Stage 3\|OS Camp Stage 3]]

# ArceOS hashmap

本题是要为系统支持HashMap

我的一开始的思路是自己实现,但是我又感觉HashMap这种实现网上应该到处都是(绝对不是我懒得写),于是我去看了[Rust std]([rust/library/std at master · rust-lang/rust](https://github.com/rust-lang/rust/tree/master/library/std))的实现,顺便参考了它的封装方式.
其实就是把core::alloc::collections和自定义的HashMap写到一个collection.rs里,在lib.rs公开声明这个模块即可
rust std的HashMap是对[hashbrown]([rust-lang/hashbrown: Rust port of Google's SwissTable hash map](https://github.com/rust-lang/hashbrown))的抽象,不过其实hashbrown就已经能用了,所以不需要再抽象一次了
## 封装hashbrown
但是题目给了提示
那就用一下
主要是实现HashMap的new/insert/iter方法
同时自定义一个DefaultHasher和一个实现了BuildHasher接口的RandomState类型
其中RandomState就用到了axhal的随机函数了
```rust
pub fn new() -> RandomState {
        let k = random();
        let k0 = k as u64;
        let k1 = (k >> 64) as u64;
        RandomState { k0, k1 }
    }
```



___
# References