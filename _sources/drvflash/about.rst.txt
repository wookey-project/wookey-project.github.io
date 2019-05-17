About the flash driver
----------------------

Principles
""""""""""

About flash memory technology
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

A flash memory is made of *sectors* of various size. Each sector can be acceded independently and

Writing data to a flash memory sector can only be done by writing zeros (i.e. settings flash bits from 1 to 0). As a consequence, when writing data into a sector, the sector must be first reinitialized to a reset value, which correspond to resetting all the sector bits to 1. Once the sector reset is done, it is possible to set the requested bits to 0 to write the effective data in flash.

As a consequence, a usual flash write access is based on a successive sector reset and sector write sequence.

Flash banks and concurrent accesses
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Some Flash devices (like the STM32F4 one) can support multi-bank. This mechanism permit to separate the flash memory in two independent memory banks of the same size, both composed of multiple sectors.

Separating the flash into multiple banks permit to support write access into a given bank without blocking a potential concurrent read access on the other.

Such behavior is requested when updating the flash content at run time.

Flash devices specific storage areas
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Flash devices may support storage area with specific properties. On STM32F4 SoCs, the flash device support the following:

   * OTP (One Time Programmable) memory area, which can be written only once induring the device lifecycle. Such memory area can be used, for example, to store certificate files
   * System memory area, usually hosting the bootrom. This memory area is not writeable but is accessible through the flash device programmable interface.
