The Flash driver
================

.. highlight:: c


This library is an implementation of the STM32F4 flash device driver.

It provide an abstraction of the flash device interactions through high level
API in order to read, write, (un)lock the flash memory.

The current implementation of this flash driver support various STM32 flash
intgrated flash devices components, including 1M and 2M flash banks, with both
signle and double banking.

.. toctree::
   :caption: Table of Contents
   :name: mastertoc
   :maxdepth: 2

   About the flash principle <about>
   The drvFlash API <api>
   FAQ <faq>


