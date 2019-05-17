.. _dependencies:

Dependencies
============

Some tools need to be installed in order to build your first firmware.

.. contents::


To fetch and to manage the project
----------------------------------

Repo
^^^^
The WooKey project deployment depends on repo, a tool made by Google to manage
the Android projects. It allows the support of various profiles and various
end-user applications, drivers and libraries without requiring complex
modification of the SDK.

Installing repo is describe in the 'Install Repo' section on
`this page <https://source.android.com/setup/build/downloading>`_.

There is also a
`repo documentation <https://source.android.com/setup/develop/repo>`_

On Debian Buster and higher, this software is packaged under the name *repo*: ::

   apt install repo

Git
^^^
*Git* distributed version-control system.
On Debian Buster and higher, this software is packaged under the name *git*: ::

   apt install git

To compile the firmware
-----------------------

Perl
^^^^
The SDK uses some Perl scripts. It should already be installed on your station.

A Kconfig parser
^^^^^^^^^^^^^^^^
Various tools for parsing and managing *Kconfig* files exist.
It is possible to use the Python library *kconfiglib*: ::

   pip3 install kconfiglib


It is also possible to use the *kconfig-frontends*, downloadable from its
developer's `website <http://ymorin.is-a-geek.org/download/kconfig-frontends/>`_: ::

   wget http://ymorin.is-a-geek.org/download/kconfig-frontends/kconfig-frontends-4.11.0.1.tar.bz2
   cd kconfig-frontend-upstream-latest
   ./configure
   make
   make install

On Debian Buster and higher, this software is packaged under the name
*kconfig-frontends*: ::

   apt install kconfig-frontends

By overloading the ``KCONF`` variable (see :ref:`build`), you can use an other
Kconfig parsers.

GNU make
^^^^^^^^
Beware that Mac-OS uses the *BSD make*, which is not compatible: ::

   apt install make

C cross-compiler for *ARMv7-m*
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Beware to use a *none-eabi* compiler and not a *gnu-eabi* compiler.
On Debian, it is provided by the *gcc-arm-none-eabi* package: ::

   apt install gcc-arm-none-eabi

You might also need to install *binutils* for *ARMv7-m*: ::

   apt install binutils-arm-none-eabi

AdaCore Ada/SPARK
^^^^^^^^^^^^^^^^^
The binaries for cross-compiling ARM binaries can be downloaded here:
https://www.adacore.com/download/more.

For cross-compiling ARM binaries from Linux x86, you should
select the ``ARM ELF (32 bits)(hosted on Linux64)``.

We recommend you to install the binaries in ``/opt/adacore-arm-eabi``.
If you install the binaries in another directory, you will have to set
the ``ADA_RUNTIME`` variable from the ``setenv.sh`` file (see :ref:`build`).

Also, remember to set your PATH environment variable: ::

    export PATH="/opt/adacore-arm-eabi/bin:$PATH"

To run 32-bit binaries, you will need to install *glibc* 32-bit version. On
Debian: ::

   apt install libc6-i386

Python-IntelHex
^^^^^^^^^^^^^^^

The *IntelHex* Python module is needed to generated *.hex* and *.bin* files.
On any system having python and ``pip`` installed, just run: ::

   pip install IntelHex

.. warning:: Use ``pip`` and not ``pip3``!


To flash the firmware on the target board
-----------------------------------------
To flash the newly compiled firmwares on STM32 based microcontrollers and the
associated development boards, you can use one of these two open source
utilities:

   * OpenOCD, which is packaged in various distributions and allows to interact
     with the target
   * ST-link (the open source version can be found on Github):
     https://github.com/texane/stlink.git)

On Debian, *openocd* package is available: ::

   apt install openocd

Note that *openocd* and *st-link* can also be used to debug the platform by
connecting *gdb-arm-none-eabi* or *gdb-multiarch*.

Note also that the ST-Micro proprietary software also works on Windows, or you
can use any software able to communicate with the STLinkv2 JTAG interface.


To compile the documentation
----------------------------
To generate the whole documentation, the following utilities need to be installed:

- *Sphinx*
- *Imagemagick*
- *rst2man*, which is part of the *python-docutils* package on Debian.

On Debian: ::

   apt install python-sphinx
   apt install imagemagick
   apt install python-docutils
   apt install texlive-pictures
   apt install texlive-latex-extra
   apt install texlive-fonts-recommended
   apt install latexmk
   apt install ghostscript


Cryptographic tools
-------------------

.. warning:: Cryptographic packages are required only for the whole WooKey project, but
             not for the demo examples

In order to sign and generate keys for firmwares, python cryptographic modules
are needed. The SDK is using the  *python-pyscard* tool for smart card
interaction and *python-crypto* in order to handle AES cryptographic content.

On Debian: ::

   apt install python-pyscard
   apt install python-crypto


To compile the Java applets
---------------------------

The WooKey project is based on a secure element holding applets. The applets sources
must be compiled using the Java and Javacard environment.

On Debian: ::

   apt install openjdk-11-jdk
   apt install maven
   apt install ant

The Javacard-specific toolkit is not a part of the Debian project. Oracle also does
not deliver any Javacard environment for GNU/Linux. Although, these JDK can be
downloaded from the following github repository:

https://github.com/martinpaljak/oracle_javacard_sdks.git

This repository holds all the Javacard SDKs an can be hosted typically in ``/opt``.

.. warning:: Update the setenv.sh JAVA_SC_SDK variable with the path of the SDK you
             wish to use

.. note:: When using the JCOP J3D081 Javacard (see :ref:`javacard`), use the SDK 3.0.3

.. danger:: Building the external tools requires at least *openjdk*, *maven*
            and *ant*.

