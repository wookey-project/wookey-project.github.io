Bitiwse operations
------------------
Manipulate endianess

.. note:: The libstd's libARPA implementation is **not** a network stack
	  implementation but an implementation of the minimalist network bytes
          order encoding/decoding API.

Synopsis
^^^^^^^^

When writing a protocol stack that need to interact with the external word,
the information encoding (MSB, LSB) may not be the host encoding.

The host encoding varies, depending on the hardware architecture and sometimes
the system configuration (e.g. ARM, which support both MSB and LSB encoding).

To guarantee the correct encoding of numerical data, the POSIX standard has
defined an API historically defined for network communications.

The Libstd API implements this API and permits its usage for any encoding
conformance usage.

The endianess manipulation API is the following ::

   #include "api/arpa/inet.h"

   uint16_t htons(uint16_t hostshort);
   uint32_t htonl(uint32_t hostlong);
   uint16_t ntohs(uint16_t netshort);
   uint32_t ntohl(uint32_t netlong);

Description
^^^^^^^^^^^

``htons()`` and ``htonl()`` convert a short integer and a long integer into a
MSB encoded value.

``ntohs()`` and ``ntohl()`` convert a short integer and a long integer from a
MSB encoded value into the host endianess encoding.
