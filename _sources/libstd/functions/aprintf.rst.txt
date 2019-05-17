aprintf
-------
Asynchronous pretty printing

Synopsys
^^^^^^^^

The *aprintf* functions familly is an asynchronous implementation of the printf
function, which permits to specific embedded components such as ISR handlers to
pretty print content without requesting kernel scheduling and blocking
execution.

The printing phase write content in a ring buffer and the flush phase is
handled voluntary, using aprintf_flush() function.

.. caution::
   aprintf_flush() is a blocking function and can't be executed in ISR mode

``aprintf()`` is based on a ring buffer, and as is, can't print a big amount of
content between two flushes.

Usage
^^^^^

The aprintf API respects the following prototypes::

   #include "api/print.h"

   void aprintf(const char*fmt, ...);
   void aprintf_flush(void);
