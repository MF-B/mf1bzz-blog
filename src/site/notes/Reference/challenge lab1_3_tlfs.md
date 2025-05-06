---
{"dg-publish":true,"permalink":"/reference/challenge-lab1-3-tlfs/"}
---

2025-05-05
Status: #idea
Tags: [[Zettelkasten/挑战题目\|挑战题目]]

# challenge lab1_3_tlfs
```rust
//! Allocator algorithm in lab.

#![no_std]
#![allow(unused_variables)]

mod tlsf;
mod bump;
mod slab;
mod buddy;

use allocator::{BaseAllocator, ByteAllocator, AllocResult};
use core::ptr::NonNull;
use core::alloc::Layout;
use tlsf::TlsfByteAllocator;
use allocator::AllocError;
use bump::SimpleByteAllocator;
use slab::SlabByteAllocator;
use buddy::BuddyByteAllocator;
use log::*;

pub struct LabByteAllocator {
    next_tlsf: bool, // true: tlsf, false: other
    tlsf: TlsfByteAllocator,
    other: TlsfByteAllocator,
    add_memory_to_num: u8,
//    other: SimpleByteAllocator,
//    other: SlabByteAllocator,
//    other: BuddyByteAllocator,
    align8_tlsf: TlsfByteAllocator,
}

impl LabByteAllocator {
    pub const fn new() -> Self {
        Self {
            next_tlsf: true,
            tlsf: TlsfByteAllocator::new(),
            other: TlsfByteAllocator::new(),
            add_memory_to_num: 0,
//            other: SlabByteAllocator::new(),
//            other: BuddyByteAllocator::new(),
            align8_tlsf: TlsfByteAllocator::new(),
        }
    }
}

impl BaseAllocator for LabByteAllocator {
    fn init(&mut self, start: usize, size: usize) {
        self.tlsf.init(start, size);
    }
    fn add_memory(&mut self, start: usize, size: usize) -> AllocResult {
        match self.add_memory_to_num {
            0 => self.tlsf.add_memory(start, size),
            1 => self.other.add_memory(start, size),
            2 => self.align8_tlsf.add_memory(start, size),
            _ => Err(AllocError::InvalidParam),
            
        }
    }
}

impl ByteAllocator for LabByteAllocator {
    fn alloc(&mut self, layout: Layout) -> AllocResult<NonNull<u8>> {
        if !self.next_tlsf && layout.align() == 1 {
            self.add_memory_to_num = 1;
            let ptr = self.other.alloc(layout)?;
//            warn!("other alloc: {:?}", ptr);
            self.next_tlsf = !self.next_tlsf;
            if layout.size() >= 512 * 1024 {
                self.next_tlsf = true;
            }
            Ok(ptr)
        } else if layout.align() == 1 {
            self.add_memory_to_num = 0;
            let ptr = self.tlsf.alloc(layout)?;
//            warn!("tlsf alloc: {:?}", ptr);
            self.next_tlsf = !self.next_tlsf;
            if layout.size() >= 512 * 1024 {
                self.next_tlsf = true;
            }
            Ok(ptr)
        }
        else {
            self.add_memory_to_num = 2;
            let ptr = self.align8_tlsf.alloc(layout)?;
            warn!("align8 tlsf alloc: {:?}", ptr);
            Ok(ptr)
        }
    }
    fn dealloc(&mut self, pos: NonNull<u8>, layout: Layout) {
        if layout.align() == 8 {
            warn!("align8 tlsf dealloc: {:?}", pos);
            self.align8_tlsf.dealloc(pos, layout);
        }else{
            self.tlsf.dealloc(pos, layout);
        }
    }
    fn total_bytes(&self) -> usize {
        match self.add_memory_to_num {
            0 => self.tlsf.total_bytes(),
            1 => self.other.total_bytes(),
            2 => self.align8_tlsf.total_bytes(),
            _ => 0,
            
        }
    }
    fn used_bytes(&self) -> usize {
        todo!()
    }
    fn available_bytes(&self) -> usize {
        todo!()
    }
}
```
