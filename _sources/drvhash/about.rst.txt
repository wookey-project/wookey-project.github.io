About the Hash driver
---------------------

Principles
""""""""""

The Hash cryptographic coprocessor provides a hardware implementation of various hash algorithms to support hash computation of input data, as
well as HMAC variants of these algorithms.
This device is an autonomous device, using a dedicated memory mapping. On STM32F4xx devices, the HASH device support the following hash algorithms:

   * MD5
   * SHA1
   * SHA224 (with and without HMAC mode)
   * SHA256 (with and without HMAC mode)

