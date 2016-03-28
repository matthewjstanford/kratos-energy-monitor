This is all derivitave work based on https://github.com/IanHarvey/spark-monitor & http://www.instructables.com/id/Internet-connected-home-energy-monitor/.

I only copied the info to this repository for my own reference. I do not claim any of this work as my own.

# Notes

I've been building the code using command-line source from GitHub:

git clone -b master https://github.com/spark/firmware.git
git clone -b master https://github.com/spark/core-common-lib.git
git clone -b master https://github.com/spark/core-communication-lib.git

and then build instructions are given in firmware/README.md.

## On expanding for multiple sensors:
Yes, it could be expanded - there are 8 analogue inputs on the Core, each of which could be connected to a sensor. You'd have to duplicate the R6,R7,C5 circuit for each sensor, but they could all share the R4/R5/C4 network. You'd also need to change the firmware to switch between analogue inputs - at the minute it's fixed at measuring A0.

### What I have done:
I duplicated R4/R5/C4 as well as R6/R7/C5. The additional sensor circuitry is connected to A1.

# Component details:

* D1-D4: Mouser - 512-1N4001 - $0.16
* C1: Mouser - 647-UVZ1E102MPD - $0.36
* C2: Mouser - 647-UVR1A101MDD - $0.24
* C3: Mouser - 810-FK18X7R1E104K - $0.29
* C4: Mouser - 647-UPW1E221MPD - $0.29
* C5: Mouser - 594-K471K15X7RF53H5 - $0.10
* R1: Mouser - 660-CF1/4CT52R103J - $0.15
* R2, R7: Mouser - 603-CFR-25JB-1K0 - $0.044
* R3: Mouser - 588-OK2235E-R52 - $0.013
* R4, R5: Mouser - 660-MF1/4LCT52R473J - $0.10
* R6: Mouser - 279-CB10LV221M - $0.20
* Q1: Mouser - 610-2N2222A - $1.80
* U1: Spark - Core - $39.00
* 12v-5v converter: Amazon - B008BHAOQO - $6.05
* AC-AC adapter: Amazon - B00J356DB8 - $12.99
* Housing: Mouser - 546-1591CSFLBK - $4.01
* LED: Mouser - 749-PM3PGD - $0.59
* Perfboard: Mouser - 534-3400 - $4.34
* Male Audio Plug: Mouser - 490-MP3-3501 - $1.10
* Female Audio Plug: Amazon - B00EZIGTYY - $0.499
* Female DC Jack: Adafruit - 1505 - $0.95
* CT Sensor: EnviR energy monitor CT Sensors (bought in 2011)

## CT Sensors

The CT Sensors were recycled from an old EnviR CurrentCost system that I had purchased from Amazon in 2011 and never hooked up. I did some research (looked at the resistor embedded in the sensors) and estimate the number of turns at 1500.

# Firmware Readme:

# Spark Core Firmware [![Build Status](https://travis-ci.org/spark/firmware.svg)](https://travis-ci.org/spark/firmware)

This is the main source code repository of the Spark Core firmware libraries.

*Photon users:* This firmware branch is _not_ yet updated for the Photon;
For the latest (0.4.3) firmware that is compatible with the Photon,
please switch to the [latest branch](https://github.com/spark/firmware/tree/latest).


This firmware depends on two other libraries: the [Spark Common Library](http://www.github.com/spark/core-common-lib) and the [Spark Communication Library](http://www.github.com/spark/core-communication-lib)

1. [Download and Install Dependencies](#1-download-and-install-dependencies)
2. [Download and Build Repositories](#2-download-and-build-repositories)
3. [Edit and Rebuild](#3-edit-and-rebuild)
4. [Flash It!](#4-flash-it)

## 1. Download and Install Dependencies

1. [GCC for ARM Cortex processors](#1-gcc-for-arm-cortex-processors)
2. [Make](#2-make)
3. [Device Firmware Upgrade Utilities](#3-device-firmware-upgrade-utilities)
4. [Zatig](#4-zatig) (for windows users only)
5. [Git](#5-git)


#### 1. GCC for ARM Cortex processors
The Spark Core uses an ARM Cortex M3 CPU based microcontroller. All of the code is built around the GNU GCC toolchain offered and maintained by ARM.

Download and install the latest version from: https://launchpad.net/gcc-arm-embedded

Mac users can install the toolchain with [Homebrew](http://brew.sh/), using the PX4 formula:
```
brew tap PX4/homebrew-px4
brew update
brew install gcc-arm-none-eabi
```

#### 2. Make
In order to turn your source code into binaries, you will need a tool called `make`. Windows users need to explicitly install `make` on their machines. Make sure you can use it from the terminal window.

Download and install the latest version from: http://gnuwin32.sourceforge.net/packages/make.htm

#### 3. Device Firmware Upgrade Utilities
Install dfu-util 0.8. Mac users can install dfu-util with [Homebrew](http://brew.sh/) or [Macports](http://www.macports.org), Linux users may find it in their package manager, and everyone can get it from http://dfu-util.sourceforge.net/

#### 4. Zatig
In order for the Core to show up on the dfu list, you need to replace the USB driver with a utility called [Zadig](http://zadig.akeo.ie/). Here is a [tutorial](https://community.spark.io/t/tutorial-installing-dfu-driver-on-windows/3518) on using it. This is only required for Windows users.

#### 5. Git

Download and install Git: http://git-scm.com/

## 2. Download and Build Repositories

The entire Spark Core firmware is organized into three repositories. The main firmware is located under *firmware*, while the supporting libraries are subdivided in to *core-common-lib* and *core-communication-lib*.

#### How do we *download* these repositories?
You can access all of the repositories via any git interface or download it directly from the website.

Make sure all of the following repositories are downloaded into the same folder. *For example (if all of the repositories are downloaded in a folder called Spark):*

```
D:\Spark\firmware
D:\Spark\core-common-lib
D:\Spark\core-communication-lib
```

*Method 1: Through the git command line interface.*

Open up a terminal window, navigate to your destination directory and type the following commands:

(Make sure you have git installed on your machine!)

* `git clone https://github.com/spark/firmware.git`
* `git clone https://github.com/spark/core-common-lib.git`
* `git clone https://github.com/spark/core-communication-lib.git`

*Method 2: Download the zipped files directly from the Spark's GitHub website*

* [firmware](https://github.com/spark/firmware/archive/master.zip)
* [core-common-lib](https://github.com/spark/core-common-lib/archive/master.zip)
* [core-communication-lib](https://github.com/spark/core-communication-lib/archive/master.zip)

#### How do we *build* these repositories?

Make sure you have downloaded and installed all the required dependencies as mentioned [previously.](#1-download-and-install-dependencies). Note, if you've downloaded or cloned these previously, you'll want to `git pull` or redownload all of them before proceeding.

Open up a terminal window, navigate to the build folder under firmware
(i.e. `cd firmware/build`) and type:

    make

    This will build your main application (`firmware/src/application.cpp`) and required dependencies.

    *For example:* `D:\Spark\firmware\build [master]> make`

##### Common Errors

* `arm-none-eabi-gcc` and other required gcc/arm binaries not in the PATH.
  Solution: Add the /bin folder to your $PATH (i.e. `export PATH="$PATH:<SOME_GCC_ARM_DIR>/bin`).
    Google "Add binary to PATH" for more details.

    * You get `make: *** No targets specified and no makefile found.  Stop.`.
      Solution: `cd firmware/build`.

      Please issue a pull request if you come across similar issues/fixes that trip you up.

### Navigating the code base

All of the repositories are sub divided into functional folders:

1. `/src` holds all the source code files
2. `/inc` holds all the header files
3. `/build` holds the makefile and is also the destination for the compiled `.bin` and `.hex` files.

## 3. Edit and Rebuild

Now that you have your hands on the entire Spark Core firmware, its time to start hacking!

### What to edit and what not to edit?

The main user code sits in the application.cpp file under firmware/src/ folder. Unless you know what you are doing, refrain yourself from making changes to any other files.

After you are done editing the files, you can rebuild the repository by running the `make` command in the `firmware/build` directory. If you have made changes to the other two repositories, make automatically determines which files need to be rebuilt and builds them for you.

## 4. Flash It!

Its now time to transfer your code to the Spark Core! You can always do this using the Over The Air update feature or, if you like wires, do it over the USB.

*Make sure you have the `dfu-util` command installed and available through the command line*

#### Steps:
1. Put your Core into the DFU mode by holding down the MODE button on the Core and then tapping on the RESET button once. Release the MODE button after you start to see the RGB LED flashing in yellow. It's easy to get this one wrong: Make sure you don't let go of the left button until you see flashing yellow, about 3 seconds after you release the right/RESET button. A flash of white then flashing green can happen when you get this wrong. You want flashing yellow.

2. Open up a terminal window on your computer and type this command to find out if the Core indeed being detected correctly.

   `dfu-util -l`
      you should get the following in return:
         ```
            Found DFU: [1d50:607f] devnum=0, cfg=1, intf=0, alt=0, name="@Internal Flash  /0x08000000/20*001Ka,108*001Kg"
               Found DFU: [1d50:607f] devnum=0, cfg=1, intf=0, alt=1, name="@SPI Flash : SST25x/0x00000000/512*04Kg"
                  ```

                     (Windows users will need to use the Zatig utility to replace the USB driver as described earlier)

                     3. Now, navigate to the build folder in your firmware repository and use the following command to transfer the *.bin* file into the Core.
                        ```
                           dfu-util -d 1d50:607f -a 0 -s 0x08005000:leave -D core-firmware.bin
                              ```

                                 For example, this is how my terminal looks like:
                                    ```
                                    D:\Spark\firmware\build [master]> dfu-util -d 1d50:607f -a 0 -s 0x08005000:leave -D core-firmware.bin
                                       ```
                                       Upon successful transfer, the Core will automatically reset and start the running the program.

##### Common Errors
* As of 12/4/13, you will likely see `Error during download get_status` as the last line from
the `dfu-util` command. You can ignore this message for now.  We're not sure what this error is all about.

* If you are having trouble with dfu-util, (like invalid dfuse address), try a newer version of dfu-util. v0.8 works well.

**Still having troubles?** Checkout our [resources page](https://www.spark.io/resources), hit us up on IRC, etc.

### CREDITS AND ATTRIBUTIONS

The Spark application team: Zachary Crockett, Satish Nair, Zach Supalla, David Middlecamp and Mohit Bhoite.

The firmware uses the GNU GCC toolchain for ARM Cortex-M processors, ARM's CMSIS libraries, TI's CC3000 host driver libraries, STM32 standard peripheral libraries and Arduino's implementation of Wiring.

### LICENSE

Unless stated elsewhere, file headers or otherwise, all files herein are licensed under an LGPLv3 license. For more information, please read the LICENSE file.

### CONTRIBUTE

Want to contribute to the Spark Core project? Follow [this link]() to find out how.

### CONNECT

Having problems or have awesome suggestions? Connect with us [here.](https://community.sparkdevices.com/)

### VERSION HISTORY

Latest Version: v1.0.0


