## Check Loaded Modules

```bash
lsmod
```

This internally read `/proc/modules`.

### Check a module info

```bash
modinfo <module_name>
```

> [!EXAMPLE]
> ```bash
> modinfo i2c_dev
> ```
> Output:
> ```bash
> filename:       /lib/modules/6.12.62+rpt-rpi-2712/kernel/drivers/i2c/i2c
> dev.ko.xz
> license:        GPL
> description:    I2C /dev entries driver
> author:         Simon G. Vogl <simon@tk.uni-linz.ac.at>
> author:         Frodo Looijaard <frodol@dds.nl>
> srcversion:     00C0C4E4A16B02B72E80647
> depends:        
> intree:         Y
> name:           i2c_dev
> vermagic:       6.12.62+rpt-rpi-2712 SMP preempt mod_unload modversions aarch64
> ```

### Where are modules stored?

```bash
ls -l /lib/modules/$(uname -r)/
```

Output:

```bash
total 2972
lrwxrwxrwx  1 root root     47 Dec 18 22:47 build -> ../../../src/linux-headers-6.12.62+rpt-rpi-2712
drwxr-xr-x 10 root root   4096 Feb 27 22:53 kernel
-rw-r--r--  1 root root 701391 Feb 27 22:54 modules.alias
-rw-r--r--  1 root root 722919 Feb 27 22:54 modules.alias.bin
-rw-r--r--  1 root root  16572 Dec 18 22:47 modules.builtin
-rw-r--r--  1 root root   8219 Feb 27 22:54 modules.builtin.alias.bin
-rw-r--r--  1 root root  18497 Feb 27 22:54 modules.builtin.bin
-rw-r--r--  1 root root 110109 Dec 18 22:47 modules.builtin.modinfo
-rw-r--r--  1 root root 251414 Feb 27 22:54 modules.dep
-rw-r--r--  1 root root 336295 Feb 27 22:54 modules.dep.bin
-rw-r--r--  1 root root    384 Feb 27 22:54 modules.devname
-rw-r--r--  1 root root  75323 Dec 18 22:47 modules.order
-rw-r--r--  1 root root   1166 Feb 27 22:54 modules.softdep
-rw-r--r--  1 root root 341325 Feb 27 22:54 modules.symbols
-rw-r--r--  1 root root 413706 Feb 27 22:54 modules.symbols.bin
-rw-r--r--  1 root root     55 Feb 27 22:54 modules.weakdep
lrwxrwxrwx  1 root root     49 Dec 18 22:47 source -> ../../../src/linux-headers-6.12.62+rpt-common-rpi
```

These are used by `modprobe`.

## What happens when you insert a module?

#### Create a minimal module

Create `hello.c`

```c
#include <linux/module.h>
#include <linux/init.h>

static int __init hello_init(void) {
	printk(KERN_INFO "Hello from Kernel!\n");
	return 0;
}

static void __exit hello_exit(void) {
	printk(KERN_INFO "Goodbye from Kernel!\n");
}

module_init(hello_init);
module_exit(hello_exit);

MODULE_LICENSE("GPL");
MODULE_AUTHOR("hexavik");
MODULE_DESCRIPTION("Simple Hello Module");
```
#### Create Makefile

```make
obj-m += hello.o

all:
	make -C /lib/modules/$(shell uname -r)/build M=$(PWD) modules

clean:
	make -C /lib/modules/$(shell uname -r)/build M=$(PWD) clean
```
#### Build

```bash
make
```

Output:

```bash
make -C /lib/modules/6.12.62+rpt-rpi-2712/build M=/home/hexavik/hello_module modules
make[1]: Entering directory '/usr/src/linux-headers-6.12.62+rpt-rpi-2712'
  CC [M]  /home/hexavik/hello_module/hello.o
  MODPOST /home/hexavik/hello_module/Module.symvers
  CC [M]  /home/hexavik/hello_module/hello.mod.o
  CC [M]  /home/hexavik/hello_module/.module-common.o
  LD [M]  /home/hexavik/hello_module/hello.ko
make[1]: Leaving directory '/usr/src/linux-headers-6.12.62+rpt-rpi-2712'
```

You'll get

```bash
hello.ko
```

#### Insert

```bash
sudo insmod hello.ko
```

Check logs:

```bash
dmesg | tail
```

You should see:

```bash
Hello from Kernel!
```

#### Remove

```bash
sudo rmmod hello
```

Check:

```
dmesg | tail
```

You'll see:

```bash
Goodbye from Kernel!
```

### Directory Structure after module created

![[hello_module_directory_structure.png]]
