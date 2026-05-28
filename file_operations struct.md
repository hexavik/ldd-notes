It is a structure that defines callbacks which connect user space system calls like read/write/open to driver-specific implementations.

```c
struct file_operations
```

This is the bridge.

> [!Example]
> ```c
> static const struct file_operations fops = {
> 	.owner = THIS_MODULE,
> 	.open = my_open,
> 	.read = my_read,
> 	.write = my_write,
> 	.release = my_close,
> };
> ```

When user calls

```c
read(fd, buf, size)
```

Kernel calls:

```c
my_read()
```

Through this structure. That's the glue.