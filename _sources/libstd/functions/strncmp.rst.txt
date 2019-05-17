strcmp
------
String comparison

Synopsys
^^^^^^^^

strcmp compare two (potentially NULL) strings and return a negative, null or positive value depending on the comparison.

The comparison algorithm is the following:

   * If both string are NULL, strcmp returns 0
   * If the s1 is NULL, strcmp returns -1
   * If the s2 is NULL, strcmp returns 1
   * If both string are non-NULL, the result depends on the first differenciated character:

       * If s1 char ASCII value is smaller than the second string one, strcmp returns a negative value
       * If s2 char ASCII value is smaller than the first string one, strcmp returns a positive value
       * If strings are equal (no difference found upto the leading \0), strcmp return 0

strncmp behaves like strcmp unless it compares up to len characters between the two string parameters.


strcmp respects the following prototype::

   #include "string.h"

   int strcmp(const char *s1, const char *s2);
   int strncmp(const char *s1, const char *s2, uint32_t len);

Caution
^^^^^^^

strcmp compare C (null-terminated) strings. strcmp arguments can be NULL but if not must finish with '\0'.

Conforming to
^^^^^^^^^^^^^

``strcmp()`` and ``strncmp()`` are conform to POSIX-1-2001, C09, C99, SVr4 and 4.3BSD

The behavior of these functions in case of invalid parameters (i.e. one or more in s1 and s2 is null) is considered as unpredictable in the POSIX implementation. In the libstd API, we have decided to sanitize correctly the input values.

