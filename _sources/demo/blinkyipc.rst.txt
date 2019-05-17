.. _blinkyipc:

Blinky with IPC demo
====================

This example provides the same functionality as the one provided
by the 'blinky' standalone application described in :ref:`blinky`.

The main difference is that **two applications** are used: one 'button' app
handling the button push events, and one 'leds' app handling the LEDs toggling.
When the button is pushed, the 'button' app send a message to the 'leds' app
by using the **IPC** mechanism.

To load the default Ada/SPARK version of the menuconfig, launch ``make``
with ::

  make boards/32f407disco/configs/disco_blinky_ipc_ada_defconfig

The 'button' app
----------------

Getting the leds application id
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The button application must get the *task id* of the leds
application to be able to communicate with it.
During the initialization phase, it performs a ``sys_init(INIT_GETTASKID)``
syscall to get this id:

.. code-block:: c

    /* Get the LEDs task id to be able to communicate with it using IPCs */
    ret = sys_init(INIT_GETTASKID, "leds", &id_leds);

Sending a message by using IPCs
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

When the blue button is in the pushed state, the button application sends a
message to the 'leds' application by using IPCs.
EwoK IPC can be synchronous (blocking) of asynchronous (not blocking) as
described in :ref:`sys_ipc`.

Here, the ``IPC_SEND_SYNC`` flag means that the syscall is blocking and that
the task is blocked until the message is read by the receiver.

.. code-block:: c

    ret = sys_ipc(IPC_SEND_SYNC, id_leds, sizeof(button_pressed), (const char*) &button_pressed);

    if (ret != SYS_E_DONE) {
        printf("sys_ipc(): error. Exiting.\n");
        return 1;
    }

Yielding
^^^^^^^^

After sending the IPC to the leds task, the button task **yields** using the
``sys_yield()`` syscall. The task is put in a sleeping state and it will be
awaken by a new interrupt.
Yielding permits to release the CPU when no work have to be performed by
the task.

.. code-block:: c

        sys_yield();

The 'leds' app
--------------

That app is similar to the 'button' application. The main difference is
that it wait for some messages from the button app.

Receiving a message by using IPCs
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The task checks for and read an incoming message using the
``sys_ipc(IPC_RECV_ASYNC)`` syscall:

.. code-block:: c

    while (1) {
        id = id_button;
        msg_size = sizeof(button_pressed);

        ret = sys_ipc(IPC_RECV_ASYNC, &id, &msg_size, (char*) &button_pressed);
        ...

IPC receiving can be synchronous or asynchronous. Here, the syscall
is asynchronous, and thus not blocking.

Using a non blocking IPC permit to perform some other work when
no message is available. Here, the app must blink the leds even when
the button is not pushed.

Since the IPC reception is asynchronous, three cases can occur:

  * ``SYS_E_DONE``: there is an awaiting message sent by the button application
  * ``SYS_E_BUSY``: there is no awaiting message
  * ``SYS_E_DENIED`` or ``SYS_E_INVAL``: these are syscall errors and should not
    occur in a nominal behavior. Possible causes are missing permissions or
    improper parameters (ie. invalid task id)

.. code-block:: c

    while (1) {
        id = id_button;
        msg_size = sizeof(button_pressed);

        ret = sys_ipc(IPC_RECV_ASYNC, &id, &msg_size, (char*) &button_pressed);

        switch (ret) {
            case SYS_E_DONE:
                printf("BUTTON sent message: %x\n", button_pressed);

                if (button_pressed == true) {
                    /* Change leds state */
                    green_state   = (green_state == ON) ? OFF : ON;
                    orange_state  = (orange_state == ON) ? OFF : ON;
                    red_state     = (red_state == ON) ? OFF : ON;
                    blue_state    = (blue_state == ON) ? OFF : ON;

                    /* Show leds */
                    display_leds  = ON;
                }

                break;
            case SYS_E_BUSY:
                break;
            case SYS_E_DENIED:
            case SYS_E_INVAL:
            default:
                printf("sys_ipc(): error. Exiting.\n");
                return 1;
        }


