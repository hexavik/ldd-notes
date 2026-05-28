
A module runs in *kernel space*, whereas applications run in *user space*. Each mode can have its own memory mapping, its own address space, as well.

Unix transfers execution from user space to kernel space whenever an application issues a system call or is suspended by a hardware interrupt. Code that handles interrupts, is asynchronous with respect to processes and is not related to any particular process.

