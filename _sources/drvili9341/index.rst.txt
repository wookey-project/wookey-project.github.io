The ILI9341 driver
==================

.. highlight:: c


This is the ILI9341 Touchscreen TFT part implementation driver.

About the ILI8341 driver
------------------------

This device is a TFT screen peripheral connected through a SPI bus interface.
This driver aims to be at most SoC-independent and board independent.

The ILI9341 TFT driver API
--------------------------

Initializing the ILI9341 driver
"""""""""""""""""""""""""""""""

Initializing the ili9341 driver is done with the following API::

   #include "libtft.h"

   uint8_t tft_early_init(void);
   uint8_t tft_init(void);

About early init
^^^^^^^^^^^^^^^^

The ILI9341 driver early initialization declare the requested GPIOs directly
connected to the ILI9341 peripheral. These GPIOs are independent of the SPI
bus GPIOs, which are initialized by the SPI driver.

.. warning::
   The caller is responsible for declaring the SPI bus through which the peripheral is connected, using the SPI driver

About init
^^^^^^^^^^

The init step configure the GPIOs declared at early init phase and configure
the SPI bus previously configured by the caller. The SPI bus **must** be mapped.

Manipulating colors
"""""""""""""""""""

The screen is manipulated using background and foreground colors.
These color are set using the following API: ::

   #include "libtft.h"

   void tft_setfg(uint8_t r, uint8_t g, uint8_t b);
   void tft_setbg(uint8_t r, uint8_t g, uint8_t b);

Colors are encoded in RGB (Red, Green, Blue) values, from 0 (the darkest) to 255 (the lightest).

Colors can be updated regulary, mostly before filling rectlangles or writing text content onscreen.

Filling colored rectangles
""""""""""""""""""""""""""

.. hint::
   Here, like in other API using corrdinates, x and y are in pixels

Filling a colored rectangle area with a given color is done with the following API: ::

   #include "libtft.h"

   void tft_fill_rectangle(int x1, int x2,
                           int y1, int y2,
                           uint8_t r, uint8_t g, uint8_t b);

This API permit to fullfill a screen area starting from the coordinate x1-y1 to
x2-y2 with the RGB color based on the r,g,b tuple.

Writing text data
""""""""""""""""""

The ILI9341 device driver permits to write text content onscreen.

Set current cursor coordinate
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

When writing text data, the cursor position must be set. If not, it will take the
last known cursor position, or 0,0.

Setting the cursor position is done using the following API: ::

   #include "libtft.h"

   void tft_set_cursor_pos(int x,int y);


Writing text
^^^^^^^^^^^^

Writing text onscreen is done with the following API: ::

   #include "libtft.h"

   void tft_putc(char c);
   int  tft_puts(const char *s);

This two function update the current cursor position accordingly to the
argument length.

.. danger::
   The ILI9341 driver does not handle end of screen newline

.. hint::
   Take care to set foreground and background color if needed

Showing an image
^^^^^^^^^^^^^^^^

The ILI9341 driver support an abstraction for transfering image content to the
peripheral. This abstraction is based on RLE image compression format (see :ref:`lib_gui` documentation). Sending RLE image onscreen is done using the following API: ::

   #include "libtft.h"

   void tft_rle_image(int x, int y,
                      int width, int height,
                      const uint8_t *colormap,
                      const uint8_t *data,
                      int datalen);

The RLE image is printed starting at coordinate ``x`` and ``y``. The effective
size of the image is defined by its ``width`` and ``height``. The ``colormap`` (which hold a list of RGB colors), ``data`` (image content in RLE format) and ``datalen`` (holding the length in bytes of the ``data`` argument) define the overall
image content.

.. hint::
   The ``libgui`` provides a tool which permits to convert images to RLE source images


TODO: other API description
