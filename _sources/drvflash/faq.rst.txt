flash driver FAQ
----------------

Is the Flash driver handling 1M and 2M flash memory ?
"""""""""""""""""""""""""""""""""""""""""""""""""""""

Yes. The flash memory size is defined in Kconfig and has to be set at configuration time.

Is the flash driver support mono-bank and dual-bank mode ?
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

Yes. The flash memory bank mode is defined in Kconfig and has to be set at configuration time.
All flash devices may not support both modes (for e.g. 2MB flash devices on STM32F4x9 only support dual bank mode.

Is there helper for RDP/WDP access ?
""""""""""""""""""""""""""""""""""""

No. The OPT registers have to be set manually by now.

I can't map all my devices ! Why ?
""""""""""""""""""""""""""""""""""

The MPU restriction may imply that you can't map all the devices in the same time. You have to handle successive map/unmap actions to serialize your various flash components mapping instead of trying to map all of them in the same time.
