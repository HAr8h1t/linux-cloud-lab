# Linux & Cloud Infrastructure Lab

**Learning Linux from the inside out — one concept, one experiment, and one documented result at a time.**

Welcome to my hands-on Linux learning repository. This is where I document my journey into Linux internals, system administration, cloud infrastructure, and the foundations of cybersecurity.

Rather than memorizing commands or following tutorials blindly, I aim to understand how things work, why they work that way, and how to troubleshoot them when they fail.

This repository is a growing technical notebook containing concepts, command-line experiments, system observations, troubleshooting notes, and practical labs.

## 🎯 Project Goals

- **Understand Linux internals:** Explore the boot process, kernel, filesystems, processes, memory, and system services.
- **Build practical skills:** Learn by inspecting and experimenting with a real Arch Linux installation.
- **Develop troubleshooting ability:** Investigate problems, understand their causes, and document solutions.
- **Build toward cloud infrastructure:** Progress from Linux fundamentals to networking, automation, servers, and cloud concepts.
- **Establish security fundamentals:** Learn permissions, authentication, system hardening, and essential security practices.
- **Document the journey:** Maintain a clear record of what I learn, what I test, and what I still need to understand.

## 🖥️ My Learning Environment

| Component | Setup |
|---|---|
| Operating system | Arch Linux |
| Desktop environment | KDE Plasma |
| Boot configuration | UEFI with GRUB |
| Computer setup | Arch Linux and Windows dual boot |
| Terminal | Konsole |
| Version control | Git and GitHub |
| Documentation | Markdown and text notes |

This environment gives me a real system to inspect and learn from instead of relying entirely on simulated examples.

## 🗺️ Learning Roadmap

The roadmap progresses from Linux fundamentals toward infrastructure and security. Topics will be expanded as I study and complete practical work.

| Stage | Topics | Status |
|---|---|---|
| 01 | Linux mental model and architecture | Studying |
| 02 | Boot process: UEFI, GRUB, kernel, initramfs | In progress |
| 03 | Linux filesystem, VFS, and pseudo-filesystems | Planned |
| 04 | Package management, repositories, and AUR | Planned |
| 05 | Users, groups, permissions, and access control | Planned |
| 06 | Processes, systemd, services, and logs | Planned |
| 07 | Networking fundamentals and troubleshooting | Planned |
| 08 | Bash scripting and automation | Planned |
| 09 | Linux security and system hardening | Planned |
| 10 | Cloud infrastructure and server administration | Planned |
| 11 | Practical infrastructure projects | Planned |

*The roadmap is a learning plan, not a claim that every topic has already been completed.*

## 📚 Repository Structure

```text
arch-course/
├── README.md
├── progress.md
├── .gitignore
├── chapter-01/
│   ├── notes-1.txt
│   ├── notes-3.txt
│   ├── boot-chain.txt
│   └── system-map.txt
├── chap-02/
│   └── boot-process.md
└── ...
```

- **Chapter notes:** Explanations, concepts, and important observations.
- **Boot process summary:** A consolidated explanation of the Linux startup sequence.
- **Progress tracker:** Current status, completed work, and next milestones.
- **Future labs:** Practical experiments, command outputs, and troubleshooting records.

The folder names and contents will evolve as the repository grows.

## 🧪 How I Learn

My approach is based on a simple cycle:

1. **Understand** — Learn the concept and the problem it solves.
2. **Inspect** — Examine the real system using Linux commands and configuration files.
3. **Experiment** — Perform a controlled practical task.
4. **Explain** — Record what happened and why.
5. **Troubleshoot** — Investigate unexpected results and learn from them.
6. **Document** — Save useful findings and update my progress.

Commands are not the final goal. Understanding their purpose, behavior, and consequences is.

## 📈 Progress and Documentation

For the latest learning status, see [progress.md](progress.md).

As the project develops, I intend to add practical labs, troubleshooting case studies, small automation scripts, and infrastructure experiments.

## 🌱 Current Focus

I am building a strong Linux foundation by understanding the boot process, the relationship between firmware and the operating system, and how Linux transitions from kernel initialization to userspace.

The long-term goal is to develop practical skills that can be demonstrated through documented experiments and real projects.

---

**Status:** Active learning project  
**Maintained by:** Harshit Dandhare  
**Approach:** Learn → Inspect → Experiment → Understand → Document
