# Learning Progress

A living record of my progress through Linux internals, system administration, cloud infrastructure, and security fundamentals.

**Last updated:** October 2026

For the overall project and roadmap, see [README.md](README.md).

## Current Status

| Area | Status |
|---|---|
| Arch Linux installation and desktop setup | Set up |
| Arch Linux and Windows dual boot | Working |
| Git and GitHub documentation workflow | Set up |
| Linux mental model | Studied; continuing to consolidate |
| Boot process and initramfs | In progress |
| Filesystems and VFS | Upcoming |
| Networking and cloud fundamentals | Upcoming |

## Chapter Progress

### Chapter 01 — Linux and the Arch Mental Model

**Status:** Foundations studied; notes need continued consolidation.

Topics explored:
- Linux as a system made up of the kernel and userspace.
- The roles of the kernel, system services, shell, and desktop environment.
- The relationship between hardware, the operating system, and applications.
- Basic system inspection using Linux commands.

Existing notes:
- `chapter-01/notes-1.txt`
- `chapter-01/notes-3.txt`
- `chapter-01/system-map.txt`

The goal is to understand how the major components fit together instead of treating Linux as just a graphical desktop or a collection of terminal commands.

### Chapter 02 — The Linux Boot Process

**Status:** In progress.

Topics explored:
- The boot sequence from power-on to userspace.
- UEFI firmware and the EFI System Partition.
- GRUB and the role of the bootloader.
- The Linux kernel and its boot parameters.
- initramfs and why early userspace is needed.
- The transition to the real root filesystem.
- systemd as PID 1 on a typical Arch Linux installation.

Practical observations:
- Investigated the UEFI boot configuration.
- Troubleshot a dual-boot issue involving Windows Boot Manager and GRUB.
- Restored a working GRUB menu for selecting Arch Linux or Windows.
- Inspected kernel information and boot-related files.
- Examined the initramfs image and its contents.

Related notes:
- `chapter-01/boot-chain.txt`
- `chap-02/boot-process.md`

**Current learning objective:** Understand the responsibility of each boot stage and how a failure at one stage affects the next.

## Practical Work Log

### Dual-Boot Troubleshooting

**Problem:** The computer began booting directly into Windows instead of showing the expected GRUB menu.

**Investigation:** Examined the UEFI boot entries and the relationship between firmware boot selection, GRUB, and Windows Boot Manager.

**Outcome:** Restored a working boot menu with entries for Arch Linux and Windows.

**Learning:** UEFI boot entries, bootloader configuration, and operating-system selection are related but distinct parts of the startup process.

### Kernel and initramfs Inspection

**Investigation:** Inspected kernel information, files under `/boot`, and the contents of the initramfs image.

**Commands used:**

```bash
uname -r
cat /proc/cmdline
ls -lh /boot/
lsinitcpio /boot/initramfs-linux.img
```

**Learning:** The running kernel version, boot parameters, kernel image, and initramfs provide different pieces of information about the boot process.

## Next Milestones

### Immediate priorities

- [ ] Review and consolidate Chapter 01 notes.
- [ ] Finish Chapter 02 and ensure the boot-chain explanation is clear.
- [ ] Document the dual-boot troubleshooting process in a dedicated lab.
- [ ] Study the Linux filesystem hierarchy and the purpose of `/proc`, `/sys`, and `/dev`.
- [ ] Understand how systemd starts and manages services.
- [ ] Continue recording practical commands with explanations and observed outputs.

### Upcoming areas

- [ ] Package management with pacman and repository concepts.
- [ ] Users, groups, permissions, and access control.
- [ ] Processes, services, journal logs, and troubleshooting.
- [ ] Networking fundamentals and command-line diagnostics.
- [ ] Bash scripting and automation.
- [ ] Linux security fundamentals and system hardening.
- [ ] Cloud infrastructure fundamentals and practical server work.

## Learning Principles

- Understand a command before relying on it.
- Record actual observations instead of inventing expected results.
- Avoid unnecessary system changes just to complete an exercise.
- Keep backups before making risky boot or system configuration changes.
- Document mistakes and troubleshooting, not just successful outcomes.
- Update this tracker as work is completed.

## Update History

- **October 2026:** Established the GitHub repository and began tracking Linux learning notes with Git.
- **October 2026:** Documented the Linux boot process and investigated the Arch Linux dual-boot configuration.

*This tracker reflects ongoing learning. A topic is marked complete only after its concepts and practical work have been reviewed.*
