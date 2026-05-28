We have seen that how `insmod` resolves undefined symbols against the table of public kernel symbols. The table contains the addresses of global kernel items, functions, and variables, that are needed to implement modularized drivers. When a module is loaded, any symbol exported by the module becomes the part of the kernel symbol table.

Module stacking is useful in complex projects. If a new abstraction is implemented in the form of a device driver, it might offer a plug for hardware-specific implementations. e.g., the video-for-linux set of drivers is split into a generic module that exports symbols used by lower-level device drivers for specific hardware.

When using stacked modules, it is helpful to be aware of the `modprobe` utility.

If your module needs to export symbols for other modules to use, the following macros should be used.

```c
EXPORT_SYMBOL(name);
EXPORT_SYMBOL_GPL(name);
```
