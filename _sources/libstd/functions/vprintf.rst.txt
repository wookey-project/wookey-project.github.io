printf
------
formated output conversion

Synopsys
^^^^^^^^

This implementation of printf implement a subset of the POSIX.1-2001 and C99 standard API (as we are in an embedded system).

the stdio printf API uses variatic arguments. stdard printf API uses va_list arg for dynamic arguments list.

The printf familly respects the following prototype::

   #include "api/stdio.h"

   void printf(const char *fmt, ...);
   void sprintf(char *dest, const char *fmt, ...);
   void snprintf(char *dest, size_t len, const char *fmt, ...);

   #include "api/stdarg.h"

   void vprintf(const char *fmt, va_list args);
   void vsprintf(char *dest, const char *fmt, va_list args);
   void vsnprintf(char *dest, size_t len, const char *fmt, va_list args);


Description
^^^^^^^^^^^

Even if all the printf() fmt formating is not supported, the behavior of the
functions for the supported flags chars and lenght modifiers is conform to the
standard.
The following is supported:

+----------------+-----------------------------------------------------------+
| flag characters|                                                           |
+================+===========================================================+
| '0'            | the value should be zero-padded. This flag is allowed for |
|                | any numerical value, including pointer (p).               |
|                | This flag *must* be followed by a numerical, decimal value|
|                | specifying the size to which the value must be padded.    |
+----------------+-----------------------------------------------------------+

+-----------------+-----------------------------------------------------------+
| length modifiers|                                                           |
+=================+===========================================================+
| 'l'             | long int conversion                                       |
+-----------------+-----------------------------------------------------------+
| 'll'            | long long int conversion                                  |
+-----------------+-----------------------------------------------------------+
| 'd','i'         | integer signed value conversion                           |
+-----------------+-----------------------------------------------------------+
| 'u'             | unsigned integer value conversion                         |
+-----------------+-----------------------------------------------------------+
| 'h'             | short int conversion                                      |
+-----------------+-----------------------------------------------------------+
| 'hh'            | unsigned char conversion                                  |
+-----------------+-----------------------------------------------------------+
| 'x'             | hexadecimal value, mapped on a long integer conversion    |
+-----------------+-----------------------------------------------------------+
| 'o'             | octal value, mapped on a long integer conversion          |
+-----------------+-----------------------------------------------------------+
| 'p'             | pointer, word-sized unsigned integer, starting with 0x    |
|                 | and padded with 0 up to word-length size                  |
+-----------------+-----------------------------------------------------------+
| 'c'             | character value conversion                                |
+-----------------+-----------------------------------------------------------+
| 's'             | string conversion                                         |
+-----------------+-----------------------------------------------------------+

Other flags characters and length modifiers are not supported, generating an immediate stop of the fmt parsing.

Conforming to
^^^^^^^^^^^^^

POSIX.1-2001, POSIX.1-2008, C89, C99




