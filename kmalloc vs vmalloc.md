## `kmalloc()`

- Allocates **physically contiguous** memory
- Fast
- Uses slab allocator
- Limited size (usually small to medium allocations)

Used when:
- DMA requires physical contiguity
- Hardware needs linear memory

> [!example]
> ```c
> void* ptr = kmalloc(1024, GFP_KERNEL);
> ```

Memory type:
- Directly mapped in kernel virtual address space

## `vmalloc()`

- Allocates **virtually contiguous**
- Physically non-contiguous
- Slower
- Uses page table mappings

Used when:
- Large allocation
- Physical contiguity not required

> [!example]
> ```c
> void* ptr = vmalloc(2 * 1024 * 1024);
> ```

Mental Model:

`kmalloc` -> one long continuous road
`vmalloc` -> multiple small roads connected via mapping

Hardware cares about physical roads. CPU cares about virtual mapping.