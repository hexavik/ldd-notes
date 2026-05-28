[[#What happens when a module is loaded]]
[[#What about Removal?]]
[[#Why can't a module call a function that is not exported?]]
[[#Module Reference Counter]]


LDD Reference:
`insmod` loads the module code and data into the kernel, which, in turn, performs a function similar to that of ld, in that it links any unresolved symbol in the module to the symbol table of the kernel. Unlike the linker, however, the kernel doesn't modify the module's disk file, but rather an in-memory copy. `insmod` accepts a number of command-line options (see the manpage for details), and it can assign values to parameters in your module before linking it to the current kernel.
The kernel support to `insmod` relies on a system call defined in kernel/module.c. The function sys_init_module allocates kernel memory to hold a module (this memory is allocated with `vmalloc`); it then copies the module text into that memory region, resolves kernel references in the module via the kernel symbol table, and calls the module's initialization function to get everything going.

The `modprobe` utility: like `insmod`, loads a module into the kernel. It differs in that it will look at the module to be loaded to see whether it references any symbols that are not currently defined in the kernel. If any such references are found, `modprobe` looks for other modules in the current module search path that define the relevant symbols. When `modprobe` finds those modules (which are needed by the module being loaded), it loads them into the kernel as well. If you use `insmod` in this situation instead, the command fails with an "unresolved symbols" message left in the system logfile.

The `lsmod` program produces a list of the modules currently loaded in the kernel. Some other information, such as any other modules making use of a specific module, is also provided. lsmod works by reading the `/proc/modules` virtual file. Information on currently loaded modules can also be found in the sysfs virtual filesystem under `/sys/module`.

Version Dependency // add later

> [!quote] Quick
> `insmod`, `rmmod`, `modprobe`
> User-space utilities that load modules into the running kernels and remove them.
## What happens when a module is loaded

### Step 0 - User Space Command

```bash
sudo insmod hello.ko
```

Important:
- `insmod` is just  a user-space program
- It does not load anything directly
- It makes a system call

### Step 1 - System Call

`insmod` calls `init_module()` function (or `finit_module()` in modern kernels)
This transfer control to *Kernel Space*.
This is **user -> Kernel boundary crossing**

### Step 2 - Kernel Receives the Module

Kernel function:

```bash
sys_init_module()
```

Now kernel:
1. Copies module binary from user space into kernel memory
2. Verifies ELF format
3. Allocates memory for module sections

### Step 3 - ELF Parsing

Generated `.ko` file is just an ELF object file, that includes following sections:

- `.text`
- `.data`
- `.bss`
- `.init.text`
- `.exit.text`
- `.symtab`
- `.strtab`

Kernel parses:
- Symbol table
- Relocation entries

### Step 4 - Symbol Resolution

A module might use `printk()`, but `printk()` is not inside your module.
So kernel must resolve, "Where is `printk` located?"

Kernel checks exported symbol list from `/proc/kallsyms` and matches it against `EXPORT_SYMBOL(printk);`

if symbol not found, *Module load fails*.

### Step 5 - Relocation

Addresses in module are relative.

Kernel:
 - Adjusts function pointers
 - Fixes jump addresses
 - Resolves global symbols
 
This is called **relocation fixing**.

### Step 6 - struct module Created

Kernel creates internal structure: `struct module`
This contains:
- name
- state
- memory regions
- list pointers
- reference count
- init function pointer

You can actually inspect module list via
```bash
cat /proc/modules
```

### Step 7 - Calling `module_init()`

Remember `module_init(hello_init);`
This macro registers a function pointer.

After loading + relocation, kernel calls `hello_init()`
This runs in **process context**.

if it returns:
- 0 -> success
- negative (e.g., `-ENODEV`) -> loading process unwinds and memory is freed

The init function returns an int so the kernel can determine whether initialization succeeded. If it returns a negative error code, the module load is aborted and resources are freed.

### Step 8 - Module Becomes Live

State changes to:

```c
MODULE_STATE_LIVE
```

Now:
- Functions callable
- Symbols available
- Device nodes usable (if driver)

## What about Removal?

Flow of `rmmod hello`
1. `delete_module()` syscall
2. Reference count checked
3. If zero -> call exit function
4. Free module memory
5. Remove from module list

What prevents `rmmod` from removing a module that is still in use?
The primary mechanism preventing unsafe removal is the **Module Reference Counter**. Every time kernel component (like a driver, a filesystem, or a system call) uses a module , the kernel increments that module's reference count.


> [!QUOTE] Analogy
> Imagine this process like this:
> ##### Kernel as a motherboard
> ##### Module = External board chip
> ##### insmod = Plugging chip into a motherboard
> 
> Motherboard checks:
> - Is chip valid?
> - Does chip require unknown signals?
> - Can I connect wires properly?
> - Does chip initialization/bringup succeed?
>   
>   Only then chip becomes active

> [!SUMMARY]
> What happens when a module is loaded
> 1. `insmod`  (or `modprobe`) opens the `.ko` file and triggers `init_module` (or `finit_module`) syscall.
> 2. Kernel copies module (ELF image) into kernel memory (form user space to kernel space).
> 3. The kernel verifies the ELF header, checks for a matching vermagic (kernel version string), and parses the section headers (like `.text`, `.data`, `.rodata`).
> 4. The kernel allocates "final" execution memory for the module sections (usually via `module_alloc`) and moves the code/data there.
> 5. The kernel searches the **Global Kernel Symbol Table** to find addresses for any `extern` symbols your module needs.
> 6. The kernel performs **Relocation Fixups**. It replaces the placeholder addresses in your code with the actual absolute memory addresses of the resolved symbols.
> 7. `struct module` is initialized, linking the module into the global `modules` list (this is what `lsmod` reads).
> 8. The kernel calls the function pointed to by your `module_init` macro.
> 9. If the function returns `0`, the module state is set to `MODULE_STATE_LIVE`.

### Why can't a module call a function that is not exported?

Short answer : **The Kernel Symbol Table (`kallsyms`)**

When a function is defined in the kernel but not marked with `EXPORT_SYMBOL()`, its memory address is not added to the public symbol table. Since the module loader acts as a dynamic linker at runtime, it simply won't have the `address` needed to resolve that function call, leading to an **'Unknown Symbol'** error during `insmod`.
### What happens if relocation fails

Relocation failure is a **fatal error** during the module loading process. If the kernel's module loader cannot resolve a symbol or apply a relocation offset, it immediately **halts the loading sequence**, triggers a cleanup of any allocated memory for that module, and returns an error to user-space (typically `Unknown symbol` or `Invalid module format`).

## Module Reference Counter

Each module has:

```c
atomic_t refcnt;
```

If `refcnt > 0`, Kernel refuses removal

```bash
rmmod: ERROR: Mdoule in use
```

Reference count increases when:
- Another module depends on it
- A device file is open
- It is actively in use (e.g., net driver handling traffic)