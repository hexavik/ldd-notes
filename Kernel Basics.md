## Monolithic vs Microkernel

### Monolithic Kernel (Linux)
In a monolithic kernel:
- All core services run in **kernel space**
- Includes:
	- Device drivers
	- File systems
	- Networking
	- Memory management
	- Scheduler

Everything runs in privileged mode.

#### Structure (simplified)

```
       +------------------+
       |   User Space     |
       +------------------+
            ↓ syscall
+----------------------------------+
| Kernel (drivers + fs + net + mm) |
+----------------------------------+
```

#### Advantages
- Fast (no IPC overhead)
- Direct function calls between subsystems
- High performance
#### Disadvantages
- Bug in driver -> entire system crash
- Large trusted code base
Linux = Monolithic (but modular, because modules can load dynamically)

### Microkernel

In microkernel:
- Only minimal core in kernel:
	- Scheduler
	- IPC
	- Basic memory management
- Drivers and services run in **user space**

```
+-----------------------+
| Drivers (user space)  |
| FS (user space)       |
+-----------------------+
         ↓ IPC
+-----------------------+
| Microkernel core      |
+-----------------------+
```

#### Advantages
- More stable
- Driver crash doesn't kill system
- Better isolation
#### Disadvantages
- Slower (IPC overhead)
- More complex communication
Examples:
- MINIX
- QNX
- Mach

> [!QUOTE] Interview Line
> Linux is a monolithic kernel because drivers and subsystems run inside kernel space, but it supports modular loading via loadable kernel modules.

---

## User Space vs Kernel Space

Modern OS divides memory into two protection domains.
### User Space

- Applications run here
- Cannot access hardware directly
- Cannot access kernel memory
- Limited privileges

If user tries:

```c
*(int *)0xFFFFFFFF = 10;
```

-> Segmentation fault.

### Kernel Space

- Full access to hardware
- Full memory access
- Runs in privileged mode (ring 0 in x86)

### Why Separation Exists?

Security + Stability

User cannot:
- Corrupt kernel memory
- Access device registers directly

---
## Process Context vs Interrupt Context

This is VERY important in driver interview