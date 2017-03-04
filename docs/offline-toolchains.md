# Yotta

The micro:bit DAL is built on top of [ARM mbed](http://mbed.com) and hence uses [yotta](http://yotta.mbed.com) as an offline build system.

When using `yotta` to build micro:bit projects there are currently two supported toolchains:

* GCC
* ARMCC

## Installation

In general, the first step is to get `yotta` and its dependencies onto your machine, to do this follow the install guide [here](http://docs.yottabuild.org/#installing).

For the micro:bit targets you currently still need the [`srecord` tools from sourceforge](http://srecord.sourceforge.net/) to create the final binaries for the micro:bit.


### Windows

The choices are:

* VM: install a VM machine and use Linux method
* Windows 10: install [wsl](https://msdn.microsoft.com/da-dk/commandline/wsl/install_guide) and use the linux method
* Native: follow the `yotta` install guide and install `srecord`.

### OSX

The choices are:

* VM: install a VM machine and use Linux method
* Native: follow the `yotta` install guide and install `srecord`.

Note:

* [srecord is also available from brew](http://brew.sh/):

```bash
brew install srecord
```

### Linux

The choices are:

* Native: follow the `yotta` install guide and install `srecord`.

Note:

* srecord is often available in the Linux distribution, for example:

```bash
sudo apt-get install srecord
```

## Build your first project

After installation, the result can be tested with some [microbit-samples](https://github.com/lancaster-university/microbit-samples). One can either git clone or download them.

In the directory that contains microbit-samples, the quick test for sucessfull installation of the work so far is:

```bash
yt clean
yt build
```

The file for the micro:bit is then `micro-bit-samples-combined.hex`, which can be found under the directory structure for `build`, the target `bbc-microbit-classic-gcc` and finally, `source`.

## Flash your micro:bit

Now that the hex is built, it can be flashed onto the micro:bit.
The expected result will be that the micro:bit will scroll `HELLO WORLD! :)` on its display.

### Windows

Flash the micro:bit (assuming it is plugged in and mounted at `E:`) as follows:

```bash
copy build\bbc-microbit-classic-gcc\source\microbit-samples-combined.hex E:
```

### MacOS

Flash the micro:bit (assuming it is plugged in and mounted at `/Volumes/"MICROBIT"`) as follows:

```bash
cp ./build/bbc-microbit-classic-gcc/source/microbit-samples-combined.hex /Volumes/"MICROBIT"
```

### Linux

Flash the micro:bit (assuming it is plugged in and mounted at `/media/MICROBIT`) as follows:

```bash
cp ./build/bbc-microbit-classic-gcc/source/microbit-samples-combined.hex /media/MICROBIT
```

## Tips

A `yotta` target contains the information required by `yotta` in order to build a project for a specific combination of hardware. This includes the type of compiler. The microbit projects can build with both `armcc` and `gcc`, but as it gets installed with the `yotta` installer, we'll use `gcc` by default and choose a micro:bit specific target that knows about the hardware on the board.

You can use either `yotta` or `yt`, which is far easier to type!

```bash
yt target bbc-microbit-classic-gcc
```

!!! note
    In microbit-samples this target will be configured by default.

You only need to set the target once per project. All future `yotta` commands will use this target information (for example, when resolving dependencies).


