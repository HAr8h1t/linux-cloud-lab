# Chapter 02 — The Linux Boot Process

## 1. Overview

The Linux boot process is the sequence of steps that takes a computer from power-on to a usable operating system. It involves the UEFI firmware, bootloader, Linux kernel, initramfs, root filesystem, and the first userspace process.

## 2. Boot Process Flow

```text
Power Button
    ↓
UEFI Firmware
    ↓
EFI System Partition (ESP)
    ↓
GRUB Bootloader
    ↓
Linux Kernel + initramfs
    ↓
Kernel Initialization
    ↓
Mount Root Filesystem
    ↓
systemd (PID 1)
    ↓
System Services and Login Screen
```

## 3. Main Components

### UEFI Firmware

UEFI is the firmware interface that initializes essential hardware and selects a boot entry from its stored boot configuration. It locates and launches an EFI executable, such as GRUB.

### EFI System Partition (ESP)

The ESP is a dedicated partition, usually formatted as FAT32, that stores EFI boot files. It may contain GRUB's EFI executable and other bootloader files. It is not the same as the Linux root filesystem.

### GRUB Bootloader

GRUB presents the boot menu and allows the user to select an operating system or kernel entry. It loads the Linux kernel and initramfs into memory and passes boot parameters to the kernel.

### Linux Kernel

The kernel is the core of Linux. It initializes hardware and kernel subsystems, manages memory and processes, and prepares the system to access its root filesystem.

### initramfs

The initramfs is a temporary early-userspace environment provided to the kernel during startup. It contains tools, scripts, and modules that may be needed to locate and access the real root filesystem.

It is especially useful when the root filesystem requires additional drivers, storage support, encryption handling, or other setup before it can be mounted.

### Root Filesystem

The root filesystem (`/`) contains the main Linux directory hierarchy, including system programs, configuration files, libraries, and other essential resources.

Once the required setup is complete, the system switches from the temporary early-userspace environment to the real root filesystem.

### systemd (PID 1)

After the kernel establishes the normal userspace environment, it starts the initial userspace process. On a typical Arch Linux installation, this process is `systemd`, with process ID 1.

systemd starts and manages services and other system units required to bring the system into its configured operating state.

## 4. Kernel Parameters

Kernel parameters are options passed to the kernel by the bootloader. They influence how the kernel starts and configures certain behaviors.

They can be inspected using:

```bash
cat /proc/cmdline
```

For example, parameters such as `quiet` reduce boot messages displayed on the screen, while `root=` identifies the root filesystem location.

## 5. Understanding initramfs on Arch Linux

Arch Linux commonly stores its kernel and initramfs under `/boot`, although the exact arrangement depends on the installation and boot configuration.

Typical files include:

```text
/boot/vmlinuz-linux
/boot/initramfs-linux.img
```

- `vmlinuz-linux` is the Linux kernel image.
- `initramfs-linux.img` is the initial RAM filesystem image used during early boot.

Useful inspection commands:

```bash
ls -lh /boot/
lsinitcpio /boot/initramfs-linux.img
```

The first command lists files and their sizes. The second displays the contents of the initramfs image.

## 6. Why the Boot Chain Matters

Each component depends on the previous stages working correctly. If firmware cannot locate a bootloader, the boot process may stop before GRUB appears. If the kernel cannot access the root filesystem, startup may fail during early boot. If essential userspace services fail, the system may boot incompletely.

Understanding this sequence makes it easier to troubleshoot bootloader configuration, kernel problems, initramfs issues, and failures during system startup.

## 7. Key Takeaway

The Linux boot process is a coordinated transition from firmware-controlled startup to a fully running operating system. UEFI launches the bootloader, GRUB loads the kernel and initramfs, the kernel initializes the system and reaches the real root filesystem, and systemd manages the userspace startup process.
