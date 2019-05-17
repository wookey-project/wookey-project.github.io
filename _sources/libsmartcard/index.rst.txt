.. _lib_smartcard:

.. highlight:: c

The smartcard library
=====================

.. contents::

This library is a wrapper for smart cards
(either contact or contactless) communication.

It handles APDU sending to the cards, and responses
received from the card, whatever the lower layer is
(ISO7816 for contact card, ISO14443 for contactless
cards, ...).

For now, only contact cards are supported, the ISO14443
is lacking a driver support that is a future work. 
 
.. include:: api.rst
.. include:: faq.rst
