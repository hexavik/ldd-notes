[[Representation of Memory Layout]]

## Memory Size of the Kernel

### **Minimum Size of Linux Kernel**

Condition: Highly stripped down, minimal drivers, no initramfs, no modules.

- **Size (compressed `zImage`)**: ~1.5 MB – 2 MB
- **Size (uncompressed `vmlinux`)**: ~4 MB – 6 MB
- **Achievable with**:
    - Tiny kernel configs (like `tinyconfig` or `miniconfig`)
    - Minimal platform support (e.g., just for ARM Cortex-A7)
    - Stripped debug info and disabled printk

🔹 _Used in deeply embedded systems or microcontrollers with MMUs._

---

### **Maximum Size of Linux Kernel**

Condition: Fully featured kernel with many drivers, subsystems, debugging, networking, file systems.

- **Size (compressed `zImage`)**: 8 MB – 20+ MB
- **Size (uncompressed `vmlinux`)**: 30 MB – 100+ MB

🔹 _Examples include Android kernels, server builds, or developer builds with debug symbols._

---
### Factors Affecting Size

| Factor               | Impact                                            |
| -------------------- | ------------------------------------------------- |
| `CONFIG_DEBUG_*`     | Adds many MBs                                     |
| Filesystems, Drivers | Increases size                                    |
| Initramfs            | Embedded rootfs can double size                   |
| LTO / Clang          | May reduce or increase depending on optimizations |
| Static vs Modular    | Static builds are larger than modular ones        |
