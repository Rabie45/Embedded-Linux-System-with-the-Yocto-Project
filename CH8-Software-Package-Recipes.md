# Ch8 Software Package Recipes
 ## 8.1 Recipe Layout and Conventions
  - 8.1.1 Recipe Filename
    - follow the convention <packagename>_<version>-<revision>.bb here packagename is the name of the software package the recipe builds.
    - The fields packagename, version, and revision of the recipe filename are assigned by BitBake to the variables PN, PV, and PR, respectively.
  - 8.1.2 Recipe Layout
    - Descriptive Metadata
      - SUMMARY: A one-line (up to 80 characters long), short description of the package.
      - DESCRIPTION: An extended (possibly multiple lines long), detailed description of the package and what it provides.
      - AUTHOR: Name and e-mail address of the author of the software package (not the recipe) in the form of AUTHOR = "Santa Claus <santa@northpole.com>". This can be a list of multiple authors.
      - HOMEPAGE: The URL, starting with http://, where the software package is hosted.
      - BUGTRACKER: The URL, starting with http://, to the project’s bug tracking system.
    - Package Manager Metadata
      - SECTION: The category the software package belongs to. Package management
tools use this category to organize the packages.
      - Priorities are utilized only by the Debian package manager dpkg and the Open Package Manager opkg. The priorities are standard: Packages that are standard for any Linux distribution, including a reasonably small but not too limited console-mode system.
      - required: Packages that are necessary for the proper function of the system
      - optional: Packages that are not necessary for a functional system but for a reasonably usable system
      - extra: Packages that may conflict with other packages from higher priorities or that have specialized requirements
    - Licensing Metadata 
      - LICENSE: The name of the license (or licenses) used for this software package. In most cases, only a single license applies, but some open source software packages employ multiple licenses.
      - LIC_FILES_CHECKSUM: This variable allows tracking changes to the license file
itself. The variable contains a space-delimited list of license files with their
respective checksums.
    - Inheritance Directives and Includes
    - Build Metadata
      - PROVIDES: Space-delimited list of one or more additional package names typically used for abstract provisioning.
      - DEPENDS: Space-delimited list of names of packages that must be built before this
package can be built.
      - PN: The package name. The value of this variable is derived by BitBake from the base name of the recipe file. For most packages, this is correct and sufficient. Some packages may need to adjust this value.
      - PV: The package version, which is derived by BitBake from the base name of the
recipe file.
      - PR: The package revision. The default revision is r0. In the past, BitBake required you to increase the revision every time the recipe itself has changed to trigger a rebuild.
      - SRC_URI: Space-delimited list of URIs to download source code, patches, and
other files from.
      - SRCDATE: The source code date. This variable applies only when sources are
retrieved from SCM systems.
      - S: The directory location in the build environment where the build system places the
unpacked source code.
      - The default location depends on the recipe name and version: ${WORKDIR}/${PN}-${PV}. The default location is appropriate for virtually all packages built from archives.
      - B: The directory location in the build environment where the build system places the
object created during the build. The default is the same as S
      - This variable is most commonly used for append files in the form FILESEXTRAPATHS_prepend := "${THISDIR}/${PN}", which causes the build system to first look for additional files in a subdirectory with the name of the package of the directory where the append file is located before looking in the other directories specified by FILESEXTRAPATHS.
      - LDFLAGS: Options passed to the linker. The default setting depends on what the
build system is building
     - Packaging Metadata:
      - PACKAGES: This variable is a space-delimited list of packages that are created
during the packaging process.
      - FILES: The FILES variable defines lists of directories and files that are placed into
a particular package.
      - PACKAGE_BEFORE_PN: This variable lets you easily add packages before the final
package name is created.
  - Runtime Metadata
This metadata section defines runtime dependencies.
    - RDEPENDS: A list of packages that this package depends on at runtime and that
must be installed for this package to function correctly.
    - RRECOMMENDS: Similar to RDEPENDS but indicates a weak dependency, as these
packages are not essential for the package being built.
    - RSUGGESTS: Similar to RRECOMMENDS but even weaker in the sense that package
managers do not install these packages even if they are available.
    - RPROVIDES: Package name alias list for runtime provisioning. The package’s own
name is always implicitly part of that list.
    - RCONFLICTS: List of names of conflicting packages.
    - RREPLACES: List of names of packages this package replaces.
## Writing a New Recipe
 - Skeleton Recipe
```
SUMMARY = ””
DESCRPTION = ””
AUTHOR = ””
HOMEPAGE = ””
BUGTRACKER = ””
```
 - BBFILES defines a search pattern for recipe files. It is typically set to
Click here to view code image
``` 
BBFILES += “${LAYERDIR}/recipes-*/*/*.bb \${LAYERDIR}/recipes-*/*/*.bbappend”
```
 - Fetch the Source Code recipe must provide the SRC_URI variable to tell the build system where to fetch the sources from and what protocol to use.
 ``` 
 SRC_URI = “http://ftp.gnu.org/gnu/hello/hello-2.9.tar.gz”
 ``` 
## Unpack the Source Code
 - The do_unpack task takes care of the unpacking. It can handle virtually all
common archiving and compression schemes.

## 8.3 Recipe Examples
```
helloprint.h:
void printHello(void);
helloprint.c:
#include <stdio.h>
#include “helloprint.h”
void printHello(void) {
printf(“Hello, World! My first Yocto Project recipe.\n”);
return;
}
hello.c:
#include “helloprint.h”
int main() {
printHello();
return(0);
}
```


The recipe:

```
SUMMARY = “Simple Hello World Application”
DESCRIPTION = “A test application to demonstrate how to create a recipe \
by directly compiling C files with BitBake.”

SECTION = “examples”
PRIORITY = “optional”

LICENSE = “MIT”
LIC_FILES_CHKSUM = “\
file://${COMMON_LICENSE_DIR}/MIT;md5=0835ade698e0bcf8506ecda2f7b4f302”

SRC_URI = “file://hello-1.0.tgz”
S = “${WORKDIR}”

do_compile(){
${CC} -c helloprint.c
${CC} -c hello.c
${CC} -o hello hello.o helloprint.o 
}

do_install() {
install -d ${D}${bindir}
install -m 0755 hello ${D}${bindir}
}
```
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
