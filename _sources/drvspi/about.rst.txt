SPI bus and driver principle
----------------------------

Principles
""""""""""

A SPI interface is a full and half duplex synchronous communication interface widely used in embedded systems.
This communication bus is used for various onboard devices such as screens, touchpad, memories, and so on.

The STM32F4xx support up to 4 SPI interfaces (depending on the way the SoC GPIOs are configured).
SPI devices support direct and DMA-accelerated transfers.
