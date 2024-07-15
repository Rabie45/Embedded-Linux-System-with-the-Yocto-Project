# Ch9 Kernel Recipes
- the Linux kernel is modular. If you choose to compile a kernel module into the kernel, it cannot be unloaded
again during runtime. Some functionality, however, due to its technical nature, can
never be loaded during runtime.
- Kernel Footprint: Less functionality compiled into the kernel means a smaller
kernel that can be loaded quickly from storage media.
- Boot Time: A smaller kernel with fewer drivers compiled into the kernel means lessinitialization during kernel startup, which contributes to a faster boot time.
- Hardware Support at Startup: Because kernel modules are inserted into the Linux
kernel after the kernel has launched user space, all hardware support that needs to be
available during kernel boot, such as disks and eventually network hardware, must
be compiled into the kernel.
- Ability to Upgrade: Hardware drivers may need upgrades after the embedded
system has shipped.

## 9.1 Kernel Configuration
 - The Linux kernel provides its own configuration system, commonly called kconfig.
Kconfig is essentially a configuration database organized in a tree structure. All
configuration options are consolidated into a single file, .config

- The .config file is typically created automatically either from a default platform
configuration file or from an existing configuration for a particular system.
### 9.1.1 Menu Configuration
- If you have worked with the Linux kernel before and built it for a native environment, you
are most likely familiar with the commands make menuconfig, make xconfig, and
make gconfig.
- The Yocto Project’s kernel recipes provide the functionality of make menuconfig
by invoking
``` 
bitbake -c menuconfig virtual/kernel
```

### 9.1.2 Configuration Fragments
- you do not want to manually modify the kernel configuration with the menu
editor every time you rebuild the kernel. Configuration fragments are files that contain one or more lines of kernel configuration
settings as you find them in the .config file—for instance, CONFIG_SMP=n. You can
then simply add those files to SRC_URI of the kernel recipe.

## 9.2 Kernel Patches
- Applying patches to kernel sources with kernel recipes is no different from applying
patches with recipes for regular software packages.
- 1. Change to the kernel source directory. Change your working directory to the
kernel source directory.
``` 
bitbake -e virtual/kernel | grep STAGING_KERNEL_DIR
```
- Then use the output to change to the directory. Alternatively, you can use
```
bitbake -c devshell virtual/kernel
```
- 2. Add/modify kernel source files. For this example, we add a simple device driver to
the kernel. Edit/add the files, as follows:

```
drivers/misc/Kconfig (add to the end of the file):
config YP_DRIVER
tristate “Yocto Project Test Driver”
help
This driver does nothing but print a message.
drivers/misc/Makefile (add to the end of the file):
obj-$(CONFIG_YP_DRIVER) += yp-driver.o
drivers/misc/yp-driver.c (add new file):
#include <linux/module.h>
static int __init yocto_testmod_init(void)
{
pr_info(“Hello Kernel from the Yocto Project!”);
}
static void __exit yocto_testmod_exit(void)
{
pr_info(“Gone fishing. I’ll be back!”);
}
module_init(yocto_testmod_init);
module_exit(yocto_testmod_exit);
MODULE_AUTHOR(“Rudolf Streif <rudolf.streif@gmail.com”);
MODULE_DESCRIPTION(“Yocto Project Test Driver”);
MODULE_LICENSE(“GPL”);
```
- 3. Stage and commit changes. Yocto Project kernels are checked out from Git
repositories. Therefore, you can simply use Git to create the patch:

``` 
git status
git add .
git commit -m “Added Yocto Project Driver”
```
- 4. Create the patch file. Now use Git again to create from the top-level directory ofthe kernel sources to create the patch file
``` git format-patch -n HEAD^
```
which creates the file 0001-Added-Yocto-Project-Driver.patch file.
- 5. Move the patch file to your layer. Copy or move the patch file 0001-Added-
Yocto-Project-Driver.patch to the recipes-kernel/linux/files
directory of the layer we created in the previous step.
- 6. Create the configuration fragment. Since we are adding a new driver, we need to
enable it with a configuration fragment:
``` recipes-kernel/linux/files/yp-driver.cfg:
CONFIG_MISC_DEVICES=y
CONFIG_YP_DRIVER=y
```
- 7. Add the configuration fragment and patch to the recipe. Now we need to add
the configuration fragment and the patch to the recipe append file we created in the
previous step.
 
```recipes-kernel/linux/linux-yocto_3.19.bbappend:

FILESEXTRAPATHS_prepend := “${THISDIR}/files:”
SRC_URI += “file://smp.cfg”
SRC_URI += “file://yp-driver.cfg”
SRC_URL += “file://0001-Added-Yocto-Project-Driver.patch”
```
- 8. Build the kernel. Build the kernel with
Click here to view code image
``` bitbake -C fetch virtual/kernel 
```
Now you can verify the result by running QEMU and looking for the driver’s startup
message in dmesg. After logging on as root, execute
``` dmesg | grep “Hello Kernel”
```
### 9.3.1 Building from a Linux Kernel Tree
- Building from a Linux Kernel Tarball Building from a tarball is the classic way of building a Linux kernel. This method has been supported by the OpenEmbedded build system from the beginning. Many custom kernel recipes are still using this method


## 9.5 Device Tree
- device tree is a data structure describing a hardware platform. Rather than
hardcoding every detail of devices and their configuration, such as I/O addresses, memory
address space, interrupts, and more, into the kernel sources, a data structure is passed to
the kernel at boot time.

- The device tree compiler (DTC) compiles device trees from their
human-readable hierarchical format into a binary format commonly referred to as flattened
device tree (FTD).


















