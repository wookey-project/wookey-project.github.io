queue
-----
Data queuing and enqueuing API

Synopsys
^^^^^^^^

This implementation is a libstd specific complex storage data queue management.
It behave as a FIFO implementation for various objects. It can be used, for
exemple, as a media for complex messages passing between threads or to hold
information in memory.

The libembed queue API is the following::

   #include "api/queue.h"

   mbed_error_t queue_create(uint32_t capacity, queue_t **queue);
   mbed_error_t queue_enqueue(queue_t *q, void *data);
   mbed_error_t queue_dequeue(queue_t *q, void **data);
   bool queue_is_empty(queue_t *q);
   mbed_error_t queue_available_space(queue_t *q, uint32_t *space);


Description
^^^^^^^^^^^


   * *queue_create()* create an empty queue of the given size
   * *queue_enqueue()* add an element in the queue
   * *queue_dequeue()* dequeue the next element, if it exists
   * *queue_next_element()* get the next element of the queue given in
     argument, if it exists, without dequeuing it
   * *queue_is_empty()* return the empty state of the queue
   * *queue_available_space()* get the remaining free slots count of the given queue


The Queue API is thread-safe and reentrant.

The Queue API is using the libstd allocator to manipulate the storage backend.
All data are stored in the task's heap.

for function returning mbed_error_t return type, queue API may returns:

   * MBED_ERROR_NONE: function completed successfully
   * MBED_ERROR_NOMEM: the allocator failed to allocate the requested memory on the head
   * MBED_ERROR_NOSTORAGE: the requested element is not in the queue
   * MBED_ERROR_BUSY: the queue is locked by another thread

