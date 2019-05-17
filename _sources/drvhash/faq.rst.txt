Hash driver FAQ
---------------

Is the Hash driver thread safe?
""""""""""""""""""""""""""""""""

No, because the device is not. Beware to serialize correctly device requests to avoid any problems.

Can I request digests from unmapped data when using DMA?
"""""""""""""""""""""""""""""""""""""""""""""""""""""""""

Yes and no. It is possible to use DMA to let the HASH device access the data
directly instead of copying them into the input register, but the DMA
configuration is controlled by the kernel. You need at least to use DMA SHM
EwoK mechanism with another task to request digest calculation from this task
memory content.

