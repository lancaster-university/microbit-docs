# Yotta

The micro:bit DAL is built on top of [ARM mbed](http://mbed.com) and hence uses [yotta](http://yotta.mbed.com) as an offline build system.

When using `yotta` to build micro:bit projects there are currently two supported toolchains:

* GCC
* ARMCC

## Installation on Windows

There are three methods for building

* VM: install a VM machine and use Linux method
* WSL: install [wsl](https://msdn.microsoft.com/da-dk/commandline/wsl/install_guide) and use the linux method
* native: see below

### Install yotta and dependencies

The first step is to get `yotta` and its dependencies onto your machine, to do this follow the install guide [here](http://docs.yottabuild.org/#installing).


For the micro:bit targets you currently still need the `srecord` tools, which can be installed on **Windows** from [sourceforge](http://srecord.sourceforge.net/).

`srecord` is used to create the final binaries for the micro:bit so is an essential dependency.


### Fetch the example project

```bash
git clone https://github.com/lancaster-university/microbit-samples
cd microbit-samples
```

### Set your yotta target

A `yotta` target contains the information required by `yotta` in order to build a project for a specific combination of hardware. This includes the type of compiler. The microbit projects can build with both `armcc` and `gcc`, but as it gets installed with the `yotta` installer, we'll use `gcc` by default and choose a micro:bit specific target that knows about the hardware on the board.

You can use either `yotta` or `yt`, which is far easier to type!

```bash
yt target bbc-microbit-classic-gcc
```

!!! note
    In microbit-samples this target will be configured by default.

You only need to set the target once per project. All future `yotta` commands will use this target information (for example, when resolving dependencies).

#### Build the project

```
yt build
```

### Flash your micro:bit
The final step is to check your hex works.

The `yt build` command will place files in `/build/<TARGET_NAME>/source`. The file you will need to flash will be microbit-combined.hex.  Simply drag and drop the hex onto the MICROBIT usb device.

In the case of our example, using `bbc-microbit-classic-gcc` we could flash the micro:bit (assuming it is plugged in and mounted at `E:`) as follows:

```bash
copy build\bbc-microbit-classic-gcc\source\microbit-samples-combined.hex E:
```

The expected result will be that the micro:bit will scroll `HELLO WORLD! :)` on its display.

____________________

## Installation on Mac OSX

### Install yotta and dependencies

The first step is to get `yotta` and its dependencies onto your machine, to do this follow the install guide [here](http://docs.yottabuild.org/#installing).


For the micro:bit targets you currently still need the `srecord` tools, which can be installed on **Mac OSX** using
[brew](http://brew.sh/):
```
brew install srecord
```

You can also install it manually from [sourceforge](http://srecord.sourceforge.net/).

`srecord` is used to create the final binaries for the micro:bit so is an essential dependency.

### Fetch the example project

```bash
git clone https://github.com/lancaster-university/microbit-samples
cd microbit-samples
```

### Set your yotta target

A `yotta` target contains the information required by `yotta` in order to build a project for a specific combination of hardware. This includes the type of compiler. The microbit projects can build with both `armcc` and `gcc`, but as it gets installed with the `yotta` installer, we'll use `gcc` by default and choose a micro:bit specific target that knows about the hardware on the board.

You can use either `yotta` or `yt`, which is far easier to type!

```bash
yt target bbc-microbit-classic-gcc
```

!!! note
    In microbit-samples this target will be configured by default.

You only need to set the target once per project. All future `yotta` commands will use this target information (for example, when resolving dependencies).

#### Build the project

```bash
yt build
```

### Flash your micro:bit
The final step is to check your hex works.

The `yt build` command will place files in `/build/<TARGET_NAME>/source`. The file you will need to flash will be `microbit-samples-combined.hex`. Simply drag and drop the hex onto the MICROBIT usb device.

In the case of our example, using `bbc-microbit-classic-gcc` we could flash the micro:bit (assuming it is plugged in and mounted at `/Volumes/"MICROBIT"`) as follows:

```bash
cp ./build/bbc-microbit-classic-gcc/source/microbit-samples-combined.hex /Volumes/"MICROBIT"
```

The expected result will be that the micro:bit will scroll `HELLO WORLD! :)` on its display.

!!!note
    Note that if you'd like to copy the file from the command line, you can use the following command in any `yotta` project to do so, though it assumes you have only one micro:bit plugged in:

    ```
    cp build/$(yt --plain target | head -n 1 | cut -f 1 -d' ')/source/$(yt --plain ls | head -n 1 | cut -f 1 -d' ')-combined.hex  /Volumes/"MICROBIT"
    ```


____________________

## Installation on Linux

### Install yotta and dependencies

The first step is to get `yotta` and its dependencies onto your machine, to do this follow the install guide [here](http://docs.yottabuild.org/#installing).


For the micro:bit targets you currently still need the `srecord` tools, which can be installed on **Ubuntu** using

```bash
sudo apt-get install srecord
```

You can also install it manually from [sourceforge](http://srecord.sourceforge.net/).

`srecord` is used to create the final binaries for the micro:bit so is an essential dependency.


### Fetch the example project

```bash
git clone https://github.com/lancaster-university/microbit-samples
cd microbit-samples
```

### Set your yotta target

A `yotta` target contains the information required by `yotta` in order to build a project for a specific combination of hardware. This includes the type of compiler. The microbit projects can build with both `armcc` and `gcc`, but as it gets installed with the `yotta` installer, we'll use `gcc` by default and choose a micro:bit specific target that knows about the hardware on the board.

You can use either `yotta` or `yt`, which is far easier to type!

```bash
yt target bbc-microbit-classic-gcc
```

!!! note
    In microbit-samples this target will be configured by default.

You only need to set the target once per project. All future `yotta` commands will use this target information (for example, when resolving dependencies).

#### Build the project

```bash
yt build
```

### Flash your micro:bit
The final step is to check your hex works.

The `yt build` command will place files in `/build/<TARGET_NAME>/source`. The file you will need to flash will be `microbit-samples-combined.hex`. Simply drag and drop the hex onto the MICROBIT usb device.

In the case of our example, using `bbc-microbit-classic-gcc` we could flash the micro:bit (assuming it is plugged in and mounted at `/media/MICROBIT`) as follows:

```bash
cp ./build/bbc-microbit-classic-gcc/source/microbit-samples-combined.hex /media/MICROBIT
```

The expected result will be that the micro:bit will scroll `HELLO WORLD! :)` on its display.

!!!note
    Note that if you'd like to copy the file from the command line, you can use the following command in any `yotta` project to do so, though it assumes you have only one micro:bit plugged in:

    ```
    cp build/$(yt --plain target | head -n 1 | cut -f 1 -d' ')/source/$(yt --plain ls | head -n 1 | cut -f 1 -d' ')-combined.hex /media/MICROBIT/
    ```
