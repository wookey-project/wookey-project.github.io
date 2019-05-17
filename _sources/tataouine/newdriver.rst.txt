.. _newdrv:

Creating a new Driver
=====================

.. contents::

Drivers are built as userspace libraries.
Thoses libraries provide higher abstraction API that can be used by any task.

A driver should not depend on another driver.
The good practice is to implement standalone, autonomous drivers.
However, there might be some exception, e.g. iso7816 driver uses an USART
interface, requiring to use *libusart*.

A driver should not depend on another userspace library, the *libstd* being
the sole exception.

Driver source directories
-------------------------

Drivers can be hold in two places:

   * ``drivers/socs/<socname>`` for SoC-specific drivers
   * ``drivers/boards/<boardname>`` for board-specific drivers, usually
     external peripherals

*System on Chip* (SoC) drivers handle the implementation of internal hardware
devices belonging to a SoC.

Board drivers handle the implementation of any peripheral that is connected to the SoC through any
of its I/O lines. Some examples are a touchscreen or a SDcard reader.

.. note::
   For example, we want to develop a driver for the flash peripheral on a
   *stm32f439* board. That peripheral is not board specific, thus, its sources
   will be in ``drivers/socs/stm32f439/flash/``.

Sources integration
"""""""""""""""""""

A driver requires the following files:

   * A ``Makefile``, holding the basic build target
   * A ``Kconfig`` file, to (en|dis)able the driver
   * At least one source file
   * An ``api/`` directory, which contains the library API

By convention, the driver API file should be named using the driver name.
For example, the flash driver API file would be ``api/libflash.h``.


Build mechanism
---------------

Driver's Makefile
"""""""""""""""""

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


Driver's build directory
""""""""""""""""""""""""

Any driver is built in the ``APP_BUILD_DIR`` directory.
Objects files and libraries can be found in
``build/<arch>/<board>/drivers/lib<drivername>``.

Compilation command files are hold in files related to the object build with it.
For example, ``drivers/socs/stm32f439/flash/flash.c`` will produce
``build/<arch>/<board>/drivers/libflash.a``, build with
``build/<arch>/<board>/drivers/.libflash.a.cmd``.

Configuring the driver
""""""""""""""""""""""

The driver directory must hold a ``Kconfig file``.
It must contain at least a driver entry labelled by the string
``USR_DRV_<drvname>`` ::

   config USR_DRV_FLASH
     bool  "userspace Flash driver"
     default y
     ---help---
     Support for STM32F4 internal flash hardware IP.


A driver, like other EwoK userspace components, can have various other
configuration items in this file.

Integrating your driver to the SDK
""""""""""""""""""""""""""""""""""

Add your driver *git* repository in the *repo* manifest file.
The SDK automatically detects that your driver is added and integrates it to
the configuration subsystem.

Activate it using menuconfig ::

   make menuconfig

Go to *Userspace drivers and features, Drivers*. You should see your driver and
should be able to activate it.

Interacting with devices
------------------------

Getting device information
""""""""""""""""""""""""""

In Tataouine, the device list is handled through a unique JSON file,
``layout/arch/socs/<socname>/soc-devmap-<boardname>.json``.
This file hold all the necessary information for device drivers developers, including:

   * The **type**:  *block*, which means that the device is memory
     mapped in the SoC, or *peripheral*, which means that the device is
     onboard, accessed through an I/O bus
   * Memory mapping **address** (when the device is memory mapped)
   * Memory mapping **size** (when the device is memory mapped)
   * Associated **gpio** list. Each GPIO pin/port couple is
     associated to a canonical name. Only block devices communicating with the
     outside world have GPIOs
   * Associated **irq** list. Each IRQ is associated to a canonical name
   * Associated **dma** channels
   * EwoK **permission**. Those permissions are tested when the device is
     requested by a userspace task

Other fields (*RCC clocks* and *registers*) are used by the EwoK kernel to
enable the device input clock.

The JSON file is used to generate, for each device, some Ada and some C code
files.
All information about how devices header are generated and named can be found
in the :ref:`hardware layout chapter <layout>`.
The way this structure is used by the device driver is described below.

Declaring the device
""""""""""""""""""""

In EwoK, each task goes through two phases, more detailed in the :ref:`sys_init` section:

   * In the *init phase*, the task can declare a device (and other resources).
   * In the *nominal phase*, the task can access this device. In general,
     the first action will be to configure it.

As a consequence, initialization of a driver need to be separated in two parts:

   # In the *init phase*, the device is declared and registered against the kernel
   # In the *nominal phase*, the device is configured

Thus, the driver's API should provide two independent functions:

   * The declaration function, to register the device, named
     ``<device>_early_init()``, e.g. ``flash_early_init()``
   * The device configuration function, to configure the device, named
     ``<device>_init()``, e.g. ``flash_init()``

Trying to access the device during the *init phase* will trigger a memory fault
as the device is not mapped in memory.

For further details, read the :ref:`devices` and :ref:`ewok_kernel`.

.. hint::
   Beware to libstd ``mbed_error_t`` return type.

