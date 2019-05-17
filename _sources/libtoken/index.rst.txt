The token library
===================

The token library is used to communicate with the WooKey
project user authentication token (AUTH) and DFU token (DFU):

  * The **AUTH token** is the one used for holding the platform master encryption key (the AES-CBC-ESSIV encryption key)
  * The **DFU token** is the one used for key derivation when performing a DFU (Device Firmware Update) operation through USB

A mutual authentication must be performed between the platform
and the two tokens to establish a secure channel (with confidentiality,
integrity and anti-replay properties). This channel establishment
is transparently handled by libtoken as it is explained in the
API section.

The WooKey project makes use of a 2FA (two-factors authentication) for
a strong binding with the legitimate user. In this setup, the user will
have to provide two pins: a PET Pin and a User Pin. After providing the
Pet Pin, the user will be granted with a confirmation of his secret
Pet Name sentence.

.. note::
  Using a Pet Pin/Pet Name and then a User Pin might seem a bit
  complicated. Nonetheless, this scheme allows to avoid some attack
  scenarios with rogue tokens. The curious reader can refer to the
  WooKey publications for more details about the attacker model
  and how WooKey cryptography thwarts or limits many attack scenarios

The libtoken library implements primitives in the form of callbacks
to handle Pet Pin/Pet Name/User Pin interactions with an external
library (either in the form of a GUI, or any user interaction
means).

When the secure channel is mounted between the token and the
platform, instructions that are specific to each token type
(AUTH or DFU) can be send inside the secure channel. The libtoken
library implements such instructions:

  * AUTH: the main instruction with the AUTH token concerns getting the user encryption master key from the internal secure flash of the token
  * DFU: the DFU token has two instructions. The first one opens a firmware decryption session, and the second one asks the token for key derivation for each new firmware chunk to decrypt


.. toctree::
   :caption: Table of Contents
   :name: mastertoc
   :maxdepth: 2

   The token library API <api>
   FAQ <faq>

