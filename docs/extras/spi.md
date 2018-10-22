#SPI

SPI on the micro:bit is supported in the micro:bit runtime through the ARM mbed [`SPI`](https://developer.mbed.org/handbook/SPI) class.
However, by design, this is not initialised by default as part of the uBit object.

The reasons for this are:

 - SPI requires the dedicated use of three GPIO pins. Committing these pins would prevent them from being used as general purpose I/O
 - The SPI module also requires a small amount of additional memory to operate, and this is not necessary for the majority of applications

!!!note
    We may wrap SPI in the future and attach it to the [`MicroBit`](../ubit.md) class
    to provide lazy initialisation of the SPI module.

In the meantime we recommend you instantiate your own instance of [`SPI`](https://developer.mbed.org/handbook/SPI), using the mbed [`SPI`](https://developer.mbed.org/handbook/SPI) class:
```cpp
SPI spi(MOSI, MISO, SCK);
```
