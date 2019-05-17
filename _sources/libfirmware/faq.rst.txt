FAQ
---

Why voluntary updating bootinfo instead of adding them to the firmware file directly ?
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

As we are in small devices where the firmware file has to be written in place
during its download, it is not possible to validate the firmware integrity and
authenticity while the firmware is not fully written. As a consequence, a
firmware image holding the bootinfo would be bootable even if the
authentication or integrity check fails.


