# CH7- Building a Custom Linux Distribution
## 7.1 Core Images—Linux Distribution Blueprints
- These images offer root filesystem configurations for typical Linux OS
stacks. They range from very basic images that just boot a device to a command-line
prompt to images that include the X Window System (X11) server and a graphical user
interface.
- All core image recipes inherit the core-image class, which itself inherits from image class.
### Let’s have a closer look at the various core images:
- core-image-minimal:Login and command-line interpreter are provided by
BusyBox.
- core-image-minimal-initramfs: This image is essentially the same as
core-image-minimal but with a Linux kernel that includes a RAM-based initial root filesystem (initramfs).
- core-image-minimal-mtdutils: this image also includes user space tools to interact with the memory technology device (MTD) subsystem in the Linux kernel to perform operations on flash memory
devices.
- core-image-minimal-dev: this image also includes all the development packages (header files, etc.) for all the packages installed in the root filesystem.
- core-image-rt: this image target builds the Yocto Project real-time kernel and includes a test suite and tools for real-time applications.
- core-image-rt-sdk:  this image includes the system development kit (SDK) consisting of the development packages for all packages installed;
- core-image-base: this image also includes middle-ware and application packages to support a variety of hardware such as WiFi, Bluetooth, sound, and serial ports.
- core-image-full-cmdline: This minimal image adds typical Linux
command-line tools—bash, acl, attr, grep, sed, tar, and many more—to the root filesystem.
- core-image-lsb: This image contains packages required for conformance with the Linux Standard Base (LSB) specification.
- core-image-lsb-dev: includes the development packages for all packages installed in the root
filesystem.
- core-image-lsb-sdk: this image includes development tools such as compilers, assemblers, and linkers as well as performance test tools and Linux kernel development packages.
- core-image-directfb: An image that uses DirectFB for graphics and input
device management,
- core-image-clutter: This is an X11-based image that also includes the
Clutter toolkit. Clutter is based on OpenGL and provides functionality for animated
graphical user interfaces.
- core-image-weston: This image uses Weston instead of X11. Weston is a
compositor that uses the Wayland protocol and implementation to exchange data
with its clients.
- qt4e-demo-image: This image launches a demo application for the embedded
Qt toolkit after completing the boot process.
- core-image-multilib-example: This image is an example of the support of
multiple libraries, typically 32-bit support on an otherwise 64-bit system.
- core-image-testmaster, core-image-testmaster-initramfs:
These images are references for testing other images on actual hardware devices or
in QEMU.
- build-appliance-image: This recipe creates the Yocto Project Build
Appliance virtual machine images that include everything needed for the Yocto
Project build system.
### 7.1.1 Extending a Core Image through Local Configuration
-  The simplest method for adding packages and package groups to images is to add
IMAGE_INSTALL ```IMAGE_INSTALL_append =  <package> <package group>```
```IMAGE_INSTALL:append this new syntex for kirkstone for old version IMAGE_INSTALL_append ```
- As we have seen, image recipes set the IMAGE_INSTALL variable adding packages
and package groups.
- you may wonder why we use the explicit _append operator instead of the += or .+ operators. Using the _append operator unconditionally appends the specified value to the IMAGE_INSTALL variable after all recipes and configuration files have been processed.
- IMAGE_INSTALL variable using the = or ?= operators, which may happen after BitBake processed the
settings in conf/local.conf.
- IMAGE_INSTALL_append_pn-core-image-minimal = ” strace” installs the strace tool only into the root filesystem of core-image-minimal. All other images are unaffected.
### 7.1.4 Extending a Core Image with a Recipe
- Adding packages and package groups to CORE_IMAGE_EXTRA_INSTALL and
IMAGE_INSTALL and in conf/local.conf may be straightforward and quick, but
doing so makes a project hard to maintain and complicates reuse. A better way is to extend
a predefined image through a recipe.
- A couple of things to consider when extending images with recipes: you need to provide the path relative to the layer for BitBake to find the recipe file to include, and you need to add the .bb file extension.
 - While you can use either include or require to include the recipe you are
extending, we recommend the use of require, since it causes BitBake to exit with
an explicit error message if it cannot locate the included recipe file.
 - Remember to use the += operator to add to IMAGE_INSTALL. Do not use = or := because they overwrite the content of the variable defined by the included recipe.
 ### 7.1.5 Image Features
 - Image features provide certain functionality that you can add to your target images. This
can be additional packages to be installed, modification of configuration files, and more.
 - IMAGE_FEATURES  used in image recipes to define the required set of image features.
 - EXTRA_IMAGE_FEATURES used in the conf/local.conf file
to define additional image features that, of course, then affect all images built with that
build environment.
 ### 7.3.5 Users, Groups, and Passwords
 - The class extrausers provides a comfortable mechanism for adding users and groups
to an image 

```
 inherit extrausers
 # set image root password
 ROOT_PASSWORD = “secret”
 DEV_PASSWORD = “hackme”
```















 

