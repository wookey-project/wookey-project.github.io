Bitiwse and register operations
-------------------------------
Manipulating registers and bitfields

Synopsis
^^^^^^^^

When writing a driver, a part of the implementation consists in manipulating
device's registers. The C language is not a pleasant language for such
manipulation and mostly rely on preprocessing.

The libstd embed part implement various generic helpers to manipulate register
fields without requiring explicit bitwise operation.

The register manipulation helpers respect the following API::

   #include "api/regutils.h"

   uint32_t read_reg_value(volatile uint32_t * reg);
   void     write_reg_value(volatile uint32_t * reg, uint32_t value);

   uint32_t get_reg_value(volatile const uint32_t * reg, uint32_t mask, uint8_t pos);
   uint8_t  set_reg_value(volatile uint32_t * reg, uint32_t value, uint32_t mask, uint8_t pos);

   uint16_t read_reg16_value(volatile uint16_t * reg);
   void     write_reg16_value(volatile uint16_t * reg, uint16_t value);

Description
^^^^^^^^^^^

read_reg_value() and write_reg_value() read from and write into a 32 bits
register directly, with no consideration for its potential fields. They can be
used at initialize or device reset time.
For half-word (16 bits) registers, read_reg16_value() and write_reg16_value()
behave like read_reg_value() and write_reg_value().

get_reg_value() and set_reg_value() can get or set a given field of a register,
without modifying the other fields.
They use the following arguments:

   * reg: the register address
   * value: for set_reg_bit(), the value to set in the field (position independent)
   * mask: the field mask (starting at bit 0). E.g. 0x3 means a field of 2 bits.
   * pos: the field position (starting with the least significant bit, used to
   * move the mask and the value to the correct position

get_reg_value() return the field value independently of its position.

These helpers are not made to be used directly on registers (volatile pointers)
but on basic variables.

