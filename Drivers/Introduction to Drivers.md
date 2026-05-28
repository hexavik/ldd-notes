
When writing drivers, a programmer should pay particular attention to this fundamental concept: write kernel code to access the hardware, but don't force particular policies on the user, since different users have different needs. The driver should deal with making hardware available, leaving all the issues about how to use the hardware to the applications. A driver, then, is flexible if it offers access to the hardware capabilities without adding constraints. Sometimes, however, some policy decisions must be made. for example, a digital I/O driver may only offer byte-wide access to the hardware in order to avoid the extra code needed to handle individual bits.

A device driver is software that enables communication between the operating system and hardware. It acts as an intermediary, allowing user applications to access hardware components like storage devices, network interfaces, and peripherals. Without device drivers, the OS would not be able to control or communicate with hardware efficiently.

### Key functions of a Device Driver

- **Configuration:** Initializes and configures hardware components.
- **Data Transfer:** Manages read and write operations between the OS and the hardware.
- **Request Handling:** Processes commands from the OS and hardware interrupts.
- **Interface Provisioning:** Provides standardized interfaces to user-space applications for communication with hardware.

### Linux Kernel Architecture

**System Call Interface** has an important role, which can be spilt into following processes:
- Process Management
- Memory Management
- Filesystems
- Device Control
- Networking

// add diagram

### Loadable Modules

Each piece of code that can be added to the kernel at runtime is called a [[Kernel Modules|module]].

The Linux kernel offers support for quite a few different types (or classes) of modules, including, but not limited to, device drivers. Each module is made up of object code (not linked into a complex executable) that can be dynamically linked to the running kernel by the *insmod* program and can be unlinked by the *rmmod* program.

### Classes of Devices and Modules

- [[Character Devices]]
- [[Block Devices]]
- [[Network Interfaces]]

[[Platform vs PCI vs USB Drivers]]
[[file_operations struct]]
[[Major and Minor Numbers]]
[[copy_to_user vs copy-from_user]]

