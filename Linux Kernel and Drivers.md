
[[#Final Index]]
[[#LDD Book Chapters]]
[[#Stages]]
[[#Linux through Drivers Path]]

---
Pages and links are not categorized properly, which shall be done later.

[[Raspberry Pi 5]]

[[Kernel Basics]]
[[Kernel Modules]]

[[Components]]

[[Learning Path]]

### Miscellaneous
- [[printk]]
- [[User Space and Kernel Space]]

### Memory and Concurrency
- [[kmalloc vs vmalloc]]
- [[GFP Flags]]


---

## Final Index

1. [[Kernel Fundamentals]]
	- Monolithic vs microkernel vs hybrid (where Linux actually sits)
	- Kernel versions and stable / LTS trees -- how patches flow
	- User space vs kernel space -- address space separation, privilege levels (ring 0/3)
	- System call interface -- syscall table, entry path (`SYSCALL_DEFINE`), return to user
	- Process context vs interrupt context -- what's legal in each
	- What happens when a module is loaded -- `module_init`, `__init`, ELD loading, symbol resolution
	- `THIS_MODULE`, reference counting, `try_module_get` / `module_put`
	- Kernel compilation -- `Kconfig`, `Kbuild`, `menuconfig`, `defconfig`
2. [[Kernel Data Structures]]
	- Linked lists -- `list_head`, `list_entry`, `container_of`
	- Red-black trees -- `rbtree` usage in the kernel (VM, CFS scheduler)
	- Hash tables -- `hlist`, `DEFINE_HASHTABLE`
	- `kfifo` -- lock-free ring buffer for driver I/O
	- `IDR` -- integer ID allocator (used in DRM for object IDs)
	- `xarray` -- modern replacement for radix tree
3. [[Device Driver Model]]
	- `struct bus_type`, `struct device`, `struct device_driver` -- the driver model triad
	- Platform bus -- `platform_device`, `platform_driver`, `of_match_table`
	- PCI subsystem -- enumeration, BAR mapping, MSI/MSI-X (relevant for GPU PCIe)
	- USB subsystem -- URBs, endpoints (background context)
	- `probe()` and `remove()` -- lifecycle, deferred probe (`-EPROBE_DEFER`)
	- `sysfs` -- exposing driver attributes to user-space
	- `udev` / `uevent` -- device enumeration in user-space
4. [[Character and Misc Drivers]]
	- `file_operations` structure -- `open`, `release`, `read`, `write`, `ioctl`, `mmap`, `poll`
	- Major and minor numbers -- `KMDEV`, `alloc_chrdev_region`, `cdev_init`
	- `copy_to_user` / `copy_from_user` -- why these exist, page fault handling on kernel side
	- `ioctl` -- `_IO`, `_IOR`, `_IOW`, `_IOWR` macros, unlocked vs compat
	- `mmap` -- `vm_operations_struct`, `remap_pfn_range`, `dma_mmap_coherent`
	- `poll`/`select` support -- `wait_queue_head_t`, `poll_wait`
	- Misc device framework -- `misc_register` (lighter than full cdev)
5. [[Memory Management]]
	- Physical vs virtual address -- `phys_to_virt`, `virt_to_phys`, `__pa`, `__va`
	- `kmalloc` vs `vmalloc` -- contiguous physical vs contiguous virtual, size thresholds
	- `GFP_KERNEL` vs `GFP_ATOMIC` vs `GFP_DMA` -- when each is valid
	- Slab allocator -- `kmem_cache_create`, `SLAB` vs `SLUB`
	- Per-CPU memory -- `alloc_percpu`, `get_cpu_var`
	- Memory-mapped I/O -- `ioremap`, `ioremap_wc` (write-combining -- critical for framebuffers), `iounmap`
	- `readl`/`writel`, `readw`/`writew` -- MMIO accessors, memory barriers
	- DMA -- `dma_alloc_coherent`, `dma_map_single`, scatter-gather (`sg_table`), IOMMU
	- CMA(Contiguous Memory Allocator) -- relevance to GPU/display buffers
	- Page tables -- `pgd`, `p4d`, `pud`, `pmd`, `pte` walk (conceptual)
6. [[Concurrency and Synchronization]]
	- Spinlock -- `spin_lock`, `spin_lock_irqsave` -- when to use each variant
	- Mutex -- `mutex_lock`, `mutex_trylock` -- sleepable, not usable in IRQ context
	- RW semaphore -- `down_read`/`down_write`, reader/write starvation
	- `atomic_t`/`atomic64_t` -- lock-free counters
	- `RCU` (Read-Copy-Update) -- read-side critical sections, `rcu_read_lock`, use in hot baths
	- `seqlock` -- for frequently read, rarely written data (e.g., timestamp)
	- Why you cannot sleep in interrupt context -- stack, scheduling, `in_interrupt()`
	- Memory barriers -- `smp_mb`, `smp_rmb`, `smp_wmb`, `barrier()` -- when MMIO ordering matters
	- `commpletion` -- signaling between threads/IRQs
7. [[Interrupt Handling]]
	- IRQ  descriptor -- `irq_desc`, GIC/APIC, interrupt controller hierarchy
	- `request_irq`/`free_irq`/`devm_request_irq` -- flags (`IRQF_SHARED`, `IRQF_TRIGGER_*`)
	- Top half -- hardirq context, what's forbidden (sleep, `kmalloc GFP_KERNEL`)
	- Bottom half mechanisms:
		- `tasklet` -- soft-IRQ based, non-preemptible, deprecated path
		- `workqueue` -- kernel threads, sleepable, `schedule_work`, `INIT_WORK`
		- Threaded IRQs -- `IRQF_ONESHOT`, handler runs in kernel thread
	- Softirqs -- `NET_RX_SOFTIRQ`, `BLOCK_SOFTIRQ` etc. -- fixed set, not for drivers
	- `disable_irq`/`enable_irq`, `local_irq_save`/`restore`
	- Shared interrupts -- how the kernel walks the chain, your handler returning `IRQ_HANDLED` vs `IRQ_NONE`
	- MSI/MSI-X -- message-signalled interrupts, PCIe, relevance to GPU
8. [[DRM KMS Subsystem]]
	- DRM Architecture overview -- libdrm, `drm_driver`, render vs display split
	- KMS objects -- `drm_crtc`, `drm_encoder`, `drm_connector`, `drm_plane`, `drm_bridge`
	- Atomic modesetting -- `drm_atomic_state`, `drm_atomic_commit`, `begin`/`check`/`commit` phases
	- GEM (Graphics Execution Manager) -- `drm_gem_object`, `drm_gem_cma_object`, buffer lifecycle
	- PRIME / DMA-buf -- cross-device buffer sharing, `dma_buf_export`, `dma_buf_attach`
	- Framebuffer -- `drm_framebuffer`, `drm_fb_helper`, legacy vs atomic path
	- `drm_panel` -- panel abstraction, `drm_panel_funcs`, MIPI DSI panels
	- Vblank handling -- `drm_crtc_handle_vblank`, pageflip completion events
	- HDMI specifics -- `drm_display_info`, `drm_hdmi_avi_infoframe`, EDID parsing (`drm_do_get_edid`)
	- HPD (Hot Plug Detect) -- IRQ-driven, connector status polling vs HPD
	- Audio on HDMI -- `drm_audio_component`, HDPC (conceptual)
	- `drm_beidge` chain -- DP/HDMI encoder bridges, `devm_drm_of_get_bridge`
9. [[Display Hardware and Clocks]]
	- Clock framework -- `clk_get`, `clk_prepare_enable`, `clk_set_rate`, `clk_round_rate`
	- Pixel clock -- HDMI timing, `drm_display_mode`, `htotal`/`vtotal`, porches, sync polarity
	- PLL configuration -- how pixel clocks are derived from a reference (practical for HDMI at different resolutions)
	- Regulator framework -- `regulator_get`, `regulator_enable` -- display power sequencing
	- Pinctrl -- `pinctrl_lookup_state`, mux for HDMI/display signals
	- Reset controller -- `reset_control_assert/deassert` -- display subsystem bring-up
10. [[Debugging and Tracing]]
	- `printk`/`pr_*`/`dev_*` -- log levels, rate limiting (`pr_err_ratelimited`)
	- `dmesg` -- ring buffer, `dmesg -T`, log level filtering
	- Dynamic debug -- `dyndbg`, `pr_debug`, enabling per-module/per-file at runtime
	- `debugfs` -- exposing internal driver state (DRM uses this heavily -- `drm_debugfs_add_file`)
	- Kernel oops -- decoding the backtrace, `decode_stacktrace.sh`, `addr2line`
	- Kernel panic -- `panic_on_oops`, kdump, vmcore (relevant for production systems)
	- `BUG()`, `WARN()`, `BUG_ON()`, `WARN_ON()` -- appropriate usage
	- `kasan` -- kernel address sanitizer, catching UAF and OOB in drivers
	- `lockdep` -- deadlock and lock-order validation, reading lockdep splats
	- `ftrace` -- function tracer, `trace_printk`, event tracing (`trace_*` hooks in DRM)
	- `perf` -- PMU counters, latency profiling for display pipeline bottlenecks
11. [[Power Management]]
	- Runtime PM -- `pm_runtime_enable`, `pm_runtime_get_sync`, `pm_runtime_put` -- essential for display/HDMI
	- System sleep -- `suspend`/`resume` callbacks in `dev_pm_ops`
	- `CONFIG_PM_DEBUG` -- tracing PM state transitions
	- HDMI display off sequence -- mute audio, disable output, power down PHY, assert resets
12. [[Build, Integration and Upstreaming]]
	- `Kconfig` -- `tristate`, `depends on`, `select`
	- `Makefile` integration -- `obj-$(CONFIG_FOO) += foo.o`
	- Device tree bindings -- YAML schema, `dt-bindings/`, `make dtbs_check`
	- Submitting patches -- `scripts/checkpatch.pl`, commit message format, `get_maintainer.pl`
	- Kernel coding style -- `Documentation/process/coding-style.rst`
	- `coccinelle` -- semantic patch tool for automated refactoring

## LDD Book Chapters

Ch 1 [[Introduction to Drivers]]
Ch 2 Building and Running Modules
Ch 3 Char Drivers
Ch 4 Debugging Techniques
Ch 5 Concurrency and Race Conditions
Ch 6 Advanced Char Driver Operations
Ch 7 Time, Delays and Deferred Work
Ch 8 Allocating Memory
Ch 9 Communicating with Hardware
Ch 10 Interrupt Handling
Ch 11 Data Types in the Kernel
Ch 12 PCI Drivers
Ch 13 USB Drivers
Ch 14 The Linux Device Model
Ch 15 Memory Mapping and DMA
Ch 16 Block Drivers
Ch 17 Network Drivers
Ch 18 TTY Drivers

---

Interview Preparation (12 Hour)

- [[Kernel Basics]]
	- Monolithic vs microkernel
	- Process vs interrupt context
	- User Space vs kernel space
	- System call flow
	- What happens when a module is loaded
- Device Driver Fundamentals
	- Types of Drivers
		- Character driver
		- Block driver
		- Network driver
		- Platform vs PCI vs USB drivers
	- file_operations Structure
	- copy_to_user vs copy_from_user
	- Major and Minor Numbers
- Memory and Concurrency
	- kmalloc vs vmalloc
	- GFP_KERNEL vs GFP_ATOMIC
	- Spinlock vs mutex
	- Why you cannot sleep in interrupt context
	- What is bottom half (tasklet/workqueue)
- Interrupts & Hardware Interaction
	- Interrupt flow
	- request_irq()
	- free_irq()
	- Shared interrupts
	- Top half vs bottom half
- Debugging
	- How to debug kernel panic?
	- What is printk?
	- What is dmesg?
	- How to enable dynamic debug?
	- How to analyze oops trace?

---
## Stages

#### Stage 0: Why interrupts exist
Why polling breaks at scale in embedded + OS systems.
#### Stage 1: CPU perspective
What literally happens electrically + architecturally when interrupt line asserts.
#### Stage 2: Bare metal refresher
IRQ vector table, ISR, context save/restore
#### Stage 3: Why Linux complicates things
Because now:
- multitasking exists
- scheduling exists
- sleeping exists
- concurrency exists
- SMP exists
#### Stage 4: Hard IRQ vs soft IRQ vs threaded IRQ
Why Linux split responsibilities?
#### Stage 5: Interrupt context
what is forbidden and why:
- no sleeping
- no blocking mutex
- allocation restrictions
- GFP flags
#### Stage 6: Real driver integration
GPIO interrupt driver on Raspberry Pi.
#### Stage 7: Kernel source reading
Walk through generic IRQ subsystem.
#### Stage 8: Performance and debugging
Latency, IRQ storm, `/proc/interrupts`, ftrace.

Memory zone:
Physical memory -> virtual memory -> MMU -> page tables -> kernel mapping -> zones -> buddy allocator -> slab -> GFP flags -> DMA -> vmalloc -> page cache -> NUMA

---
## Linux through Drivers Path

### Driver 0: Kernel Module Fundamentals

Not "hello world"
- What exactly is an KLM?
- Why can kernel code be loaded dynamically?
- How is it different from user-space ELF?
- What happens during `insmod`?
- Symbol resolution?
- Why `init` and `exit`?
- Why kernel build system matters?

Code:
- minimal module
- module params
- logging
- module metadata
- Makefile
- `insmod`, `rmmod`, `modinfo`, `lsmod`, `dmesg`

### Driver 1:  Character Driver

It teaches: Linux sees everything as a file.
- major and minor number
- `file_operations`
- `open` / `read` / `write` / `ioctl`
- kernel <-> user-space boundary
- copy_to_user and copy_from_user
- why direct pointer access is dangerous

Code:
A pseudo device: `/dev/hexavik_device`

```bash
echo "hello" > /dev/hexavik_device
cat /dev/hexavik_device
```

### Driver 2: GPIO Driver

Bare metal GPIO understanding <-> Linux GPIO subsystem.

- Why no direct MMIO?
- Why subsystem abstraction?
- Why platform drivers?
- Device Tree?

Code:
GPIO LED + button

### Driver 3: Interrupt Driver

Button interrupt.
- hardirq
- threaded irq
- sleeping restrictions
- race conditions
- bottom halves

### Driver 4: I2C Driver

How Linux represents buses?

Code:
simple sensor driver

- probe/remove
- match tables
- DT bindings
- regmap
- client abstraction

### Driver 5: Memory

- `kmalloc`
- `kzalloc`
- DMA-safe memory
- `vmalloc`
- GFP flags
- cache alignment

### Driver 6: Concurrency

Real driver problems:
- spinlock
- mutex
- atomic_t
- wait queue
- completion
- workqueue
- kthread

### Driver 7: Performance and Debugging

- ftrace
- tracepoints
- dynamic debug
- perf
- lockdep
- crash analysis
- kernel panic reading
