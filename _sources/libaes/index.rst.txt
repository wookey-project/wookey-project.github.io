.. _lib_aes:


.. highlight:: c

The AES library
===============

.. contents::

The libaes project aims at implementing variants of the AES (Advanced Encryption Standard)
algorithm.

This library supports:

   * Fully software based masked implementation (in assembly and designed to be protected against side
     channel attacks) - only AES-128 is supported here
   * Fully software based unprotected implementation in pure C
   * Hardware based implementation, based on an underlying hardware cryptographic
     coprocessor through an existing dedicated driver. The implementation supports
     both DMA based and polling based accelerations.

By now, the libaes supports the following key lengths:

   * AES-128
   * AES-192
   * AES-256

The libaes also supports the following AES modes:

   * AES ECB
   * AES CBC
   * AES CTR

.. hint::
  The library does not handle padding and supposes that all input data is 16 bytes aligned for ECB and
  CBC modes. This is not the case for CTR mode due to its inherent unaligned data tolerance


Overview
--------

Principles
""""""""""
AES implementations follow the NIST design principles that
can be found here:

https://nvlpubs.nist.gov/nistpubs/fips/nist.fips.197.pdf

The protected AES is an external project that can be found
here:

https://github.com/ANSSI-FR/SecAESSTM32

Limitations
"""""""""""

For now, the masked AES (protected against side channel attacks) only supports 128 bits.
Only ECB, CBC and CTR mode are supported. Future evolution might include other
modes.

No data padding is supported, and for ECB and CBC modes the input data is supposed to
be AES block size of 128 bits (16 bytes) aligned.

The libaes API
--------------

Initializing libaes
"""""""""""""""""""

Initializing libaes is done with the following API: ::

   #include "aes.h"

   enum aes_type {
       AES_SOFT_ANSSI_MASKED = 1,
       AES_HARD_NODMA = 2,
       AES_HARD_DMA = 3
   };

   enum aes_key_len {
       AES128 = 0,
       AES192 = 1,
       AES256 = 2
   };
   
   enum aes_mode {
       ECB = 0,
       CBC = 1,
       CTR = 2
   };
   
   enum aes_dir {
       AES_ENCRYPT = 0,
       AES_DECRYPT = 1
   };


   int aes_init(      aes_context        *aes_ctx,
                const unsigned char      *key,
                      enum aes_key_len    key_len,
                const unsigned char      *iv,
                      enum aes_mode       mode,
                      enum aes_dir        dir,
                      enum aes_type       type,
                      user_dma_handler_t  dma_in_complete,
                      user_dma_handler_t  dma_out_complete,
                      int                 dma_in_desc,
                      int                 dma_out_desc);

The AES initialization function does the following:

   * initialize the AES context, by:
      * setting the AES key and key length
      * setting the IV (in AES modes requiring IV)
      * setting the AES direction (encryption or direction)
      * setting the AES type (full software, full software masked, hardware with polling, hardware with DMA)
      * setting the DMA handlers and DMA kernel descriptors when using the AES_HARD_DMA hardware with DMA mode
      (descriptors that has been initialized with appropriate sys_init(INIT_DMA) syscalls)

.. hint::
  Multiple AES contexts can be initialized and used in parallel


.. danger::
  When the hardware AES is used and multiple contexts are used in parallel, the user
  MUST reinitialize the AES using an aes_init at each context switch. This is due
  to the fact that the underlying hardware loses its embedded keys when it is configured
  with new ones. It is the user responsibility to handle AES reinitializations at the
  upper layer

AES data (de/en)cryption
""""""""""""""""""""""""

Encryption and decryption are performed using the aes_exec core function: ::

    int aes_exec(aes_context * aes_ctx, const unsigned char *data_in,
        unsigned char *data_out, unsigned int data_len,
        int dma_in_desc, int dma_out_desc);

This function takes as input:

    * the AES context that has been initialized using aes_init
    * a pointer to the input data, and its size
    * a pointer to the output data 
    * DMA kernel descriptors to be used when AES_HARD_DMA mode is used (these descriptors must
    have been initialized with proper sys_init(INIT_DMA) syscalls)


For all the modes except the AES_HARD_DMA, aes_exec is blocking and only returns when
all the input data is processed and output data is filled with the result. When using
AES_HARD_DMA, aes_exec is non blocking and the DMA handlers must be used to check the
operation finalization and get the result in the output.

.. warning::
  When using polling and DMA, it is the user's responsibility to check any hardware related issue
  by accessing the underlying coprocessor status. For instance, any incomplete DMA transfer (due
  to a hardware error) must be handled by the user
