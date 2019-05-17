mutexes
-------

Mutual exclusive locks

Synopsys
^^^^^^^^

The mutex functions family allows to use a variable to detect a mutually exclusive lock. This permit to protect critical sections of code between two concurrent threads.

In mutexes, unlike semaphores, there is no counter notions. The mutex can be locked or free. When a function try to lock the mutex, it might fail, if a preemption happen just between the mutex load and store. In this very case, the failure is detected and the lock failure can be handled by the calling code.

The mutex API respects the following prototypes::

   #include "api/semaphore.h"

   void mutex_init(volatile uint32_t *mutex);

   bool mutex_trylock(volatile uint32_t *mutex);

   void mutex_lock(volatile uint32_t *mutex);

   bool mutex_tryunlock(uint32_t *mutex);

   void mutex_unlock(uint32_t *mutex);

All mutex API but the mutex_lock() and mutex_unlock() functions are non-blocking functions. mutex_lock() and mutex_unlock() block the caller until the mutex is free to be acceded exclusively.

.. danger::
   Exclusive store may fail on ARM systems, even when releasing a mutex. This behavior may happen when an exception arrise during the execution of the overall __STREX intrinsic. Retrying to unlock just after should be enough is nearly all the times

It is possible to use multiple mutexes in the same time.

.. caution:: There is no protection against dead-lock, you must be aware of the impact of using mutexes and lock mechanisms in your software

.. caution:: The mutex **must** be declared as volatile to avoid any border effect associated to the compilation process. The assembler backend manipulates the mutexes is using specific synchronisation instructions


