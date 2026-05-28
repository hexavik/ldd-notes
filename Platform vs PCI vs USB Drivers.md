## Platform Driver

Used for:
- On-chip peripherals
- SoC internal devices

Defined via:
- Device Tree

Used in:
- ARM boards
- Raspberry Pi

Flow:
Device Tree -> `platform_device` -> `platform_driver` -> `probe()`

## PCI Driver

Used for:
- PCI / PCIe devices

Device auto-detected via:
- PCI bus enumeration

Matching via:
- Vendor ID
- Device ID

## USB Driver

Used for:
- USB devices

Matching via:
- USB device descriptors
- Vendor/Product ID

