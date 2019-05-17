.. _internals:

The build system internals
==========================

.. contents::


The WooKey build system is based on Makefiles and Kconfig. It uses specific GNU Make syntax (and hence
requires GNU Make).

Global Makefile hierarchy
--------------------------

Here is a list of the Makefiles in the project:

   * ./Makefile, manages the overall project build and the build dependencies at the project level
   * ./apps/Makefile, manages the build of the various applications, depending on the configuration (see Kconfig section)
   * ./apps/_appname_/Makefile, manages a given app build
   * ./drivers/Makefile, manages the drivers build
   * ./libs/Makefile, manages the libraries build
   * ./external/Makefile, manages the external projects build
   * ./doc/Makefile, builds the documentation
   * ./javacard/Makefile, builds the token Javacard applets

Other Makefiles (.objs, .conf, .gen) are included in theses Makefiles.

Integrating the Configuration set in Kconfig
---------------------------------------------

The configuration generated (through menuconfig) is hosted in the .config file. This file is
sourced and its variables are cleaned by the ./m_config.mk file. This Makefile also creates a minimal
configuration to support some targets when no .config file exists. This file can be hosted from any
Makefile in the project while the variable PROJ\_FILES exists and targets the project root directory.

Some targets are common to all apps (clean, distclean, all etc.) and are therefore hosted in the
root m_generic.mk (for generic) file. This file can be hosted from any Makefile in the project while
the variable PROJ\_FILES exists and targets the project root directory.

The pretty-printing build system
---------------------------------

Most of the build commands are executed silently (using classical "CC   ...", "LD    ...", etc.) pretty
printing. This pretty printing is managed using the standard Linux pretty printing support.
The command are called using::

   $(call if_changed,buildcmd)

or::

   $(call cmd,buildcmd)

syntax in the Makefile targets, where:

   * `buildcmd` is the name of the command to execute
   * `if_changed` is the macro to use when the command has to be executed if any requirements have changed
   * `cmd` is the macro to use when the command has to be always executed

The macros are written in m_build.mk file. This is the very same file as the Linux Kernel and
most other files and should not be modified.

The buildcmd is the name of the command, as defined in the m_build.mk file. This file does not have
to be included explicitly, as it is included by m_generic.mk.
The buildcmd corresponds to the command name without the "(quiet\_)\_cmd\_" string.

Here is an example of a classical compilation of object files from source files: ::

   %.o:%.c
   	$(call if_changed,cc_o_c)

When building in quiet mode, all commands are written in files named as the target, starting with a dot
and finishing with .cmd. As an illustration, the command used to build helpers.o is written in .helpers.o.cmd,
in the same directory as the object file.

To disable the quiet mode, just pass V=1 to the command line. All commands will be printed in the console.

Makefile.objs and configuration
--------------------------------

In order to support .config-based compilation, the activation of:

   * applications
   * SoC features
   * drivers and peripherals support

is made using Makefile.objs files.

In each Makefile.objs, the corresponding variable (app-y, drv-y, etc.) is filled based on the according
configuration variable set in the .config file.

Here is an example of such a Makefile.objs:

   drv-y :=

   drv-$(CONFIG_DRV_USR_USART) += usart/

Here, drv-y is first set to (null), and then, for each option:

   * If the option is set to y (this means that the corresponding KConfig option is "bool"), the driver dir is
     added to drv-y
   * If the option is set to n, the file is added to drv-n.

All Makefile.objs fulfill their variables. m_generic.mk then includes all Makefile.objs. As said above, this
inclusion can be done from any Makefile including m_generic.mk file, whatever its directory is, while PROJ\_FILES
variable exists.

.. FIXME
As a consequence, applications Makefile can now use the Makefile.objs variables to be built. Only their own sources
(being hosted in apps/_appname_/) are neither managed by Makefile.objs nor by the Kconfig mechanism.

By now, _varname_-n is not used, yet it exists if needed. The applications Makefile only use the _varname_-y var.


