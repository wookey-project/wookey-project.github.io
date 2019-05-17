.. _lib_std:


.. highlight:: c

EwoK Standard library
=====================

.. contents::

Overview
--------

EwoK standard library is the EwoK microkernel userspace small libc
implementation, hosting:

   * The userspace syscall part
   * The various embedded-specific utility functions (such as registers
     manipulation helpers)
   * Some various basic functions for string manipulation, etc.


API
---

Libstd **does not aim** to be a POSIX compliant library. Nevertheless, for
functions that behave like POSIX ones, libstd try to keep the POSIX conformant
API.

Each function is described bellow.

.. toctree::
  :maxdepth: 1
  :name: stdtoc

   aprintf_flush <functions/aprintf_flush>
   aprintf <functions/aprintf>
   get_random <functions/get_random>
   get_reg_value <functions/get_reg_value>
   hexdump <functions/hexdump>
   htonl <functions/htonl>
   htons <functions/htons>
   memcmp <functions/memcmp>
   mutex_init <functions/mutex_init>
   mutex_lock <functions/mutex_lock>
   mutex_trylock <functions/mutex_trylock>
   mutex_unlock <functions/mutex_unlock>
   ntohl <functions/ntohl>
   ntohs <functions/ntohs>
   printf <functions/printf>
   queue_available_space <functions/queue_available_space>
   queue_create <functions/queue_create>
   queue_dequeue <functions/queue_dequeue>
   queue_enqueue <functions/queue_enqueue>
   queue_is_empty <functions/queue_is_empty>
   read_reg16_value <functions/read_reg16_value>
   read_reg_value <functions/read_reg_value>
   semaphore_init <functions/semaphore_init>
   semaphore_lock <functions/semaphore_lock>
   semaphore_release <functions/semaphore_release>
   semaphore_trylock <functions/semaphore_trylock>
   set_reg_value <functions/set_reg_value>
   snprintf <functions/snprintf>
   sprintf <functions/sprintf>
   strcmp <functions/strcmp>
   strcpy <functions/strcpy>
   strlen <functions/strlen>
   strncmp <functions/strncmp>
   strncpy <functions/strncpy>
   vprintf <functions/vprintf>
   vsnprintf <functions/vsnprintf>
   vsprintf <functions/vsprintf>
   wfree <functions/wfree>
   wmalloc_init <functions/wmalloc_init>
   wmalloc <functions/wmalloc>
   write_reg16_value <functions/write_reg16_value>
   write_reg_value <functions/write_reg_value>


FAQ
---

- **Are there helper functions to manipulate registers in userspace?**

Helper functions and macros have been written to access registers.
This API is in the libstd ``regutils.h`` header. Applications can include
this header directly in order to use it.


