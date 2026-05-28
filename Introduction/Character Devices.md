A character (char) device is one that can be accessed as a stream of bytes (like a file); a char driver is in charge of implementing this behavior. Such a driver usually implements at least the *open*, *close*, *read*, and *write* system calls. The text console (*/dev/console*) and the serial ports (*/dev/ttyS0* and friends) are examples of char devices, as they are well represented by the stream abstraction.

Char devices are accessed by means of filesystem nodes, such as */dev/tty1* and */dev/lp0*. The only relevant difference between a char device and a regular file is that you can always move back and forth in the regular file, whereas most char devices are just data channels, which you can only access sequentially. There exist, nonetheless, char devices that look like data areas, and you can move back and forth in them; for instance, this usually applies to frame grabbers, where the applications can access the whole acquired image using *mmap* or *lseek*.

> [!QUOTE] In short ...
> Character devices, like serial ports, sensors, and keyboards, transfer data sequentially as a stream of bytes. The driver provides basic operations like `read()`, `write()`, and `ioctl()` to interact with the device.

> [!Example] Examples of Character Driver
> - GPIO
> - UART
> - Pseudo devices
> - `/dev/tty`

User interaction:

```c
open()
read()
write()
ioctl()
close()
```



