Covers...
[[#Simple Hello Module]]
[[#Component Definitions]]

---
## Simple Hello Module

Here I have created a simple module called hello. We create a module by writing `hello.c` and then compiling it to `hello.ko`. I have created a directory just to keep things separately.
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

## Component Definitions

### Headers

```c
#include <linux/module.h>
#include <linux/init.h>
```

`module.h` contains a great many definitions of symbols and functions needed by loadable modules. You need `init.h` to specify your initialization and cleanup functions (`hello_init()` and `hello_exit()` in the above example).

### Module Definitions

```c
MODULE_LICENSE("GPL");
MODULE_AUTHOR("hexavik");
MODULE_DESCRIPTION("Simple Hello Module");
```


> [!ERROR] If you comment or remove MODULE_LICENSE
> The module will not be built and `make` will throw an error
> ```bash
> make -C /lib/modules/6.12.62+rpt-rpi-2712/build M=/home/hexavik/hello_module modules
make[1]: Entering directory '/usr/src/linux-headers-6.12.62+rpt-rpi-2712'
  CC [M]  /home/hexavik/hello_module/hello.o
  MODPOST /home/hexavik/hello_module/Module.symvers
ERROR: modpost: missing MODULE_LICENSE() in /home/hexavik/hello_module/hello.o
make[3]: *** [/usr/src/linux-headers-6.12.62+rpt-common-rpi/scripts/Makefile.modpost:145: /home/hexavik/hello_module/Module.symvers] Error 1
make[2]: *** [/usr/src/linux-headers-6.12.62+rpt-common-rpi/Makefile:1922: modpost] Error 2
make[1]: *** [/usr/src/linux-headers-6.12.62+rpt-common-rpi/Makefile:236: __sub-make] Error 2
make[1]: Leaving directory '/usr/src/linux-headers-6.12.62+rpt-rpi-2712'
make: *** [Makefile:4: all] Error 2
> ```
> 
> In some cases module will still compile but when inserted, kernel will mark it as **tainted**.

Other descriptive definitions
`MODULE_AUTHOR("abc")` : stating who wrote the module
`MODULE_DESCRIPTION("desc")`: a human-readable statement of what the module does
`MODULE_VERSION()`: for a code revision number; see the comments in `<linux/module.h>` for the conventions to use in creating version strings
`MODULE_ALIAS()`: another name by which this module can be known
`MODULE_DEVICE_TABLE()`: to tell user space about which devices the module supports

### Initialization and Shutdown

Definition of the initialization function always looks like:

```c
static int __init initialization_function(void) {
	/* Initialization code here */
}

module_init(initialization_function);
```

- Initialization function should be `static`, since they are not meant to be visible outside of the specific file.
- There is a similar tag (`__initdata`) for data used only during initialization.
- The use of `module_init()` is mandatory. This macro adds a special section to the module's object code stating where the module's initialization function is to be found, else initialization function is never called.
- The return type `int` is essential to return the `0` for success, or negative number for an error

Definition of the cleanup function:

```c
static void __exit cleanup_function(void) {
	/* Cleanup code here */
}

module_exit(cleanup_function);
```

- The `module_exit()` declaration is necessary to enable to kernel to find your cleanup function.
- If your module does not define a cleanup function, the kernel does not allow it to be unloaded.
### `__init` and `__exit`

They are **macros for section placement**, not system calls. They are section annotations. `__init` memory is freed after initialization to reduce kernel memory footprint.
What they actually do:
- `__init` -> places function in `.init.text` section
- After initialization, memory is **freed**, saves kernel memory
- `__exit` -> placed in `.exit.text`
- Only used if module is unloadable
- If built-in, exit section may be discarded
### What happens when you insert a module

- `insmod` uses `init_module()` syscall
- Kernel loads ELF into memory
- Relocations resolved
- `module_init()` function pointer executed

### Makefile procedure

```c
obj-m
```

A makefile symbol used by the kernel build system to determine which modules should be built in the current directory.

```make
make -C /lib/modules/$(shell uname -r)/build M=$(PWD) modules
```

The command starts by changing its directory to the one provided with the -C option (that is, your kernel source directory). There it finds the kernel's top-level makefile.

The `M=` option causes that makefile to move back into your module source directory before trying to build the `modules` target. This target refers to the list of modules found in the `obj-m` variable, which we've set to `hello.o`  in our example.

Can also be written as:

```make
MAKE = make
KERNELDIR = /lib/modules/$(shell uname -r)/build

$(MAKE) -C $(KERNELDIR)$ M=$(PWD) modules
```
