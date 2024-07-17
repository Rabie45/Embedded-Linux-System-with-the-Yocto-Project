# 11. Application Development
## 11.1 Inside a Yocto Project ADT (Apllicatiopn development toolkit)
- Cross-Development Toolchain: The ADT cross-development toolchain comprises a
cross-compiler, cross-linker, cross-debugger, and a set of other tools used for
application development.
- System Roots: An ADT contains two system roots: one for the development host,
which contains the cross-development toolchain and other tools, and one for the
target, which is an entire root filesystem for the target that also contains thedevelopment packages with header files, libraries, and more.
- QEMU Emulator: Together with a kernel and a root filesystem image, QEMU
provides for testing of your user space applications without the actual hardware. You
can develop applications for the target even before the hardware is available.
- Environment Setup: Scripts for setting up the environment on your development
host for cross-development with the ADT are provided.
Yocto Project Eclipse Plugin: The plugin is for the popular Eclipse IDE 1 to
integrate an ADT.

- Profiling Tools: Various user space tools for profiling applications on your target system are included with an ADT. The set of tools includes the following:
- LatencyTOP: LatencyTOP 2 is a tool to measure and resolve latency issues in applications for Linux systems that impact the user experience, such as audio and video skips during media playback, delayed response to user input on deskto interfaces, and more, even if your system has plenty of CPU power.
- 2. http://git.infradead.org/latencytop.git
- PowerTOP: Power management is paramount for virtually all embedded systems, particularly if they are running on batteries. PowerTOP 3 is a diagnostic tool to measure power consumption and trace it into applications, libraries, routines, and code fragments.
- 3. https://01.org/powertop
- OProfile: OProfile 4 is a systemwide profiling tool for Linux systems, capable of profiling running code by adding only minimal overhead. It consists of a Linux kernel driver and a daemon for collecting sample data for the target system, as well as several offline tools to analyze the sample data on a host system.
- 4. http://oprofile.sourceforge.net
- Perf: Perf 5 is a Linux profiling tool that uses Linux kernel performance counters for collecting data on various hardware and software events, such as CPU cycles, instructions, interrupts, cache references, and many more.
- 5. https://perf.wiki.kernel.org
- System Tap: System Tap 6 is infrastructure and instrumentation for gathering
information about a running Linux system. System Tap scripts allow tracing
virtually any system event by placing probes.
- 6. https://sourceware.org/systemtap
Linux Trace Toolkit—Next Generation (LTTng): LTTng 7 is an open source tracing framework for Linux systems that provides instrumentation to identify system events, extraction of identified events with little overhead, and tools for investigation and analysis.

## 11.2 Setting Up a Yocto Project ADT
- The ADT installer then downloads the appropriate cross-toolchain, root filesystems, and
more from the Yocto Project download site and installs them on your development
system.
- Build the ADT Installer: Use a Yocto Project build environment to build the ADT
installer tarball yourself rather than downloading it.
- Build a Toolchain Installer to Create an ADT: Using your target build
environment, you create a toolchain installer that includes cross-toolchain and target
system root that exactly match your target system with all the development packagesfor the software packages that you may have added to your custom system image.
### 11.2.1 Building a Toolchain Installer
- If you already have a build environment for your target system, potentially including a
BSP and other layers, you can use it to build a toolchain installer with all the software
components that you added to a custom root filesystem image.


# I think this chapter is very important and u have to read it whit ur self and applay every sample





















