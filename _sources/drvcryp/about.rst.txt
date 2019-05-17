About the Cryp driver
---------------------

Principles
""""""""""

The STM32F4x9 cryptographic coprocessor support various symmetric algorithms,
that are configured by the Cryp driver during its initialization step.

The driver is also able to separate two independent roles:

   * The CRYP configurator, allowing key injection, but no (de)cryption requests
   * The CRYP user, allowing (de)cryption on a previously configured CRYP device by a CRYP injector, but refusing any key injection

These two roles permit to separate a (de)cryption data plane from a cryptographic control dataplane, which can be held in two independent tasks.

It is also possible to handle the overall CRYP manipulation in the same task.

The STM32F4x9 Cryptographic coprocessor supports the following algorithms:

   * 3DES (ECB mode)
   * 3DES (CBC mode)
   * DES (ECB mode)
   * DES (CBC mode)
   * AES (ECB mode)
   * AES (CBC mode)

For AES algorithm, the device support three key sizes:

   * 128 bits length
   * 192 bits length
   * 256 bits length

