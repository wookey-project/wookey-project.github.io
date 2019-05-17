.. _target:

WooKey project
==============

.. contents::

Building trusted USB devices and IoTs
-------------------------------------

Securing the USB stack, and hence the USB hosts and devices, has been a growing
concern since exploitable flaws have been revealed with the BadUSB
threat [nohl2014badusb]_.
As a consequence: USB devices firmwares, operating systems, and user data are
at risk!

The WooKey project aims at prototyping a secure and trusted USB mass storage
device featuring user data encryption and strong user authentication, with
fully open source and open hardware foundations.

The Wookey is a custom STM32 based USB thumb drive with mass storage
capabilities designed for user data encryption and protection, with a
full-fledged set of in-depth security defenses:

- A secure DFU (Device Firmware Update) ensuring firmware integrity and
  authenticity
- Up-to-date cryptography
- An external and extractable authentication token embedding a secure element
- :ref:`ewok_kernel`, a secure microkernel implemented in
  Ada/SPARK
- Memory confinement using the MPU (Memory Protection Unit), privilege
  separation, W^X principle, stack and heap anti-smashing
- **Tataouine**, a versatile SDK developed to easily integrate user
  applications in C and Rust.
- Open source and open hardware

Informations about the security concerns are detailed in the section
:ref:`publi`.

Even though the current WooKey focuses on the mass
storage USB class, it's
easily portable to other USB device classes such as HID or CDC.

Beyond the mere USB oriented devices, the WooKey many defense in depth
primitives can be ported and used in **many IoT projects**.

WooKey threat model
-------------------

We consider that the adversary has logical or physical access to the
device:

* The adversary may try to read the data simply by connecting the device
  to a host

* The adversary may try to physically reading the mass storage cells, for
  example when the device is lost or stolen.

* The adversary may try to tamper with the device using logical attacks,
  for example when it is connected to an untrusted host, exploiting
  weaknesses in protocols used for external communication
  such as the USB stack or the external data storage buses.

* Side-channel and fault injection attacks on the device during
  the pre-authentication phase.

* The adversary may open the device to physically tamper with the
  internal storage, firmware, or any other component present on the
  actual device.

However, some threats are out of scope:

* We will only consider physical attacks where the adversary does not possess
  the legitimate user PIN code of the external authentication token.

* Side-channel and fault injection attacks on the device in
  a post-authentication phase are explicitly out of scope.

* The data integrity is out of scope.

Security features
-----------------

The WooKey provides the following main security features:

* **User data protection**: all data at rest are encrypted, and their
  confidentiality protected.

* **Strong user authentication**: the legitimate user must be present when data
  is decrypted. When a user PIN code is used, attack vectors to steal it must
  be limited.

* **Secure device software update (DFU)**: the device’s software is securely
  upgradable for system maintenance (e.g. security patches). Update files
  are authenticated and integrity is checked with no possible rollback to
  old versions. A software upgrade must be a voluntary and
  authenticated action. The firmware updates is reliable with no
  possible platform bricking.

* **Firmware robustness against software attacks**: the firmware implements
  many security mechanisms and mitigation techniques to hinder
  an adversary attacking the exposed software surface (on the USB bus
  for instance) to be able to get a privileged access to the platform, and
  to gain access to the critical materials (such as sensitive cryptographic
  keys). The MPU (Memory Protection Unit) is used to confine
  software attacks in unprivileged and isolated containers.

.. rubric:: References

.. [nohl2014badusb] BadUSB-On accessories that turn evil, Karsten Nohl and Jakob Lell, Black Hat USA, 2014

