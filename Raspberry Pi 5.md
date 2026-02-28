## Building the raspberry pi kernel manually

1. Download the RPi Kernel version 6.10.y from git repository:

```bash
git clone --depth=1 https://github.com/raspberrypi/linux.git -b rpi-6.1.y
cd linux
```

2. Set up build config:

```bash
make ARCH=arm64 CROSS_COMPILE=aarch64-linux-gnu- bcm2712_defconfig
```

3. Menuconfig:

```bash
make ARCH=arm64 CROSS_COMPILE=aarch64-linux-gnu- menuconfig
```

4. Build kernel + DTBs:

```bash
make -j$(nproc) ARCH=arm64 CROSS_COMPILE=aarch64-linux-gnu- Image modules dtbs
```

To clean kernel build and cofigs

```bash
make mrproper
```

5. Get Root Filesystem:
   Raspberry Pi Imager is a great tool to install the images on the SD card or USB flash disk. I used Raspberry Pi OS Lite (64-bit) to get the initial rootfs and kernel to be installed so that I can overwrite our custom kernel. Use imager for this as it has this OS already available for Raspberry Pi 5.

6. Replace the kernel + DTBs + overlays:

```bash
cd rpi-kernel (in case you are not in the kernel directory)
cp arch/arm64/boot/Image /media/<user>/<boot>/kernel8.img
cp arch/arm64/boot/dts/broadcom/bcm2712-rpi-5-b.dtb /media/<user>/<boot>/
cp -r arch/arm64/boot/dts/overlays/* /media/<user>/<boot>/overlays/
make ARCH=arm64 INSTALL_MOD_PATH=/media/<user>/<rootfs>/ modules_install
```

7. Ensure `config.txt` has:

```ini
kernel=kernel8.img
enable_uart=1 # for SSH over tty
dtoverlay=vc4-kms-v3d
```

## Verify

Boot the SD card ot USB Flash Drive from Raspberry Pi 5. It will show you the login shell if everything goes well. Log into it with username and password set through either from Imager's edit settings or creating an user at the time of first boot.

To check the kernel boot logs use following simple command:

```bash
journalctl -b
journalctl -b | grep printk
```

## Execution
SSH (Secure Shell) is a network protocol that enables secure remote connections between two computers over an insecure network. Using SSH with RPi allowed me to write programs directly from my host computer, so it saved my efforts of switching between my host computer to RPi5 for display and keyboard.
