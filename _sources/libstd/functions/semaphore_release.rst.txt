semaphore
---------
Synchronization primitives with counters

Synopsis
^^^^^^^^

The semaphore functions family allows to use an integer variable to detect a
lock. The variable holds a counter that is set when calling the
semaphore_init() function.

If the counter is greater than 0, the semaphore_trylock() will try to decrease
the counter using the arch-specific exclusive atomic write on the counter.
If the counter is correctly updated, the function returns true. If the counter
is already set to 0 or the update fails due to a potential race condition with
another thread during the update, the function returns false.

The semaphore_release() function should be called only if the
semaphore_trylock() function has previously been called and has returned true.
This function tries to increment the lock counter and returns true if the
counter has been correctly increased, or false otherwise.  

The semaphore API respects the following prototypes ::

   #include "api/semaphore.h"

   void semaphore_init(uint8_t value, volatile uint32_t* semaphore);
   bool semaphore_trylock(volatile uint32_t* semaphore);
   void semaphore_lock(volatile uint32_t* semaphore);
   bool semaphore_release(volatile uint32_t* semaphore);


It is possible to use multiple semaphores at the same time.

It is possible to create mutually exclusive access (also known as mutex) to a
variable or a specific function when setting the semaphore to 1 at
initialization time. Although, you should use the mutex API instead for this
last case.

.. caution:: There is no protection against dead-lock, you must be aware of the
             impact of using semaphore and lock mechanisms in your software

.. caution:: The semaphore **must** be declared as volatile to avoid any border
	     effect associated to the compilation process. The assembler
	     backend manipulates the semaphore is using specific
             synchronisation instructions

.. hint:: When a lock is required between a thread and an ISR for which a
	  treatment can't be postponed (i.e. the application whishes to avoid
	  the lock to happend instead to detect it). The kernel sys_lock() API
          is a better choice

semaphore_trylock is nonblocking and try to lock the semaphore, returning false
if the lock fails.

semaphore_lock is a blocking function, waiting for the samaphore to be free to
be locked before returning.
