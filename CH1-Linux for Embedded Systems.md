## What This Book Is and What It Is Not

- A build system that integrates many different steps necessary to create a fully functional
  Linux OS stack from scratch is rather complex.
- In this book, you will learn how the OpenEmbedded build system works, how you can
  write recipes to build your own software components, how to use and create Yocto Project
  board support packages to support different hardware platforms, and how to debug build
  failures.

## Who Should Read This Book

- This book is intended for software developers and programmers who have a working
  knowledge of Linux.

## Chapter 1

### 1.1 Why Linux for Embedded Systems?

- Linux debuted as a general-purpose operating system (GPOS) for PC hardware with Intel x86 architecture.
- Linux remained true to its GPOS origins in three major aspects that do
  not make it the premier choice of engineers for an embedded operating system at first: - Filesystem: Linux is a file-based operating system requiring a filesystem on a
  block-oriented mass storage device with read and write access. - Memory Management Unit (MMU): Linux is a multitasking operating system. - Real Time: Embedded systems running critical applications may require predictive responses with guaranteed timing within a certain margin of error, commonly referred to as determinism. - hard real-time system :An operating system that can absolutely guarantee a maximum time for the operations.
  -soft real-time An operating system that typically performs its operations within a certain time - There are many reasons for the rapid growth of embedded Linux. To name a few: - Royalties: Unlike traditional proprietary operating systems, Linux can be deployed without any royalties. - Hardware Support: Linux supports a vast variety of hardware devices including all major and commonly used CPU architectures: ARM, Intel x86, MIPS, and PowerPC in their respective 32-bit and 64-bit variants. - Networking: Linux supports a large variety of networking protocols. - Modularity: A Linux OS stack is composed of many different software packages. - Scalability: Linux scales from systems with only one CPU and limited resources tosystems featuring multiple CPUs with many cores. - Source Code: The source code for the Linux kernel, as well as for all software. - Developer Support: Because of its openness, Linux has attracted a huge number of active developers
- OpenEmbedded:s a build framework composed of tools, configuration data, and recipes to create Linux distributions targeted for embedded devices. At the core of OpenEmbedded is the BitBake task executor that manages the buildprocess.
- The Yocto Project: is not a single open source project but represents an entire family of projects that are developed and maintained under its umbrella.

### 1.3 A Custom Linux Distribution—Why Is It Hard?
- building and maintaining an operating system is not a trivial task. Many different aspects of the operating system have to be taken into consideration to create a fully functional computer system:
- Bootloader: The bootloader is the first piece of software responsible for initializing the hardware, loading the operating system kernel into RAM, and then starting the kernel. - Kernel: is the core of an operating system. It manages the hardware resources of the system and provides hardware abstraction through its APIs to other software.
- Device Drivers: They provide application software with access to hardware devices in a structured form through kernel system calls.
  ![image](https://github.com/Rabie45/Embedded-Linux-System-with-the-Yocto-Project/assets/76526170/8d775f14-c46b-40ff-a84c-48f1188db08c)

