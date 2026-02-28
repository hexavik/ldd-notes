Think of `/proc` as :
	A live window into kernel internal data structures.

```bash
ls /proc
```

Important ones:

```bash
/proc/cpuinfo
/proc/meminfo
/proc/modules
/proc/interrupts
/proc/kallsyms
```

Now run:

```
cat /proc/module
```

This shows currently loaded modules.
That means:
- Your kernel is modular.
- Not everything is built-in.

