strncpy
-------
Copy a substring from one string to another

Synopsys
^^^^^^^^

strcpy - copy a string

strcpy function copy a string from src to the buffer pointed by dest, including the final null byte.

strncpy function is similar to strcpy, unless it copies at most n characters (including the final null byte) from src to dest.
If the length of src is less than n, strncpy() complete dest with null bytes up to n chars.

strcpy respects the following prototype::

   #include "string.h"

   char *strncpy(char *dest, const char *src, uint32_t n);
   char *strcpy(char *dest, const char *src);

Conforming to
^^^^^^^^^^^^^

strcpy() and strncpy() are conform to POSIX-1-2001, C09, C99, SVr4 and 4.3BSD

The behavior of these functions in case of invalid parameters (i.e. one or more in dest and src is null) is considered as unpredictable in the POSIX implementation. In the libstd API, we have decided to sanitize correctly the input values.
