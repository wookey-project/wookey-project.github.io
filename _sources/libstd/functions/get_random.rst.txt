get_random
----------
Random source accessor

Synopsis
^^^^^^^^

Get back some random content from the system entropy source.

.. caution::
   get_random() requires the PERM_RES_DEV_RNG permission to request random content from the KRNG entropy source

Description
^^^^^^^^^^^

get_random load random content from the system entropy source into a buffer

get_random returns:

   * MBED_ERROR_NONE if the RNG source fullfill the buffer, or:
   * MBED_ERROR_DENIED if the task is not authorized to request RNG source
   * MBED_ERROR_BUSY if the RNG source entropy is not ready
   * MBED_ERROR_INVPARAM if len is not 32bit aligned or the buffer is NULL.


get_random() has the following API::

   #include "api/random.h"

   mbed_error_t get_random(uint8_t *buffer, uint16_t len);
