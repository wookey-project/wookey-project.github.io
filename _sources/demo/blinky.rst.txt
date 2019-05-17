.. _blinky:

Blinky demo
===========

The 'blinky' demo is in the directory ``app/blinky``.

Building and running the demo
-----------------------------

To build a new firmware and to flash the board with it, follow the
instructions in :ref:`quickstart`.

To choose the 'blinky' profile, you will have to execute ``make`` with ::

   $ make boards/32f407disco/configs/disco_blinky_ada_defconfig

Understanding the demo code
---------------------------

LEDs configuration
^^^^^^^^^^^^^^^^^^

To use a device, first, it must be declared and registered in the kernel.
A ``device_t`` structure is associated to the LEDs. It describes its memory
mapping, the GPIOs and the EXTIs of the device.

.. code-block:: C

    device_t    leds;
    ...
    memset (&leds, 0, sizeof (leds));

    strncpy (leds.name, "LEDs", sizeof (leds.name));
    leds.gpio_num = 4; /* Number of configured GPIO */

    leds.gpios[0].kref.port = GPIO_PD;
    leds.gpios[0].kref.pin = 12;
    leds.gpios[0].mask     = GPIO_MASK_SET_MODE | GPIO_MASK_SET_PUPD |
                             GPIO_MASK_SET_TYPE | GPIO_MASK_SET_SPEED;
    leds.gpios[0].mode     = GPIO_PIN_OUTPUT_MODE;
    leds.gpios[0].pupd     = GPIO_PULLDOWN;
    leds.gpios[0].type     = GPIO_PIN_OTYPER_PP;
    leds.gpios[0].speed    = GPIO_PIN_HIGH_SPEED;

    leds.gpios[1].kref.port = GPIO_PD;
    leds.gpios[1].kref.pin = 13;
    leds.gpios[1].mask     = GPIO_MASK_SET_MODE | GPIO_MASK_SET_PUPD |
                             GPIO_MASK_SET_TYPE | GPIO_MASK_SET_SPEED;
    ...

Then, the ``sys_init()`` syscall is used to register the device with the kernel:

.. code-block:: c

    ret = sys_init(INIT_DEVACCESS, &leds, &desc_leds);

The ``desc_leds`` variable is a descriptor returned by the syscall. It's
ignored in that example (it's used only by some few syscalls :ref:`sys_cfg`).

Button configuration
^^^^^^^^^^^^^^^^^^^^

The button must also be declared and registered with the kernel.
Note the registration of the ISR handler with:


.. code-block:: c

    button.gpios[0].exti_trigger = GPIO_EXTI_TRIGGER_RISE;
    button.gpios[0].exti_lock    = GPIO_EXTI_UNLOCKED;
    button.gpios[0].exti_handler = (user_handler_t) exti_button_handler;

The ``GPIO_EXTI_TRIGGER_RISE`` configures the IRQ associated to the GPIO to be
triggered only on a rising edge (corresponding to a button push in our case).

The ``GPIO_EXTI_UNLOCKED`` has a special meaning. The ``GPIO_EXTI_LOCKED``
means that the EXTI line is muted and that it must be voluntary "unlocked"
using a specific syscall to receive further EXTIs.

The ``exti_handler`` field is initialized with the address of the ISR handler
that will be executed for each EXTI interrupt.

Leaving the initialization phase
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

When all devices are registered, they still can't be used by the app.
Before, the initialization phase must be leaved using the
``sys_init(INIT_DONE)`` syscall before using them:

.. code-block:: c

    /* Devices and resources registration is finished */
    ret = sys_init(INIT_DONE);

Be aware that after that, no more further device or resource registration is
possible.

ISR handler
^^^^^^^^^^^

In our example, the ISR handler ``exti_button_handler()``
set the global variable ``button_pushed`` to notify the interrupt event:

.. code-block:: c

  void exti_button_handler ()
  {
    uint64_t        clock;
    e_syscall_ret   ret;

    /* Syscall to get the elapsed cpu time since the board booted */
    ret = sys_get_systick(&clock, PREC_MILLI);

    if (ret == SYS_E_DONE) {
            /* Debounce time (in ms) */
            if (clock - last_isr < 20) {
                last_isr = clock;
                return;
            }
    }

    last_isr = clock;
    button_pushed = true;
  }

The only subtlety here is the *debouncing* handling inside the ISR to avoid
burst of interrupts.
The debouncing time is arbitrary fixed to 20 milliseconds.
The ``sys_get_systick()`` syscall is used to return elapsed CPU time since the
board booted.

Main loop
^^^^^^^^^

After the initialization phase, the main function executes a loop that waits
for interrupt notifications by checking the value of ``button_pushed``.
When the Button is pushed, LEDs blinking pattern is switched.

.. code-block:: c

    while (1) {

        if (button_pushed == true) {
            printf ("button has been pressed\n");

            /* Change leds state */
            green_state   = (green_state == ON) ? OFF : ON;
            orange_state  = (orange_state == ON) ? OFF : ON;
            red_state     = (red_state == ON) ? OFF : ON;
            blue_state    = (blue_state == ON) ? OFF : ON;

            /* Show leds */
            display_leds  = ON;

            button_pushed = false;
        }
        ...

To make the LEDs blinking, their related GPIO must be set to ON of OFF
using the ``sys_cfg()`` syscall:

.. code-block:: c

        ....
        if (display_leds == ON) {
            ret = sys_cfg(CFG_GPIO_SET, (uint8_t) leds.gpios[0].kref.val, green_state);
            if (ret != SYS_E_DONE) {
                printf ("sys_cfg(): failed\n");
                return 1;
            }
        ...
        } else {
            ret = sys_cfg(CFG_GPIO_SET, (uint8_t) leds.gpios[0].kref.val, 0);
            if (ret != SYS_E_DONE) {
                printf ("sys_cfg(): failed\n");
                return 1;
            }
        ...

Then, the task sleeps 500 milliseconds:

.. code-block:: c

        /* Sleeping for 500 ms */
        sys_sleep (500, SLEEP_MODE_INTERRUPTIBLE);

If the button is pushed during that sleeping time, the task is awake
due to the ``SLEEP_MODE_INTERRUPTIBLE`` option.

