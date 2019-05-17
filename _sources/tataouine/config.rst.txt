Configuration
=============

.. contents::

.. _configuration:

General architecture
--------------------

Tataouine SDK configuration is based on the Kconfig toolkit.
This toolkit has been originally used by the Linux kernel, but it is now
used by various embedded systems configuration, reducing their
complexity.

The Kconfig syntax is fully described in the Linux kernel documentation and can
be found directly on `kernel.org <https://www.kernel.org/doc/Documentation/kbuild/kconfig-language.txt>`_.
The only difference with the Linux kernel Kconfig usage if the *tristate* type,
which is not used in Tataouine as there is no such module notion.

.. hint::
   Do not hesitate to take a look at the existing Kconfig files to understand the
   Kconfig syntax

Tataouine configuration is structured in various sub-items:

   * General project configuration
   * EwoK kernel configuration
   * Userspace content configuration:
      * Userspace drivers configuration
      * Userspace libraries configuration
      * Applications configuration
   * Global compilation flags

