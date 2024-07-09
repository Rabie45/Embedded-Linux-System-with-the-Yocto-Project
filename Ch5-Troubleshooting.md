# 5. Troubleshooting
- Troubleshooting failures of a complex build system can
be a daunting task. Failures can be rooted in many different areas of the build system: the
code in recipes and classes, configuration file settings, cross-development, the software
package to be built, packaging, and more.
- Having the right tools at hand and knowing how to use them effectively saves a lot of
time and effort for correctly locating and identifying the root cause of a problem.
## 5.1 Logging
 - BitBake logs all events occurring during the build process. The events logged by BitBake
are Debug statements inserted into executable metadataOutput from any command executed by tasks and other code Error messages emitted by any command executed by tasks and other code
 - General Log Files BitBake stores all general log files in
  ```LOG_DIR = “${TMPDIR}/log”```
 - Inside that directory, you find a subdirectory for each BitBake process, such as
cooker.
 - For example, if you are building for the qemux86 machine, the cooker subdirectory contains a qemux86 subdirectory that contains the actual log files.
 - BitBake names the log files using the timestamp at the time the process was started
 - important piece of information contained in the cooker log file is the build configuration, which tells you what settings this build used.
 - BB_VERSION: The BitBake version number.
 - BUILD_SYS: Type of the build system. The variable is defined in bitbake.conf as BUILD_SYS = "${BUILD_ARCH}${BUILD_VENDOR}-${BUILD_OS}". BUILD_ARCH contains the output of uname -m, BUILD_OS contains the outputof uname -s, and BUILD_VENDOR is a custom string that is commonly empty.
 - TARGET_SYS: Type of the target system.
 - MACHINE: The target machine BitBake is building for.
 - DISTRO: The name of the target distribution.
 - DISTRO_VERSION: The version of the target distribution.
 - TUNE_FEATURES: Tuning parameters for the target CPU architecture.
 - TARGET_FPU: Identification for the floating-point unit of the target architecture.
 ### 5.1.2 Using Logging Statements
  - One of the more commonly used, if not the most commonly used, methods for debugging
in programming is inserting debug messages into the code
  - BitBake provides several levels to indicate the severity of messages:
  - Plain: Logs the message exactly as passed without any additional information.
  - Debug: Logs the message prefixed with DEBUG:. The logging function also expects a numeric parameter between 1 and 3 indicating the debug level
  - Note: Logs the message prefixed with NOTE:. It is used to inform the user about a
condition or information to be aware of.
  - Warn: Logs the message prefixed with WARNING:. Warnings indicate problems
that eventually should be taken care of by the user; however, they do not cause a
build failure.
  - Error: Logs the message prefixed with ERROR:. Errors indicate problems that need
to be resolved to successfully complete the build.
  - Fatal: Logs the message prefixed with FATAL:. Fatal conditions cause BitBake to
halt the build process right after the message has been logged.
## 5.2 Task Execution
- the build process for any
software package pretty much follows these standard steps:
1. Fetch: Retrieve the package source code archives for download sites, or clone
source repositories as well as all applicable patches and other local files.
2. Unpack: Extract source code, patches, and other files from their archives.3. Patch: Apply the patches.
4. Configure: Prepare the sources for building within the target environment.
5. Build: Compile the sources, archive the objects into libraries, and/or link the objects
into executable programs.
6. Install: Copy binaries and auxiliary files to their target directories of an emulated
system environment.
7. Package: Create the installation packages, including any manifests, according to the
chosen package management systems.
### 5.2.1 Executing Specific Tasks
- Rerunning tasks individually allows you to make changes at any stage of the build
process and then execute only the task that is affected by the changes.
## 5.3 Analyzing Metadata
- The entire BitBake build process is driven by metadata. Executable metadata provide the
process steps, which are configured through values of the different variables. For
example, variables such as SRC_URI are defined by every recipe.
## 5.4 Development Shell
- bitbake <target> -c devshell
launches a terminal with a cross-build environment for target. The setup of the cross-
build environment exactly matches the one that Poky is using for its own builds.
## 5.5 Dependency Graphs
- To create dependency graphs for a target, invoke BitBake with the -g or --graphviz
options. Using
$ bitbake -g <target>
creates the following dependency files for target:
- pn-buildlist: This file is not a DOT file but contains the list of packages in reverse build order starting with the target.
- pn-depends.dot: Contains the package dependencies in a directed graph declaring the nodes first and then the edges.
- package-depends.dot: Essentially the same as pn-depends.dot but declares the edges for a node right after the node. This file may be easier to read by humans because it groups the edges ending on a node with the node.
- task-depends.dot: Declares the dependencies on the task level.
## 5.6 Debugging Layers
- ``` bitbake-layers help```
- help: Without any argument or by specifying help as argument, the tool shows a
list of the available commands. If you provide a command, it shows additional helpfor that command.
- show-layers: Displays a list of the layers used by the build environment together
with their path and priority.
- show-recipes: Displays a list of recipes in alphabetical order including the layer
providing it.
- show-overlayed: Displays a list of overlaid recipes. A recipe is overlaid if
another recipe with the same name exists in a different layer.
- show-appends: Displays a list of recipes with the files appending them. The
appending files are shown in the order they are applied.
- show-cross-depends: Displays a list of all recipes that are dependent on
metadata in other layers.



















