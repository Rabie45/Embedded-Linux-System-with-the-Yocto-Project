# 12. Licensing and Compliance
## 12.1 Managing Licenses
- For any software product, it is common practice to include an End-User License
Agreement (EULA)
- The software product contains software components, such as libraries, from other
providers
- The software product is built using open source software packages
- The software product is provided together with hardware as part of an embedded
system
- on Android devices, you can access license information from
the Legal Information item in the About Device menu of the Settings app. License
information and text can be stored on the device, or alternatively, in particular for
connected devices, accessed through hypertext links directing the end user to a website
where the license information can be shown.
- Managing licenses for your product is not a trivial task. The Yocto Project includes over
170 common license schemes. The majority of them are open source licenses, but there
are also some commercial licenses included for some software packages.
- he Open Source Initiative (OSI) examines licenses
in a license review process for their compliance with the Open Source Definition. 1 OSI
lists about 70 open source licenses on its website that have passed the organization’s
license review process and are approved as compliant with the Open Source Definition.

- complicating the issue is that a single open source project can actually use
multiple license schemes. Following are common examples:

- The Yocto Project assists with managing licenses in several ways:( License tracking - Common licenses - Commercially-licensed packages - License deployment)

### 12.1.1 License Tracking
- All recipes must set the LICENSE variable to a list of source licenses that apply to the
software package the recipe builds. The latter is typically specified in the LICENSE file of the layer.
- If there is a choice of licenses, separate the license designations with the pipe (|)
symbol: for instance, LICENSE = "LGPLv2.1+ | GPLv3".
If multiple licenses cover different parts of the package sources, separate the license
designations with the ampersand (&) symbol: for instance, LICENSE = "MPLv2
& LGPLv2.1".
- Unless LICENSE is set to the special license designation CLOSED (LICENSE =
"CLOSED"), a recipe must also set the variable LIC_FILES_CHKSUM to enable license
tracking.

### 12.1.2 Common Licenses
- This directory is referenced by the variable COMMON_LICENSE_DIR. You can use this variable and the common license filename for LIC_FILES_CHKSUM, such as
```
LIC_FILES_CHKSUM = “\
file://${COMMON_LICENSE_DIR}/GPL-2.0;md5=801f80980d171dd6425610833a22dbe6”
```

### 12.1.3 Commercially Licensed Packages
- Recipes building such software packages flag the special licensing requirements by setting the
variable LICENSE_FLAGS: for example, LICENSE_FLAGS = "commercial".

- To enable a particular license flag, you add it to the LICENSE_FLAGS_WHITELIST
variable. The variable contains a space-delimited list of license flags.

### 12.1.4 License Deployment
- When building a target, the build system places the license information into the
${TMPDIR}/deploy/licenses directory inside the build environment.
- ``` INCOMPATIBLE_LICENSE = “GPL-3.0 LGPL-3.0 AGPL-3.0” ```
effectively excludes all packages licensed under these licenses from the build, unless there
are license alternatives.

### 12.1.6 Providing License Manifest and Texts
- A common requirement for open source licenses is that you have to provide license
information. Using
```
COPY_LIC_MANIFEST = “1”
```

###12.2 Managing Source Code

- Providing the source code is part of the compliance management activities you have to perform, and you should consider the necessary tasks before you create the final image for your target
system.

- The size of the entire download directory can be rather large. It also contains the
sources for packages that you typically do not deploy in a released image, such as
the toolchain sources. There are also files in the download directory that you never
deploy, namely the *.done files that indicate whether a source package has
successfully been downloaded.
- The download directory does not contain any patches that are provided with a
recipe.

- A better way of providing source code is offered by the archiver class which gives
you flexibility to control what source code you want to provide and in what form you want
to provide it You enable the source archiving with the archiver class by adding the
class to the INHERIT variable to the conf/local.conf file in your build
environment:
```INHERIT += “archiver”
```
# 13. Advanced Topics
In this chapter, we discuss select topics that facilitate using the Yocto Project in team and
production environments. One of the strengths of the Yocto Project build system is that it
can be easily deployed on individual development systems of software and build
engineers.

## 13.1 Toaster
- Toaster is a graphical user interface to the build system. Unlike Hob, which is a native
application, Toaster is a web interface accessed via web browser. Because it is a web
interface, Toaster is suitable for deployment on remote systems in build farms or cloud
services.
- In the following sections, we discuss the two Toaster operational modes, setup for local
development in both modes, Toaster configuration, and setup of a production build system
with Toaster.

###  13.1.1 Toaster Operational Modes
- Analysis Mode In Analysis mode, Toaster attaches to an existing build environment that you have
previously created with oe-init-build-env.  
- Toaster then collects build statistics and other information, stores them in the database, and makes them available for browsing and viewing through its web interface. You need to start Toaster first before launching your build through BitBake.

- Toaster provides the following functionality:
  - Detailed information on the image that was built including recipes and packages 
  - Manifest of what packages were installed into the image Ability to browse the directory structure of the image
  - Build configuration including variable settings
  - Examination of error, warning, and log messages to facilitate debugging
  - Information on task execution and shared state usage
  - Dependency explorer
  - Performance information such as build time, time per task, CPU usage, and more.
  
- Build Mode Toaster creates build environments and manages the configuration,
BitBake execution, and data collection and analysis tasks of the Analysis mode. You
interact with Toaster only through its web interface. You can select your image, configure
the target machine and other aspects of builds, and start builds from the Toaster interface.
You do not interact directly with BitBake as you do when Toaster is in Analysis mode.

- Toaster provides the following functionality in addition to the functionality of Analysis mode:
Browse layers and add layers to the build configuration.
- Select target images, target machine, and distribution policies.
- Inspect and set configuration variables.
- Execute builds.
### 13.1.2 Toaster Setup
- Installing the Toaster Requirements
```pip3 install -r bitbake/toaster-requirements.txt
```
### 13.1.3 Local Toaster Development
- In local deployment mode, Toaster uses Django’s built-in web server rather than
integrating with an external web server, and it uses SQLite instead of an RDBMS. That
greatly simplifies installation and configuration. However, it does not scale for workgroup
use and remote deployment. For a scalable deployment, you want to consider using a
Toaster production setup
- Local Toaster Development in Build Mode
```source oe-init-build-env build2
source toaster start webport=0.0.0.0:8400
```
### 13.1.4 Toaster Configuration
- Toaster operation can be configured and administrated through command-line options,
environment variables, and the Django administrative user interface.
Setting a Different Port
By default, Toaster listens on port 8000 on all network interfaces of your build host. To
change the port, start Toaster in Build mode with the webport argument, as follows:

``` /home/myname/poky/bitbake/bin/toaster webport=5000
```

- Alternatively, you can start Toaster in Analysis mode with the webport argument:

```source /home/myname/poky/bitbake/bin/toaster webport=5000
```

- Setting the Toaster Directory for Build Mode
- Toaster stores the build environment and clones of additional layers from remote repositories in a directory defined by the environment variable TOASTER_DIR. Inside that directory, Toaster creates the directory build, which contains the build environment; the directory _toaster_clones, which contains the cloned layers; and the database toaster.sqlite, which stores the configuration and build data. By default, TOASTER_DIR is set to the current directory from where Toaster is started.
- Administrating the Django Framework
- The command launches the Django administrative utility, which first prompts you for the
following items:
  - User name for the superuser (mandatory)
  - E-mail address (optional)
  - Password (required; you have to enter it twice for verification)
  
### 13.1.6 Toaster Web User Interface

- The Toaster web user interface provides the following functionality:
  - Project Management: Create, configure, and view Toaster projects. A Toaster project is similar to a build environment that you create from the command line by sourcing oe-init-build-env
  - Build Configuration: Within a Toaster project, you can configure machine,
distribution, and other settings as you would by editing the conf/local.conf
file of a build environment. 
  - The Toaster user interface provides direct access to
common configuration variables, such as DISTRO, IMAGE_FSTYPES,
IMAGE_INSTALL_append, PACKAGE_CLASSES, and SDKMACHINE.
  -  You can add other variables as you wish. However, some variables are precluded. These are
variables that affect the configuration of the build host and variables that set paths to
where build artifacts are stored such as SSTATE_DIR and TMPDIR.

  - Layer Management: The Toaster user interface allows you to add and remove
layers to your project.
  - You can also browse a list of available layers. The three layers
meta, yocto, and yocto-bsp are by default included in a project and are
checked out automatically from the Poky repository. 
  - Information about layers from the OpenEmbedded Layer Index is obtained directly from the web and shown in the Toaster user interface.
  - You can add those layers to your project by a simple click of abutton. These layers are checked out from the OpenEmbedded Layer repositories on demand.
  - you can import your own layers from Git repositories.
  - You need to ensure that the layers you are importing are compatible with the Yocto
Project release you have chosen for your project.
  - Image Targets: Toaster identifies and lists the image targets from the various
available layers. 
  - If an image target is available from a layer that is already included
with your build configuration, then you can build it directly by clicking a Build
recipe button next to the image target. 
  - click the Add layer button to add the layer to your build configuration. If a layer is dependent on other layers, Toaster informs you about the dependencies and includes them automatically.
  - Package Recipes: Toaster maintains a list of all recipes of all layers, whether
already included with your build or not.
  - A search function assists in finding a particular recipe. For example, entering jdk into the search bar lists all recipes that provide the Java JDK. 
  - With the click of a button, you can add the layer containing
the recipe and build it. 
  - However, building a recipe does not automatically add it to IMAGE_INSTALL. 
  - You have to do this explicitly by editing the IMAGE_INSTALL_append variable from the BitBake variables screen.
  - Build Log: You can directly view and examine trace, warning, and error messages
from the Toaster user interface. You can also download the build log from Toaster to
your local machine.
  - Build Statistics and Performance Information: Toaster collects build statistics
such as overall build time, time per task, CPU usage, and disk I/O.
  - Image Information: Toaster collects and presents information on what software
packages have been built and included with your image. 
  - You can browse the structure of your image from the Toaster user interface and view dependency relationships between recipes and packages.
  
### 13.4 Autobuilder
- The Yocto Project Autobuilder is an automated build system based on the open source
continuous integration framework Buildbot. Buildbot is an extensible framework for
automating software builds, quality assurance, and release processes.
- The Yocto Project build team has packaged Autobuilder with setup and execution
scripts, which make it a matter of minutes to get an instance of Autobuilder running on
your own system.

### 13.4.1 Installing Autobuilder
- A basic installation and configuration of Autobuilder with one controller and one worker
running on the same host can be done in three simple steps:

``` 
git clone git://yoctoproject.org/yocto-autobuilder
cd yocto-autobuilder
source yocto-autobuilder-setup
```
- which you should note:
  - Client–Server Password: This is the password that workers use to identify
themselves with the controller.
  - User Name and Password: The script creates a user name and password for the
web user interface and stores them in the file yocto-
autobuilder/.htpasswd.














