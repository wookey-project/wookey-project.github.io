Creating a new Application
--------------------------

.. contents::

What is a WooKey application?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

An application is a standalone software embedding all the needed content (apart from the application loader)
and built on top of the EwoK microkernel. Applications interact with EwoK through **syscalls**.

Usually, libraries (such as the standard library) wrap such syscalls exposing higher level helpers. Applications can
also interact with the hardware if they are allowed to (see the permissions model), and must initialize
devices using dedicated syscalls. Higher level helpers for hardware interactions are also provided to
the application in the form of userland drivers libraries (such as the USART library).

An application can use any of the SoCs, boards, cores and drivers sources to be able to run on the target.

The application source directory structure
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

A typical application named *myapp* is hosted in apps/myapp.

The application directory structure is composed of four main elements:

   * Its Makefile
   * Its configuration file (named Kconfig)
   * Its main.c source file (usually hosted in src/ relative directory)
   * a README.md file, describing the purpose of the application

And that should be all!

The myapp directory should look like this:

   ./src/main.c
   ./Kconfig
   ./Makefile
   ./README.md

Writing your first basic application
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The application Makefile
""""""""""""""""""""""""

The application Makefile allows to specify which library is needed by
the application. The Makefile is also the link between the SDK and the
application.

All applications Makefiles are nearly identical. Only the application's
dependencies (libs, drivers) and paths varies.

Here is a sample Makefile for a given application:

.. literalinclude:: ../samples/Makefile.app
    :linenos:
    :language: make

Inclusion path of libraries and drivers is implicitly added
by Tataouine through the APPS_CFLAGS variable.

As a consequence, including a library or a driver in the C code can be done by using the following syntax:

.. code-block:: c

   #include <api/headername.h>

The application layout is automatically generated.

It is important to respect the following variable names, as they are used by
the SDK and overloaded as input variables for advanced mechanisms such as multi-bank support:

   * APP_NAME
   * EXTRA_LDFLAGS

Some variables are set by the SDK (through the included m_config.mk file):

   * BUILD_DIR       : the build directory of the project
   * AFLAGS          : the architecture-specific compilation flags
   * APPS_CFLAGS     : libs and drivers inclusion flags and other
     arch-specific and warning flags
   * PROJ_FILES      : the project root directory path

.. caution::
   Please do not set these variables using **:=**, use instead **?=** for paths and **+=** for flags to support overloading, as shown in the sample Makefile


The application Kconfig
"""""""""""""""""""""""

Like all applications, yours must be configured. A typical example is the
permissions configuration, but also the required memory or stack. All these information can
be set using a Kconfig file (same syntax as the Linux kernel) which allows
the usage of ncurses or GTK-based configuration.

Like for Makefiles, applications Kconfig are nearly the same. Only the
application name varies.

.. hint::
   You can use the application Kconfig file to add other
   application-specific configuration elements if needed

80% of the Kconfig file is dedicated to the permissions.

Here is a sample Kconfig file.

.. literalinclude:: ../samples/Kconfig.app
    :linenos:
    :language: kconfig


Integrating your application to the Tataouine SDK
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

This is done by updating the manifest file to add your application repository.
The SDK automatically detects that your application is added to the apps/ subdirectory
and integrates it to the configuration subsystem.

Now, you only have to activate it using menuconfig, in the same way you
configure the Linux kernel, by executing::

   make menuconfig

Go to *Userspace drivers and features*, *Applications*. You should see your application
and should be able to activate it. Until your configuration is saved, you can now
directly compile and flash the new version of the firmware with your application
integrated in it.

.. warning::
   Please beware of the following limitations when adding new applications:
     * The number of available slots for applications is limited. Too much
       applications will induce a compilation error
     * The user is responsible of the memory ressource management of new applications (stack usage and so on).
       Some configuration errors here (for instance a stack too small for the app memory needs) will
       not be caught at compile time, and will lead to crashes (due to memory errors caught by the MPU)
       that can be hard to debug
     * Mistakes and inconsistencies when setting the applications permissions could lead to runtime
       hazardous errors!
     * The user must check the consistency of device drivers usage when adding new
       applications. For instance, if two applications both try to use USART4 concurrently,
       one of it will have its request refused by the kernel. As a general rule of thumb, sharing the
       same piece of hardware in two or more applications (i.e. registers handling the same peripheral) is
       not permitted and must be resolved by using a userspace proxy


