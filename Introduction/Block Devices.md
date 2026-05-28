
Block devices are accessed by filesystem nodes in the */dev* directory. A block device is a device (e.g., a disk) that can host a filesystem. In most Unix systems, a block device can only handle I/O operations that transfer one or more whole blocks, which are usually 512 bytes (or a larger power of two) bytes in length. Linux, instead, allows the application to read and write a block device like a char device -- it permits the transfer of any number of bytes at a time. As a result, block and char devices differ only in the way data is managed internally by the kernel, and thus in the kernel/driver software interface. Like a char device, each block device is accessed through a filesystem node, and the difference between them is transparent to the user. Block drivers have a completely different interface to the kernel than char drivers.

> [!QUOTE] In short...
> Block devices, such as hard drives and USB storage, operate by reading and writing data in blocks. Block drivers enable random access to data, and they interface with the Linux filesystem and I/O subsystem.

> [!Example] Examples of Block Driver
> - Hard disk
> - SD card
> - eMMC

