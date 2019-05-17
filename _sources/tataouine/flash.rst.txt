.. _flash:

Flashing a new firmware
-----------------------

Multiple solutions exist to flash the firmware, some use open source tools and
others can use proprietary tools. We will focus here on using the
*openocd* open source tool.

After building the ``wookey.hex`` file, use the
following steps to flash it on the target board.

.. note::
   For now, this help targets the STM32F407 Discovery board

Connect the board to the host
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The board should be connected to the host via USB.
You should see something like this when running ``lsusb`` command ::

    Bus 001 Device 005: ID 0483:374b STMicroelectronics ST-LINK/V2.1

You can see the *vendor* and *product id* (here 0483 and 374b).

On Debian Stretch, the board is seen as the ``/dev/ttyACM0`` device.
You need some extra privileges to be able to access (read and write) that
device.
You may decide to be root (for example by using the ``sudo`` command)
but it is usually safer to add your current user in the `plugdev`.

Connect OpenOCD to the board
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The ``tools/`` directory contains some configuration files for STM32 Discovery boards.
To connect OpenOCD to the board, launch ``openocd`` command with the appropriate
configuration file: ::

   openocd -f tools/stm32f4disco1.cfg

Note also that the ``stm32f4disco1.cfg`` configuration file may fail.
If so, try with the ``stm32f4disco0.cfg`` file. A tip is to compare the *vendor*
and the *product id* returned by the ``lsusb`` command with the one found
in the configuration file.

Flashing the device
^^^^^^^^^^^^^^^^^^^

When OpenOCD is successfully connected to the board, you can interact with it
through its inner local server. This can be done with ``gdb`` or ``telnet``.
However, the simplest way to flash a new firmware is to use ``openocd``
command as describe below.

Using the telnet client
"""""""""""""""""""""""
Connect the server listening on *localhost:4444*: ::

   telnet localhost 4444

Then, it is possible to send some commands to the OpenOCD local server in order
to interact with the board. To flash the board, use the following
commands.

First, asks to the board to reset and to halt at startup, freezing
the core on its first instruction so that flashing it is safe: ::

   reset halt

Now you can flash the board with the new firmware: ::

   flash write_image erase build/armv7-m/32f407discovery/32f407discovery.hex

Then, you can reset and start the board: ::

   reset run

Using OpenOCD
"""""""""""""
It is possible to flash the firmware by using a subsequent configuration file in
parameter: ::

   openocd -f tools/stm32f4disco1.cfg -f tools/ocd_demo.cfg

The file ``tools/ocd_demo.cfg`` should contain: ::

   init
   reset halt
   flash write_image erase build/armv7-m/32f407discovery/32f407discovery.hex
   reset
   shutdown

Using GDB
"""""""""
GDB connects to OpenOCD through *localhost:3333* instead of *localhost:4444*: ::

   $ arm-eabi-gdb
   (gdb) target extended-remote 127.0.0.1:3333

For example, to debug the EwoK kernel: ::

   target extended-remote 127.0.0.1:3333
   mon reset halt
   symbol-file build/armv7-m/32f407discovery/kernel/kernel.fw1.elf
   b main
   c

Other ways
^^^^^^^^^^

You can also use the ST-link tool to flash the firmware, simply by using the
``st-flash`` tool of the st-link project.


