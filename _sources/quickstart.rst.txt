.. _quickstart:

Quick start
===========

.. contents::

This section describes how to build and execute the ‘blinky’ demo
on a STM32F407 Discovery board. It summarises informations thoroughly detailed
here:

- :ref:`dependencies`
- :ref:`build`
- :ref:`flash`


Pre-requisites
--------------

Install the dependencies
^^^^^^^^^^^^^^^^^^^^^^^^
To compile and to run the project, some dependencies need to be installed.
Please check the :ref:`dependencies` section to fetch them.

Fetch the project files
^^^^^^^^^^^^^^^^^^^^^^^
Once the dependencies are installed, create a working directory and then fetch
the project with ``repo`` ::

   mkdir wookey
   cd wookey
   repo init -u https://github.com/wookey-project/manifest.git -m wookey.xml
   repo sync

For the discovery board, it is possible to test some basic apps with another
manifest file ::

   mkdir disco407
   cd disco407
   repo init -u https://github.com/wookey-project/manifest.git -m disco407.xml
   repo sync


.. info::
   These two manifest files concern the stable versions of the Wookey and Discovery
   boards projects. For the nightly built versions, you can use soft/wookey_nightly.xml and soft/disco407_nightly.xml respectively
   The next steps remain the same

.. danger::
   Nightly built versions are still unstable and are for developping purposes

Build the firmware
------------------

Set the environment
^^^^^^^^^^^^^^^^^^^
Set the needed environment variables by sourcing the ``setenv.sh`` script ::

   . setenv.sh

Configuration
^^^^^^^^^^^^^
List the predefined configuration profiles ::

   make defconfig_list

To configure the Wookey board firmware, please use one of the following ::

   make boards/wookey/configs/wookey2_graphic_ada_hs_defconfig
   make boards/wookey/configs/wookey2_production_defconfig

.. info::
   the graphic_ada_hs_defconfig is the debug firmware configuration file, as the production_defconfig is for production mode

.. hint::
   During the Wookey project development, various revisions of the hardware
   design have emerged. The current stable public version is refered to as wookey2.
   Nonetheless, we still provide config files and sources compatible with the previous revisions.
   Hence, if you use any hardware referenced upto wookey 1.4 on the board, use wookey prefixed defconfig instead (This will not be the case if you use the publicly available OpenHardwae design)

When using the Disco target, two demo examples are described in section :ref:`demo`.
To build the *blinky* project, choose the ``disco_blinky_ada_defconfig`` profile ::

   make boards/32f407disco/configs/disco_blinky_ada_defconfig

Build the binary files
^^^^^^^^^^^^^^^^^^^^^^
Then, you can launch the compilation ::

   make

Flash the firmware
------------------

Once the firmware is built, you can flash a target board using the procedure
described in :ref:`flash` ::

   openocd -f tools/stm32f4disco1.cfg -f tools/ocd.cfg

If you build the discovery board demo profile, use ::

   openocd -f tools/stm32f4disco1.cfg -f tools/ocd_demo.cfg

Execute the new firmware
------------------------

Press the black *reset button* to reset the board. You should see the
leds blinking. When the blue button is pressed, the blinking pattern changes.

Debug
-----

Read the USART with *minicom*
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
The demo examples are compiled with output traces sent to **USART1**.
This USART uses GPIOs **PB6** (TX, transmit) and **PB7** (RX, receive).
You can connect a USB-to-TTL converter to read those outputs from your host.
At least, you must connect the **PB6** pin to the receive part of the
USB-to-TTL converter.

On Linux, the device ``/dev/ttyUSB0`` should be automatically created after the
USB-to-TTL is succesfully connected.
Then, you can use ``minicom`` to read the messages from the USART and to have
some insight about what's happening ::

   minicom -c on -D /dev/ttyUSB0

Debug with *gdb*
^^^^^^^^^^^^^^^^
To debug the new firmware, launch ``arm-eabi-gdb`` ::

   arm-eabi-gdb

Then, inside GDB, execute the following commands ::

   target extended-remote 127.0.0.1:3333
   mon reset halt
   symbol-file build/armv7-m/32f407discovery/kernel/kernel.fw1.elf
   b main
   c

Going further
-------------
Read the :ref:`demo` section that roughly describes the user code
executed by the tasks. You can adapt it to run your own examples.

Troubleshooting
---------------
If you any problem while trying to build a new demo firmware, be sure that you
have carefully read the following:

- :ref:`dependencies`
- :ref:`build`
- :ref:`flash`
- :ref:`debug`

