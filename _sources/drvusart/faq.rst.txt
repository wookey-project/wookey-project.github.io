USART driver FAQ
----------------

Can I declare multiple U(S)ART devices in the same time ?
"""""""""""""""""""""""""""""""""""""""""""""""""""""""""

Yes, as far as you handle correctly your various configs. You may need, depending on you memory constraints, to map them voluntary.


In voluntary mode where only my ISR handle the device, should I keep (un)mapping the device ?
"""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

No! The map mode is for the main thread only. In ISR mode, the corresponding device is always mapped.
