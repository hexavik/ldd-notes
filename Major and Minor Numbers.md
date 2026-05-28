Run:

```bash
cat /proc/devices
```

Major Number:
- Identifies driver
Minor Number:
- Identifies device instance

> [!Example]
> - Major = 240 (your driver)
> - Minor = 0,1,2 -> multiple devices

Device Node:

```bash
/dev/mydevice
```

Created using:

```bash
mknod
```

Or automatically via:
- udev

