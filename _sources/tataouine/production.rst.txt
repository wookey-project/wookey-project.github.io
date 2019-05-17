.. _production:

Transition to production mode
=======================================

.. contents::

About the protections to activate in production mode
----------------------------------------------------

When everything is OK on the debug side, you can put the firmware in
**production mode**.

When developing on an open WooKey platform, two elements are
unlocked and are a **potential threat** that an attacker can
use to attack the platform:

  * JTAG/SWD interface: in open mode, the WooKey platform's debug interface (SWD) is left opened for flashing and debug purposes. Of course, such a SWD interface opens many possible attacks on the production firmware, and it **must be closed** in production mode.
  * JavaCard tokens default GlobalPlatform keys: the WooKey token(s) usually use default keys (depending on the exact model of JavaCard you use). The WooKey security model **imposes to modify the default keys of the token**. Leaving the default keys opens threats where it is possible to **compromise your sensitive keys** and **produce a rogue token**.

Beyond this, the WooKey platform is quite verbose on its UART in development mode, and there is no
reason to keep such a verbosity in production mode. Hence, it is strongly advised to **mute** the
UART in order to avoid any leak on this output channel.

Lock the platform JTAG/SWD
--------------------------

On STM32 MCUs, locking the JTAG/SWD debug and flashing interface makes use of ST's RDP (Readout Protection) technology (through internal fuses).
Three levels of RDP exist:

  * RDP0: this is the default level where everything is opened (the WooKey development platform is in this level)
  * RDP1: in this level, some (but not all) debug features are locked. It is not possible to read the internal flash form the SWD debug interface, but it is still possible to debug and interact with elements in the internal SRAM. This protection level must **not be considered** as secure since the SRAM might leak sensitive information! Moreover, this level is **reversible** (at the expense of erasing the internal flash).
  * RDP2: in this level, all the debug features and internal flash reading are locked. We strongly advise to use this protection mode in WooKey's production phase. This level is **irreversible** for obvious security reasons.

All these elements are summarized in ST's Application Note AN4701 that can be found `here <https://www.st.com/content/ccc/resource/technical/document/application_note/89/12/c5/e2/0d/0e/45/7f/DM00186528.pdf/files/DM00186528.pdf/jcr:content/translations/en.DM00186528.pdf>`_.

More practically, the transition to RDP2 can be performed on a board by using ST's `ST-LINK utility dedicated tool <https://www.st.com/content/ccc/resource/technical/document/user_manual/e6/10/d8/80/d6/1d/4a/f2/CD00262073.pdf/files/CD00262073.pdf/jcr:content/translations/en.CD00262073.pdf>`_.

See the **Option bytes commands**, specifically using ``-OB RDP=2``. This tool is a **Windows only** tool.

Unfortunately, there is no easy way under Linux to activate RDP2 using open source OpenOCD or st-util. You can still use these tools to **manually configure** the option bytes
of the MCU, but do it **at your own risks**.

.. danger::
  Please be sure of what you do before switching to RDP2. This is **irreversible**, you must ensure that you have properly configured and
  compiled you production firmware!


After RDP2 activation, the **only way** to update a WooKey platform is through the secure DFU (Device Firmware Update) procedure with
**signed and encrypted firmwares**.

Activate bootloader RDP2 detection
----------------------------------

As a defense-in-depth mechanism, WooKey's initial bootloader optionally checks for RDP2 protection activation
before booting the rest of the firmware. Of course, this check is disabled by default in development mode
(or the bootloader will refuse to boot since the default RDP value is RDP0 for dev boards).

When configuring the production firmware (i.e. you have ensured that everything is fine with your options), you can
activate in the ``menuconfig`` 'Bootloader configuration' menu the 'Check that RDP protection is active at boot time'
option. Next, compile your firmware and it should check for the protection activation: if you are still in RDP0, you should
see reboots.

.. warning::
  This feature is still under beta test review, please use with caution!


Lock the JavaCard GlobalPlatform tokens
---------------------------------------

Locking the JavaCard tokens must be performed after flashing them with the AUTH, DFU and SIG applets. When
locking the token, you must keep the new key(s) **somewhere safe**, as you might need them to flash
the tokens again in case of reconfiguration (for a new platform and so on).

.. danger::
  Please don't loose the new key when you lock your tokens, or they will be **bricked** and useless!


Changing the JavaCard default GlobalPlatform keys is important in WooKey's security model. Performing this
is easy using the GlobalPlatformPro tool:

https://github.com/martinpaljak/GlobalPlatformPro

with the ``-lock`` option.

.. warning::
  Note your new key somewhere, and keep it **safe**. You will need it for any new administrative operation
  on your tokens


Mute the debug UART
-------------------

We strongly advice to **mute** the debug console UART (i.e. the UART used by the kernel and for userland ``printf``). This can
be performed by selecting the 'No kernel serial interface (production mode)` in the 'Micro-Kernel configuration' menu.

Choosing this option renders the UART hardware IP completely non-configured, ensuring that no sensitive information is
leaked through it.

