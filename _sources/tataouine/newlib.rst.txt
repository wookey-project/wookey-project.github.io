.. _newlib:

Creating a new library
======================

.. contents::

An EwoK userspace library usually provides:

   * A protocol stack (SCSI, DFU, ISO7816, SD, etc.)
   * A toolkit (graphic user interface API, firmware handling API, etc.)
   * An independent algorithmic element (cryptographic algorithms such as AES
     or hash functions, etc.)

EwoK libraries can depend on each others and on drivers. Although, take care to
make them as portable as possible.

.. hint::
   Usually, using a library does not require any specific permission.
   On rare cases (e.g. for TRNG access) a permission can be requested.

Library source directory
------------------------

Libraries are in the ``libs/`` directory.
A library should be named wisely, depending on its content.

Sources integration
"""""""""""""""""""

A basic library requires the following files:

   * A ``Makefile``, holding the basic build target
   * A ``Kconfig`` file, to (en|dis)able the library
   * At least one source file
   * An ``api/`` directory, which contains the library API

By convention, the API file should be named using the library name.
For example, the API file of the *libsd* would be ``api/libsd.h``.

Library build mechanism
-----------------------

Library's Makefile
""""""""""""""""""

The ``Makefile`` are generic.
Only the ``LIB_NAME`` variable of the ``Makefile`` needs to be updated.

The ``Makefile`` supports the following targets:

   * ``all``: build the driver
   * ``doc``: build the doc if there is some
   * ``show``: show the drivers build info (sources, objects, etc.)
   * ``lib``: called by all target, build the driver

You should not need to take care about ``CFLAGS``, as theses are automatically
set. Although, it is possible to add any other compilation flag if needed.
A usual case is to add the ``-MMD -MP`` compilation flags to generate the sources dependency tree
or to add some explicit optimisation flags.

.. danger::
   Beware to use **CFLAGS +=** to keep previous ``CFLAGS`` contents

Library's build directory
"""""""""""""""""""""""""

Any driver is built in the ``APP_BUILD_DIR`` directory.
Objects files and libraries can be found in
``build/<arch>/<board>/libs/lib<name>``.

Configuring the library
"""""""""""""""""""""""

The library directory must hold a ``Kconfig`` file.
It must contain at least a library entry labelled by the string
``USR_DRV_<name>``. For example, the library *libsd* Kconfig file contains::

   config USR_LIB_SD
     bool  "userspace libsd for SDCard support"
     default y
     select USR_DRV_SDIO
     ---help---
     Support for SDCard hardware-independent protocol implementation.
     This lib depends on the sdio driver, which manage the HW IP.


A library, like other EwoK userspace components, can have various other
configuration items in this same file.


Integrating your library to the SDK
"""""""""""""""""""""""""""""""""""

Add your library *git* repository in the *repo* manifest file.
The SDK automatically detects that your library is added and integrates it to
the configuration subsystem.

Activate it using menuconfig ::

   make menuconfig

Go to *Userspace drivers and features, Libraries*.
You should see your library and should be able to activate it.

