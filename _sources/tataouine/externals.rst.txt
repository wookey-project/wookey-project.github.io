External dependencies
=====================

.. contents::

Beyond the tools and SDK dependencies listed in :ref:`dependencies`, the WooKey project uses various external
open source projects that are integrated either to the SDK, to the firmware or to
the tokens Javacard applets.

All the external dependencies are provided by the Tataouine SDK in the 'externals'
folder of the project.

libecc: an elliptic curves cryptography library
-----------------------------------------------

The ECC library libecc is used in the WooKey project for handling ECDSA
signatures (Elliptic Curve based Digital Signature Algorithm),
ephemeral ECDH (Elliptic Curve based Diffie-Hellman) as well as hash
algorithms (SHA-256 and SHA-512).

The project can be found here:

https://github.com/ANSSI-FR/libecc

SecAESSTM32: a masked AES implementation resilient against SCA
--------------------------------------------------------------

During some sensitive operations such as secure channels with
the tokens, we have chosen to use an AES algorithm resilient against
side channel attacks (or SCA). The implementation provided here:

https://github.com/ANSSI-FR/SecAESSTM32

is in assembly and well suited for our Cortex-M4 STM32F4 MCU.

Resilience against SCA is part of the defense-in-depth approach of the
WooKey project.

ant-javacard and GlobalPlatformPro: a cross-platform Javacard framework
-----------------------------------------------------------------------

When it comes to Javacard development in an open source environment,
ant-javacard and GlobalPlatformPro are the most efficient solutions
that we have come across. These solutions are self-contained and
their command line 'Do What I Mean' approach is clean and
simple.

ant-javacard is used to compile the Javacard applets for the WooKey
Tokens, it can be found here:

https://github.com/martinpaljak/ant-javacard

The Oracle Javacard SDKs that can be hard to find from official sources are
also provided by the authors here:

https://github.com/martinpaljak/oracle_javacard_sdks

GlobalPlatformPro is used to 'push' the compiled Javacard applets on the
smart card tokens, as well as to manage the life-cycle of such tokens
(e.g. lock the tokens and change the default keys to production ones).
The project sources are here:

https://github.com/martinpaljak/GlobalPlatformPro

.. _externaldeps:


