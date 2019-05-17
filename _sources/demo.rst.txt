.. _demo:

Demo examples
=============

This section describes a simple application to make the four LEDs
of the STM32 Discovery F407 blinking and reacting to the press
of the blue button on the board.

The purpose of this example is to show how a single application can interact
with the GPIOs, in both output mode (with the LEDs: PD12, PD13, PD14 and PD15)
and input mode (with the push Button: PA0).

Two demo examples are provided:

  * A 'blinky' standalone user task (see :ref:`blinky`) executes on the top
    of Ewok. It toggles the board LEDs and waits for some blue button
    pressing events to switch the blinking pattern

  * The 'blinky with IPC' demo (see :ref:`blinkyipc`) do
    the same as the 'blinky' app, but with two tasks communicating with IPC.
    On task manage the LEDs while the other receives events from the blue button.

.. toctree::
  :maxdepth: 2

  Blinky demo <demo/blinky>
  Blinky with IPC demo <demo/blinkyipc>

