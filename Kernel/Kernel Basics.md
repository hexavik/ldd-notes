[[Kernel Basics at glance]]

## Observing the Running System

### How do you check kernel version

```bash
uname -a
```

Output

```bash
Linux raspberrypi 6.12.62+rpt-rpi-2712 #1 SMP PREEMPT Debian 1:6.12.62-1+rpt1 (2025-12-18) aarch64 GNU/Linux
```

Then:

```bash
cat /proc/version
```

Output:

```bash
Linux version 6.12.62+rpt-rpi-2712 (serge@raspberrypi.com) (aarch64-linux-gnu-gcc-14 (Debian 14.2.0-19) 14.2.0, GNU ld (GNU Binutils for Debian) 2.44) #1 SMP PREEMPT Debian 1:6.12.62-1+rpt1 (2025-12-18)
```


## Kernel Directories

- [[proc]] Directory
