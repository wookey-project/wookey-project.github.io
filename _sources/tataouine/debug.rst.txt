.. _debug:

Debugging and logging
=====================

.. contents::

Activating the debug
--------------------

Configuring for debugging
^^^^^^^^^^^^^^^^^^^^^^^^^

In the kernel hacking section of the menuconfig, it is possible to set the debug level of the kernel.

.. image:: img/kconfig_root_kh0.png
   :alt: menuconfig root
   :align: center

.. image:: img/kconfig_root_kh.png
   :alt: menuconfig root
   :align: center

.. image:: img/kconfig_kh_dl.png
   :alt: menuconfig kernel hacking
   :align: center

It will add the ``-ggdb`` flags to GCC. There is no impact on
the target binary size, as the generated HEX file has no debug symbol.

WooKey serial console
^^^^^^^^^^^^^^^^^^^^^

The kernel is outputting its console in the USART configured in the kernel menu
of the menuconfig. On the WooKey board, USART1 and USART4 can be used. On
Discovery boards USART1 (using GPIOB6 and GPIOB7 connected to the host PC USB
through a TTL to USB connector) can be used.

Beware that with informational debug, the kernel output might be huge and
generate big latencies. This may have an impact on the userspace drivers
behavior.

Debugging userspace drivers and applications
--------------------------------------------

The *std library* supports a ``printf()`` function and prints
the content on the kernel USART through a syscall (see :ref:`lib_std`).

Using Minicom and GDB
---------------------
By default USART1 (configurable USART6 depending on the board) is used
as a kernel logging output.

.. hint::
   On the STM32F407, the USART1 uses the PB6 pin as its output TX,
   and PB7 as the RX input.

With gdb, you can load the ELF file of the userspace application
in order to easily manipulate its symbols::

   (gdb) symbol-file build/armv7-m/st32f407discovery/apps/myapp/myapp.elf
   (gdb) print myvar
   12
   (gdb) break myfunction
   (gdb) monitor reset halt
   (gdb) continue
   (gdb) Breakpoint 1: myfunction at main.c:28

