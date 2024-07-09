# CH6  Linux System Architectur
- Understanding the architecture of a Linux system provides the context for the methods
employed by OpenEmbedded Core.
## 6.1 Linux or GNU/Linux?
- The reason behind this distinction is that the name Linux, strictly
speaking, refers only to the Linux kernel as the foundation or core of an operating system.
For an operating system to be useful, many more applications and libraries are required
—development tools, compilers, editors, shells, utilities, and more—that are not part of
the kernel.
## 6.2 Anatomy of a Linux System
- The bootloader is not strictly part of the Linux system, but it is necessary to start the system
and therefore relevant in the context of building a fully functional system with the Yocto
Project.
-A Linux OS can be divided into two levels, the kernel space and the user or application
space. 
- This distinction is not just conceptual but originates from the fact that code from
the kernel space is executed in a different processor operating mode than code from the
user space.
- All kernel code is executed in unrestricted or privileged mode. In this mode, any
instruction of the instruction set of the architecture can be executed
- Application code, by contrast, is executed in restricted or user mode. In this mode, instructions that directly access hardware—input/output (I/O) instructions
- Changing from user mode to kernel mode requires the CPU to switch contexts, which is
achieved by special instructions that are dependent on the architecture.
## 6.3 Bootloader
- Although the bootloader plays only a very short role in the life cycle of a system during
startup, it is a very important system component.
- Bootloaders are divided into two categories: loaders and monitors.
- In the former case, the bootloader provides only the mere functionality to initialize the hardware and load the operating system. 
- In the latter case, the bootloader also includes a command-line interface through which a user can interact with the bootloader, which can be used for configuration, reprogramming, initialization, and testing of hardware and other tasks.
### 6.3.1 Role of the Bootloader
- After power is applied to a processor board, the majority of the hardware components
need to be initialized before any software application can be executed. Each processor
architecture has its own set of initialization steps that need to be performed before the
hardware can be used.
- All other hardware and peripherals are initialized by the operating system itself at a later stage of the boot process.
- Most processors have a default address from which they read the first instruction to be
executed. Other processors read that address from a defined location.
### 6.3.2 Linux Bootloaders
- As a Linux system developer you have many choices for a bootloader for your project.
Bootloaders differ in functionality, processor, and operating system support. Many of them
can boot not only Linux but also other operating systems.
- Virtually all bootloaders allow the selection of different systems from a menu before the
default is booted after a configurable timeout.
- LILO The LInux LOader (LILO) was once the standard bootloader for virtually all Linux
distributions for x86 systems.
- ELILO The EFI-based LInux LOader (ELILO) is a branch of LILO made by Hewlett-Packard to
support EFI-based hardware.
- GRUB The GNU GRand Unified Bootloader (GRUB) was originally designed and implemented
by Erich Stefan Boleyn. It started replacing LILO as the mainstream bootloader for Linux
distributions from 2013 on.
SYSLINUX
- The Syslinux Project is an umbrella project covering multiple lightweight bootloaders for
different purposes:
SYSLINUX: Bootloader for FAT and NTFS filesystems that can handle hard drives,
floppy disks, and USB drives.
ISOLINUX: Bootloader for bootable El Torito CD-ROMs.
EXTLINUX: Bootloader for Linux EXT2/EXT3/EXT4 and BTRFS filesystems.
PXELINUX: Bootloader for network booting via BOOTP, DHCP, and TFTP using

- U-Boot U-Boot, the Universal Bootloader, also known as Das U-Boot, can be considered the
Swiss army knife among the embedded Linux bootloaders.
## 6.4 Kernel
- The core resources of a computer are typically composed of
- CPU: The CPU is the execution unit for programs. The kernel is responsible for
allocating programs to processors (scheduling) and setting up the execution
environment (dispatching).
- The kernel is responsible for
allocating memory to the programs, protecting memory, and deciding what happens
if a program requests more memory than available, which in most cases is less than
the physical memory of the computer.
- I/O Devices: I/O devices represent sources and sinks for data such as keyboard,
mouse, display, network interfaces, storage, and more.

### 6.4.1 Major Linux Kernel Subsystems
- The Linux kernel is divided into a set of major subsystems
- Architecture-Dependent Code
Although the majority of the Linux kernel code is independent of the architecture it is
executed on, there are portions that need to take the CPU architecture and the platform
into consideration.
- Device Drivers Device drivers handle all the devices that the Linux kernel supports.
- Memory Managemen
- Process Management
- Network Stack
- Interprocess Communication
- System Call Interface
### 6.4.2 Linux Kernel Startup
After the bootloader has copied the Linux kernel image into memory, it passes control
to the bootstrap loader that is a prepended part of the kernel image. To save space, the
kernel image is typically compressed, and it is the bootstrap loader’s responsibility to
create the proper execution environment for the kernel, decompress the kernel, relocate
the kernel in memory, and then pass control to it. The bootstrap loader directly passes
control to the kernel entry point inside a module, which is called head.o for most
architectures.
The module head.o contains architecture-specific but platform-independent
initialization code for the particular CPU architecture. This module is derived from the
assembly language file head.S, which is located inside the directory
linux/arch/<ARCH>/kernel, where <ARCH> is replaced by the particular
architecture.
At a high-level, the head.o module performs the following tasks:
Verifies the correct architecture and CPU
Detects CPU type and functionality, such as hardware floating-point capabilities
Enables the CPU’s MMU and creates the initial table of memory pages
Establishes basic error reporting and handling
Switches to non-architecture-specific kernel startup function start_kernel() in
main.c
The file main.c in linux/init contains the bulk of the Linux kernel startup code,
from architecture setup, kernel command-line processing, and initialization of the first
kernel thread to mounting the root filesystem and executing the first user space application
program.
After performing a basic set of kernel initialization, the start_kernel() function
calls rest_init(), which spawns the first kernel thread. This thread is spawned by
calling kernel_thread() with the function kernel_init() as the first parameter.
This function becomes the init thread. At this point, there are now two threads running:
start_kernel() and kernel_init(). The former kicks off the scheduler and thenloops forever in the cpu_idle() function. The latter becomes the init() thread, the
parent of all user space processes with the process ID (PID) of 1.
At the end, kernel_init() launches the first user space application. If an init
command was passed as part of the kernel command line, kernel_init() first
attempts to start that program. If no init command was passed, the function then tries a
set of default programs that it loads from the root filesystem. It tries /sbin/init,
/etc/init, /bin/init, and /bin/sh in this order until one succeeds. If none
succeeds, the kernel exits with the well-known error message “No init found. Try passing
init= option to the kernel. See Linux Documentation/init.txt for guidance.”
Typically, the first user space process is part of an init or a startup system that then
launches other user processes. The init systems typically used by Linux desktops and
servers are System V Init, systemd, and Upstart. Embedded systems commonly use more
lightweight startup systems such as BusyBox or directly launch their core application.
## 6.5 User Space
- Now that the kernel has completed its initialization, launched the init process, and
within it executed the first user space application, the system has entered userland or user
space. User space is all the code that runs outside the operating system’s kernel and
includes all the libraries and application programs.



