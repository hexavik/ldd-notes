When you allocate memory, kernel need to know:
"Am I allowed to sleep?"

## GFP_KERNEL

- Can sleep
- Can reclaim memory
- Used in process context

> [!example]
> ```c
> kmalloc(size, GFP_KERNEL);
> ```

## GFP_ATOMIC

- Cannot sleep
- Used in interrupt context
- Used when holding spinlock

> [!example]
> ```c
> kmalloc(size, GFP_ATOMIC);
> ```

If memory not available, then allocation fails immediately.