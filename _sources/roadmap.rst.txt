.. _roadmap:

Roadmap
=======

About the components versioning
-------------------------------

Versions of the components
""""""""""""""""""""""""""

The WooKey project is composed of multiple software and hardware components.
These components are versioned depending on their maturity level, using the standard x.y.z triplet, where:

   * **x** is the major version
   * **y** is the minor update step
   * **z** is the current patchset

In WooKey, we consider that a software component reaches its release 1.0.0 when:

   * All the features requested by the development team have been implemented and improved
   * The code quality is good enough (this part is mostly subjective, we use various code checkers to validate RTE - Run Time Errors - absence, code complexity and style, etc.)
   * The component design is portable and generic enough (depending on the component)
   * The component documentation is complete

About component maturity
""""""""""""""""""""""""


.. image:: img/maturity.png
   :alt: maturity level
   :align: center

Maturity level consideration in WooKey:


   * Up to ``0.2``, the component is a draft, neither stable nor clean
   * Up to ``0.4``, the component has a part of the requested features written. These features may be unstable in some cases. Some other features are missing, the code architecture is not clean, the quality check and the documentation is not yet made
   * up to ``0.6``, the component is stable for a given usage, but is neither portable nor architecture-clean. It may require a cleaner rewrite (automaton design, and so on). The quality check is incomplete, the documentation may be missing
   * up to ``0.8``, the component is stable. The code architecture may require some enhancement (style, simplification, optimization). The RTE check (through static analysis and so on) should have been passed at least one time. The documentation may still be missing. All core features should be written and stable. Optional features may be missing.
   * up to ``1.0``, the component architecture should be clean. Component architecture, style, RTE check should be okay. The documentation should be written. The component reaches the 1.0 version when no part is missing
   * after ``1.0``, the `master` branch of the component evolves including new features, potential bugfixes, etc.


Life-cycle of the component and evolution of the maturity level:

   * First complete release or huge evolution with potential API incompatible content impact the major value (aka ``x``).
   * New feature releases impact the minor value (aka ``y``)
   * Bugfixes, RTE check, architecture and style updates impact the patchset value (aka ``z``)

Planned Updates and wishlist
----------------------------

The following roadmap describes:

   * The various components planned updates
   * Potential new features the project team wishes to implement or to see in the WooKey ecosystem in the next months


Components planned updates
""""""""""""""""""""""""""

.. list-table::
   :widths: 20 80
   :header-rows: 1

   * - Component
     - Update
   * - ``EwoK``
     - Finalize the Ada/SPARK implementation. The ADA kernel should no more rely on any C code
   * - ``Tataouine``
     - Support for RDP2 lock tooling through make target(s). Set device OTP area when locked
   * - ``Bootloader``
     - Usage of flash OTP area for more efficient RDP2 lock check, in association with Tataouine
   * - ``libmassstorage``
     - Move USB BULK stack into a dedicated library. libmassstorage becomes libscsi
   * - ``STM32F4 USB driver``
     - Clean reimplementation of the USB FS/HS driver, control plane inside a libusbcontrol independent library
   * - ``libstd``
     - Full cleaning of the allocator implementation. Add PRNG support with various random sources (beyond the TRNG), e.g. HMAC-DRBG


Ecosystem wished updates
""""""""""""""""""""""""

.. list-table::
   :widths: 20 80
   :header-rows: 1

   * - Feature name
     - Description
   * - ``libusbrawhid``
     - RAW HID stack over USB driver, to prepare HID support for various standards such as FIDO2
   * - ``libjtag``
     - JTAG protocol stack over USB driver, to behave like a JTAG probe


.. note::
   This project is Open-Source and contributions are welcome! If you wish to implement or update any
   software feature, merge requests are accepted


.. hint::
   Community updates or evolution is supported through classical github `pull requests <https://help.github.com/en/articles/about-pull-requests>`_
