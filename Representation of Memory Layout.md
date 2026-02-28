```
+----------------------------+ 0xBFFF_FFFF
|                            |
|        DDR3 DRAM           |
|                            |
+----------------------------+ 0x8000_0000
|        U-Boot              | 0x8080_0000
|        Kernel              | 0x8000_8000
|        DTB                 | 0x8300_0000
|        Initramfs           | 0x8400_0000
+----------------------------+
|        OCRAM               | 0x0090_0000
+----------------------------+
|        TCMU (M4)           | 0x0080_0000
+----------------------------+
|        TCML (M4)           | 0x007F_8000
+----------------------------+
|        Boot ROM            | 0x0000_0000
+----------------------------+
```

The above example shows the memory layout followed for Linux built for i.MX7D processor.
The i.MX7D SABRE board features:

- **1 GB DDR3 RAM** operating at 533 MHz
- **Dual ARM Cortex-A7 cores** (up to 1 GHz)
- **Single ARM Cortex-M4 core** (up to 200 MHz)
- **On-chip SRAM** and **Tightly Coupled Memory (TCM)**
- **External memory interfaces**: eMMC, NAND, QSPI, SD card

---

## Detailed Memory Layout

### 1. **Boot ROM (Internal)**

- **Address Range**: `0x0000_0000` to `0x0000_FFFF`
- **Size**: 64 KB
- **Purpose**: Executes immediately after reset; responsible for initial boot procedures and loading the Secondary Program Loader (SPL) from external storage.

### 2. **On-Chip SRAM (OCRAM)**

- **Address Range**: `0x0090_0000` to `0x0093_FFFF`
- **Size**: 256 KB
- **Usage**: Utilized by the SPL for early initialization tasks before DRAM is configured.

### 3. **Tightly Coupled Memory (TCM) for Cortex-M4**

- **TCML (Lower TCM)**: `0x007F_8000` to `0x007F_FFFF` (32 KB)
- **TCMU (Upper TCM)**: `0x0080_0000` to `0x0080_7FFF` (32 KB)
- **Purpose**: Provides low-latency memory access for the Cortex-M4 core, ideal for real-time operations.

### 4. **DDR3 DRAM**

- **Address Range**: `0x8000_0000` to `0xBFFF_FFFF`
- **Size**: 1 GB
- **Allocation**:
	- **U-Boot**: Typically loaded at `0x8080_0000`
	- **Linux Kernel**: Loaded at `0x8000_8000`
	- **Device Tree Blob (DTB)**: Placed at `0x8300_0000`
	- **Initramfs**: Positioned at `0x8400_0000`
	- **Root Filesystem (if not using initramfs)**: Mounted from external storage like SD card or eMMC

### 5. **QSPI Flash (Optional)**

- **Address Range**: Mapped via FlexSPI interface
- **Usage**: Can store bootloader components or other firmware; accessible after initialization.

### 6. **Peripheral Registers**

- **Address Range**: `0x3000_0000` to `0x6FFF_FFFF`
- **Purpose**: Memory-mapped I/O for various peripherals like UART, SPI, I2C, GPIO, etc.

---

## 🔄 Boot Process and Memory Utilization

1. **Boot ROM**: Executes from `0x0000_0000`, loads SPL into OCRAM.
2. **SPL**: Runs from OCRAM (`0x0090_0000`), initializes DRAM, and loads U-Boot into DDR.
3. **U-Boot**: Executes from DDR (`0x8080_0000`), loads the Linux kernel, DTB, and initramfs into designated DDR addresses.
4. **Linux Kernel**: Starts execution from `0x8000_8000`, sets up MMU, and mounts the root filesystem.