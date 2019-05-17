token library FAQ
---------------------

What are the dependencies of libtoken?
""""""""""""""""""""""""""""""""""""""

The token library heavily relies on libecc for its asymmetric
cryptographic algorithms, and on libaes and libhmac for the
symmetric part. The communication channel is made over an
ISO7816 layer, so all the ISO7816 stack (libsmartcard, libiso7816,
the platform ISO7816 driver) are used.

Is it possible to make libtoken autonomous?
"""""""""""""""""""""""""""""""""""""""""""

Yes! Since libtoken only relies on APDUs to communicate with
the tokens, and since libecc, libaes and libhmac are
portable (with the exception of the masked AES of libaes
that can be replaced with a pure C AES), it is possible
to port libtoken anywhere provided that the physical
line handling APDUs is provided.

As an example, we have successfully ported libtoken to
a Linux PC host by replacing the libsmartcard calls
with PC/SC calls to send APDUs and receive responses
from the token.
