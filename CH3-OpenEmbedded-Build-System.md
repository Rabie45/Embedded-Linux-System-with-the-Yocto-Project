# 3. OpenEmbedded Build System
## 3.1 Building Open Source Software Packages
 - Some of the steps of this workflow you execute yourself, whereas others are typically carried out through some sort
of automation such as Make or other source-to-binary build systems.
 - Fetch: Obtain the source code. each open source project has its own URL to access its website, file servers,
and download areas. sources obtained from remote locations such as download sites or
repositories may be supplemented with patches and auxiliary files that are stored on a
local filesystem.
 - Extract: Unpack the source code.After the source code is downloaded, it must be unpacked and copied from its download
location to an area where you are going to build it.
 - Patch: Apply patches for bug fixes and added functionality. Patching is the process of incrementally modifying the source code by adding, deleting,
and changing the source files. applying bug and security fixes, adding functionality
 - Configure: Prepare the build process according to the environment. With variety
comes diversity requiring the build environment for the software package to be configured
appropriately for the target system. Accurate configuration is particularly important for
cross-build environments where the CPU architecture
 - Build: Compile and link. build binaries such as executable
program files and libraries as well as auxiliary files from source code.
 - Install: Copy binaries and auxiliary files to their target directories. The install step copies binaries, libraries, documentation, configuration, and other files to
the correct locations in the target’s filesystem. Program files are typically installed in
/usr/bin, for user programs, and /usr/sbin, for system administration programs.
Libraries are copied to /usr/lib and application-specific subdirectories inside
/usr/lib. Configuration files are commonly installed to /etc.
 - Package: Bundle(جمع) binaries and auxiliary files for installation on other systems. Packaging is the process of bundling the software, binaries, and auxiliary files into a
single archive file for distribution and direct installation on a target system

## 3.2 OpenEmbedded Workflow
![image](https://github.com/user-attachments/assets/5434a4cb-6356-4fa3-b698-50f860f06359)

- Metadata Files are subdivided into the categories configuration files and recipes.
  - Configuration Files Configuration files contain global build system settings in the form of simple variable
assignments. Recipes can also set and overwrite variables,
  - BitBake Master Configuration File (bitbake.conf) his file contains all the default configuration settings.
  - Layer Configuration (layer.conf) The OpenEmbedded build system uses layers to organize metadata. A layer is essentially a
hierarchy of directories and files. . Every layer has its own configuration file named
layer.conf.
  - Build Environment Layer Configuration (bblayers.conf) A build environment needs to tell BitBake what layers it requires for its build process.
  - Build Environment Configuration (local.conf) The local.conf file contains settings that apply to the particular build
environment, such as paths to download locations, build outputs, and other files
  - Distribution Configuration (<distribution-name>.conf) For the Poky reference
distribution, the default image name is also Poky A distribution is selected by setting the
variable DISTRO in the build environment’s local.conf file
  - Machine Configuration (<machine-name>.conf) capability to
strictly separate parts of the build process that are dependent on the particular hardware
system, the machine, and its architecture from the parts that do not depend on it.
  - Recipes form the core of the build system as they define the build workflow for
each software package. The recipes contain the instructions for BitBake on how to build a
particular software package BitBake recipes are identified by their .bb file extension.
- Workflow Process Steps
  - Source Fetching Recipes specify the locations of the source files by including their URIs in the
SRC_URI variable.
  - Source Unpacking and Patching they are extracted into
the local build environment.
  - Configure, Compile, and Install Although configuring, compiling, and installing are distinct steps in the build process,
they are typically addressed within the same class because all of them involve invoking
parts of the package’s own build system.
  - Image Creation The packages are installed from the package feeds
into a root filesystem staging area using the package management system.
## OpenEmbedded Build System Architecture 
 - Three base components make up the OpenEmbedded build system architecture:
  - Build system
  - Build environment
  - Metadata layers
    ![image](https://github.com/user-attachments/assets/fb798dca-8eb8-4c62-9cf2-a32f16d119d0)

 - Build System Structure
   - The directories starting with meta are all metadata layers:
   - meta: OE Core metadata layer
   - meta-hob: Metadata layer used by the Hob graphical user interface for BitBake
   - meta-selftest: Layer for testing BitBake that is used by the oe-selftest script
   - meta-skeleton: Template layer you can use to create your own layers
   - meta-yocto: Yocto Project distribution layer
   - meta-yocto-bsp: Yocto Project BSP layer
 - there is the subdirectory scripts, which contains a collection of integration and support scripts for working with Yocto Project builds. The most commonly used
scripts are as follows:bitbake-whatchanged: Lists all components that need to be rebuilt as a consequence of changes made to metadata between two builds
cleanup-workdir: Removes build directories of obsolete packages from a build
environment
    - create-recipe: Creates a recipe that works with BitBake
    - hob: Launches Hob, the graphical user interface for BitBake
    - runqemu: Launches the QEMU emulator
    - yocto-bsp: Creates a Yocto Project BSP layer
    - yocto-kernel: Configures Yocto Project kernel recipes inside a Yocto Project
    - BSP layer
    - yocto-layer: Creates a metadata layer that works with BitBake

All build output is placed into the tmp subdirectory
directory is organized into a variety of subdirectories:
- buildstats: This subdirectory stores build statistics organized by build target
and date/time stamp when the target was built.cache: When BitBake initially parses metadata, it resolves dependencies and
expressions. The results of the parsing process are written into a cache. As long as
the metadata has not changed, BitBake retrieves metadata information from this
cache on subsequent runs.
- deploy: The build output for deployment, such as target filesystem images,
package feeds, and licensing information, is contained in the deploy subdirectory.
- log: Here is where you can find the BitBake logging information created by the
cooker process.
- sstate-control: This subdirectory contains the manifest files for the shared
state cache organized by architecture/target and task.
- stamps: BitBake places completion tags and signature data for every task
organized by architecture/target and package name into this subdirectory.
- sysroots: This subdirectory contains root filesystems organized by
architecture/target. Contents includes a root filesystem for the build host containing
cross-tool-chain, QEMU, and many tools used during the build process.
- work: Inside this directory, BitBake creates subdirectories organized by
architecture/target where it builds the actual software packages.
- work-shared: This subdirectory is similar to work but is for shared software
packages.

 - 3.3.3 Metadata Layer Structure
   - Metadata layers are containers to group and organize recipes, classes, configuration files,
and other metadata into logical entities.
![image](https://github.com/user-attachments/assets/6a44b69c-5708-4caf-8906-e7f6236ce839)


