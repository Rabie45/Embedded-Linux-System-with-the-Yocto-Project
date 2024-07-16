# CH10 Board Support Packages
- how the build system supports
creating Linux kernel and root filesystem images for actual hardware.
## 10.1 Yocto Project BSP Philosophy
- a BSP is a specific adaptation of a given operating system for given hardware,
such as an embedded device board. BSPs are typically provided by semiconductor and
board vendors to their customers so that the customers can build, load, and run the
operating system on the vendor’s hardware.
- BSPs typically comprise these items:
   - Documentation:  BSP, provides information on the specific hardware the BSP supports, and includes instructions on how to use the BSP to build the operating system for the hardware, to transfer the operating system image to the hardware, and to boot the hardware.
   - Development Tools: the vendors include at least a toolchain with
compiler, assembler, linker, and archiver. Some BSPs may even include a software development
kit (SDK) and eventually an integrated development environment (IDE).
   - Operating System Source Code and Binaries:  All other operating system files are provided as binaries. Since operating system sources and binaries are potentially large, it is common that the provided development tools use the vendor’s repositories and download sites to obtain the
source code and binaries as necessary when building the system.
   - If the hardware targeted by the BSP requires special device drivers, configuration, or other modules on top of the base operating system software packages, the BSP may provide them.
   - Filesystem Images: A BSP may also include entire filesystem images for the
supported hardware, which is very convenient, as it helps in bringing up the target
and provides a reference when building your own system.
   - The Yocto Project takes a different approach for BSPs: 
     - They rely on the base metadata layers such as OpenEmbedded Core (meta) and possibly other
layers. 
     -  Yocto Project BSPs do not include a build system or any development tools. They
are provided by the Yocto Project itself and created during the build process.
     - Yocto Project BSPs do not include any source code other than recipes and eventually
patches. 
     - Yocto Project BSPs are concerned only with components that are specific to the
particular hardware.
     - All Yocto Project BSPs depend at least on the OE Core metadata layer.
### 10.1.1 BSP Dependency Handling
- we discussed BitBake’s conditional variable assignment mechanism using the variable OVERRIDES. This mechanism is the backbone of the build system’s dependency handling for BSPs.

## 10.2 Building with a BSP
- A BSP simply is a metadata layer that contains a machine definition in the form of a
configuration file with the name of the machine ending in .conf.
- To use a BSP, you have to add it to the BBLAYERS variable in the conf/bblayers.conf file of
your build environment.
- You then have to set the MACHINE variable inside the conf/local.conf file of your build environment to the name of the machine you want to build for.
### 10.2.1 Building for the BeagleBone
- The BeagleBone is a development board based on the Texas Instruments AM335x ARM
Cortex-A8 SoC. Hardware and software are open designs, created and supported by the
BeagleBoard.org Foundation
- Building the BeagleBone Images uncomment the line
```MACHINE = “beaglebone” 
```
in conf/local.conf of your build environment
- Understanding the BeagleBone Boot Process 
  - To be able to boot the target hardware, you need to understand how your target hardware,
in our case the BeagleBone, boots its operating system.
    -  1. After power-on-reset (POR), the BeagleBone’s SoC loads and runs a stage 0
bootloader from its on-board ROM.
    - 2. The stage 0 bootloader accesses a file called MLO, which must be located in the first
sectors of the first partition on the SD card. MLO is a stage 1 bootloader that is provided by U-Boot’s secondary program loader (SPL) functionality.
    - 3. The U-Boot SPL MLO configures the off-chip memory of the BeagleBone and then
loads the file u-boot.img, which is the full U-Boot bootloader. U-Boot is the stage 2 bootloader in this process.
    - 4. U-Boot then loads the Linux kernel image into memory and passes control on to the
Linux kernel. U-Boot by default expects the kernel image uImage in the /boot directory of the second partition on the SD card. The second partition contains a Linux ext3 filesystem with the entire root filesystem for the BeagleBone.
    - 5. The Linux kernel then begins its boot process, mounts the root filesystem asinstructed by the kernel command line provided by the bootloader, and finally launches the first user space process.
    - The SD card requires a specific layout with two partitions: a small FAT boot partition
and a Linux partition containing the root filesystem.

### 10.2.2 External Yocto Project BSP
- A steadily increasing number of boards is available built around a large variety of
SoCs. The vast majority uses silicon that is based on ARM architecture, but x86, x86_64,
xScale, and PowerPC can also be found. The Raspberry Pi has set a new benchmark for a
low-cost embedded computer capable of running a full Linux OS stack.
- Finding the Yocto Project BSP for Your Board 
 https://layers.openembedded.org/
``` 
meta-fsl-arm: BSP layer for Freescale platforms using SoC based on ARM
architecture.
meta-fsl-ppc: BSP layer for Freescale platforms using SoC based on PowerPC
architecture.
meta-intel: Compound BSP layer for Intel platforms based on x86 and x86_64
architectures. This layer contains multiple sublayers for the actual platforms.
meta-intel-galileo: BSP layer for Intel Galileo platform support.
meta-intel-quark: BSP layer for Intel Quark platform support.
meta-minnow: BSP layer for the original MinnowBoard (not the MinnowBoard
Max, which is supported by meta-intel).
meta-raspberrypi: BSP layer for Raspberry Pi 1 and Raspberry Pi 2 devices.
meta-renesas: BSP layer for Renesas devices.
meta-ti: BSP layer for Texas Instruments devices, including extended hardware
support for the BeagleBone that is not provided by meta-yocto-bsp.
meta-xilinx: BSP layer for Xilinx devices.
meta-zynq: BSP layer for Zynq devices.
```
- Building with an External Yocto Project BSP
Yocto Project BSPs are layers, and building with them is as easy as 1-2-3:
  - 1. Include the BSP layer with your build environment by adding its path to the
BBLAYERS variable in conf/bblayers.conf.
  - 2. Assign the machine from the BSP you want to build for to the MACHINE variable in
conf/local.conf.
  - 3. Choose an image target and start your build—for instance, bitbake -k core-
image-minimal. It is good advice to start with a small image, just capable of
booting to the console command line, such as core-image-minimal or core-image-base. If that works, you can test a larger image such as core-image-
sato or start building your own custom image recipe. Some BSPs contain their
own image targets, which are a good starting point too.

## 10.3 Inside a Yocto Project BSP
Yocto Project BSPs are specialized BitBake layers.

### 10.3.9 Recipe Files
- BSP-Specific Recipe Files (recipes-bsp): Miscellaneous recipes specific for
the BSP.
- Core Support Files (recipes-core): In the directory recipes-core you
typically find recipes for binary images for the target hardware as well as
adaptations to other core recipes such as init-scripts, systemd, udev, and more.
- Display Support Files (recipes-graphics): In the directory recipes-graphics you find recipes related to display support if the target hardware of the BSP has specific graphics requirements. Typically, these are configuration recipes for the X11 server or the Wayland/Weston compositor.
Linux Kernel (recipes-kernel): The directory recipes-kernel and its
subdirectories contain recipes and configuration files pertaining to the Linux kernel.

## 10.4 Creating a Yocto Project BSP
If you are developing your own hardware, you want to create a Yocto Project BSP to
provide full support for it. Principally, you can take one of the following three approaches
for creating a Yocto Project BSP:
 - Create Manually: You can start by creating an empty layer using the yocto- layer script and then populate directories and files for your BSP manually.
Copy from Existing BSP Layer: If the hardware that your BSP targets is similar to hardware from another BSP, you can copy that layer and make adjustments to meet the requirements of your target hardware.
 - Use the Yocto Project BSP Tools: The Yocto Project provides a couple of tools that
simplify the task of creating a BSP. 
 - They are interactive and allow setting common BSP parameters by responding to a series of questions. The tools then create a skeleton for your BSP, and you then can fill in the missing details.
### 10.4.1 Yocto Project BSP Tools
- There are two tools to assist you with creating a Yocto Project BSP: yocto-bsp and
yocto-kernel. The former, you guessed it, assists in creating the BSP layer, and the
latter assists in configuring the Linux kernel. Both tools have several subcommands.
Invoking the tool without specifying the subcommand results in printing the help message
with the list of available subcommands.
Subcommand yocto-bsp list
The subcommand yocto-bsp list shows information only. Currently, that is
information on the supported kernel architectures:
```$ yocto-bsp list karch
Architectures available:
i386
mips64
arm
powerpc
mips
x86_64
qemu
```

```$ yocto-bsp list x86_64 properties
```























