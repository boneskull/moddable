# Moddable SDK - Getting Started

Copyright 2016-2019 Moddable Tech, Inc.<BR>
Revised: May 21, 2019

This document provides an introduction to getting started building apps with the Moddable SDK. It describes how to configure the host build environments, install the required SDKs, drivers and development tools, build applications, and use `xsbug`, the JavaScript source code debugger.

## Table of Contents

- [Overview](#overview)
- [macOS](#mac)
  _ [Host environment setup](#host-mac)
  _ [ESP8266](#esp8266-mac) \* [ESP32](#esp32-mac)
- [Windows](#windows)
  _ [Host environment setup](#host-windows)
  _ [ESP8266](#esp8266-windows) \* [ESP32](#esp32-windows)
- [Linux](#linux)
  _ [Host environment setup](#host-linux)
  _ [ESP8266](#esp8266-linux) \* [ESP32](#esp32-linux)
- [Debugging applications](#debugging-applications)
- [ESP8266 Arduino version 2.4](#arduino-version)

<a id="overview"></a>

## Overview

Before you can build applications, you need to set up your host environment and install the required drivers and development tools for your target device. Fo example, if you want to build applications for Moddable One using a Mac, you'll need to follow the macOS host environment and ESP8266 setup instructions below.

A full list of supported target devices is available in the **Target platforms** section of the [examples readme](https://github.com/Moddable-OpenSource/moddable/tree/public/examples).

Additional getting started guides are available for the following devices:

- [Moddable One](./devices/moddable-one.md)
- [Moddable Two](./devices/moddable-two.md)
- [Moddable Three](./devices/moddable-three.md)
- [Silicon Labs Gecko devices](https://github.com/Moddable-OpenSource/moddable/blob/public/documentation/devices/gecko/GeckoBuild.md)
- [Qualcomm QCA4020](https://github.com/Moddable-OpenSource/moddable/blob/public/documentation/devices/qca4020/README.md)

<a id="mac"></a>

## macOS

<a id="host-mac"></a>

### Host environment setup

> The Moddable SDK requires macOS Sierra version 10.12 or newer and Xcode version 9 or newer.

1. Download and install [Xcode](https://developer.apple.com/xcode/). Launch Xcode to install additional command line components when prompted.

2. Create a `Projects` directory in your home directory at `~/Projects` for the Moddable SDK repository.

   > Note: The Moddable SDK repository can be downloaded to any directory. These setup instructions assume the Moddable SDK is downloaded to the `~/Projects` directory.

3. Download the [Moddable repository](https://github.com/Moddable-OpenSource/moddable), or use the `git` command line tool as follows:

   ```
   cd ~/Projects
   git clone https://github.com/Moddable-OpenSource/moddable
   ```

4. Setup the `MODDABLE` environment variable to point at your local Moddable SDK repository directory and edit the `PATH` environment variable in your `~/.profile` to include the build directory:

   ```
   export MODDABLE="/Users/<user>/Projects/moddable"
   export PATH="${MODDABLE}/build/bin/mac/release:$PATH"
   ```

5. Build the Moddable command line tools, simulator, and debugger from the command line:

   ```
   cd ${MODDABLE}/build/makefiles/mac
   make
   ```

6. Launch the `xsbug` debugger from the command line:

   ```
   open ${MODDABLE}/build/bin/mac/release/xsbug.app
   ```

7. Verify the host environment setup by building the starter `helloworld` application for the desktop simulator target:

   ```
   cd ${MODDABLE}/examples/helloworld
   mcconfig -d -m -p mac
   ```

<a id="esp8266-mac"></a>

### ESP8266 (Moddable Zero) setup

1. Complete ["Host environment setup"](#host-mac) for macOS.

2. Create an `esp` directory in your home directory at `~/esp` for required third party SDKs and tools.

3. Download and install the Silicon Labs [CP210x USB to UART VCP driver](https://www.silabs.com/products/development-tools/software/usb-to-uart-bridge-vcp-drivers).

4. Download and untar the [Xtensa lx106 architecture GCC toolchain](http://www.moddable.tech/private/esp8266.toolchain.darwin.tgz). Copy the `toolchain` directory into the `~/esp` directory.

5. Download the [ESP8266 core for Arduino repository](https://github.com/esp8266/Arduino/releases/download/2.3.0/esp8266-2.3.0.zip). Copy the extracted `esp8266-2.3.0` folder into your `~/esp` directory.

6. Clone the [ESP8266 SDK based on FreeRTOS](https://github.com/espressif/ESP8266_RTOS_SDK) repository into the `~/esp` directory:

   ```
   cd ~/esp
   git clone https://github.com/espressif/ESP8266_RTOS_SDK.git
   ```

   We need the v3.0rc1 version:

   ```
   cd ESP8266_RTOS_SDK
   git checkout v3.0-rc1
   ```

7. Install the required Python packages:

   ```
   pip install --user pyserial
   ```

8. Connect the ESP8266 to your computer with a USB cable.

9. Verify the setup by building `helloworld` for the `esp` target:

   ```
   cd ${MODDABLE}/examples/helloworld
   mcconfig -d -m -p esp
   ```

   For Moddable Zero applications that rely on the screen or use I2C pins, build for the `esp` target with the `moddable_zero` subplatform.

   ```
   cd ${MODDABLE}/examples/piu/balls
   mcconfig -d -m -p esp/moddable_zero
   ```

<a id="esp32-mac"></a>

### ESP32 setup

1. Complete ["Host environment setup"](#host-mac) for macOS.

2. Create an `esp32` directory in your home directory at `~/esp32` for required third party SDKs and tools.

3. Download and install the Silicon Labs [CP210x USB to UART VCP driver](https://www.silabs.com/products/development-tools/software/usb-to-uart-bridge-vcp-drivers).

4. Download and untar the [ESP32 GCC toolchain](https://dl.espressif.com/dl/xtensa-esp32-elf-osx-1.22.0-80-g6c4433a-5.2.0.tar.gz). Copy the extracted `xtensa-esp32-elf` directory into your `~/esp32` directory.

5. Clone the `ESP-IDF` GitHub repository into your `~/esp32` directory. Make sure to specify the `--recursive` option:

   ```
   cd ~/esp32
   git clone --recursive https://github.com/espressif/esp-idf.git
   ```

   Use the v3.1 version:

   ```
   cd esp-idf
   git checkout release/v3.1
   git submodule update
   ```

6. Set the `IDF_PATH` environment variable in your `~/.profile` to the `esp-idf` directory:

   ```
   export IDF_PATH="/Users/<user>/esp32/esp-idf"
   ```

   > Note: Close and reopen the Terminal window to enable the `IDF_PATH` environment variable.

7. Install the Python `pip` package management system:

   ```
   cd ~/esp32
   sudo easy_install pip
   ```

8. Install the required Python packages:

   ```
   python -m pip install --user -r $IDF_PATH/docs/requirements.txt
   ```

9. Update the `PATH` environment variable in your `~/.profile` to include the toolchain directory:

   ```
   export PATH=$PATH:$HOME/esp32/xtensa-esp32-elf/bin
   ```

10. Connect the ESP32 device to your macOS host with a USB cable and determine the serial port of the ESP32 device.

    To determine the serial port, examine the list of devices before and after plugging in your ESP32 device and note the new serial port that shows up. To see a list of serial devices, use the following command in Terminal:

    ```
    ls /dev/cu.*
    ```

11. Set the `UPLOAD_PORT` environment variable in your `~/.profile` to the ESP32 serial port:

    ```
    export UPLOAD_PORT=/dev/cu.SLAB_USBtoUART
    ```

12. Verify the setup by building `helloworld` for the `esp32` target:


    ```
    cd ${MODDABLE}/examples/helloworld
    mcconfig -d -m -p esp32
    ```

    For ESP32-based Moddable Zero applications that rely on the screen or use I2C pins, build for the `esp32` target with the `moddable_zero` subplatform.

    ```
    cd ${MODDABLE}/examples/piu/balls
    mcconfig -d -m -p esp32/moddable_zero
    ```

    > Note that the first time you build an application for the ESP32 target, the toolchain may prompt you to enter configuration options. If this happens, accept the defaults.

    > If you encounter SSL certificate errors while building the ESP-IDF, you may need to install Python 2.7 and the required packages manually. We've used [brew](https://brew.sh/) to install the additional components:
    >
    ```
    brew install python
    brew install python@2
    pip install future
    pip install pyserial
    pip install cryptography
    ```

<a id="windows"></a>

## Windows

<a id="host-windows"></a>

#### Host environment setup

> The Moddable SDK requires Windows 7 Pro SP1 or newer and Microsoft Visual Studio Community 2017 or newer.

1. Download [Microsoft Visual Studio 2017 Community Edition installer](https://www.visualstudio.com/downloads/). Launch the installer, choose the "Desktop development for C++" option and install.

2. Create a `Projects` directory in your `%USERPROFILE%` directory, e.g. `C:\Users\<your-user-name>` for the Moddable SDK repository.

   > Note: The Moddable SDK repository can be downloaded to any directory. These setup instructions assume the Moddable SDK is downloaded to the `%USERPROFILE%` directory.

3. Download the [Moddable repository](https://github.com/Moddable-OpenSource/moddable), or use the `git` command line tool as follows:

   ```
   cd C:\Users\<user>\Projects
   git clone https://github.com/Moddable-OpenSource/moddable
   ```

4. Setup the `MODDABLE` environment variable to point at your local Moddable SDK repository directory:

   ```
   set MODDABLE=C:\Users\<user>\Projects\moddable
   ```

5. Edit the system `PATH` environment variable to include the build directory:

   ```
   %MODDABLE%\build\bin\win\release
   ```

   > Environment variables should be set from the System Control Panel. The steps required vary depending on the Windows OS version.

6. Launch the "Developer Command Prompt for VS 2017" command line console. Build the Moddable command line tools, simulator, and debugger from the command line:

   ```
   cd %MODDABLE%\build\makefiles\win
   build
   ```

7. Launch the `xsbug` debugger from the command line:

   ```
   xsbug
   ```

8. Verify the host environment setup by building the starter `helloworld` application for the desktop simulator target:

   ```
   cd %MODDABLE%\examples\helloworld
   mcconfig -d -m -p win
   ```

<a id="esp8266-windows"></a>

### ESP8266 (Moddable Zero) setup

1. Complete ["Host environment setup"](#host-windows) for Windows.

2. Create an `esp` directory in your home `%USERPROFILE%` directory, e.g. `C:\Users\<your-user-name>`.

3. Download and install the Silicon Labs [CP210x USB to UART VCP driver](https://www.silabs.com/products/development-tools/software/usb-to-uart-bridge-vcp-drivers).

4. Download the [esptool](https://github.com/igrr/esptool-ck/releases/download/0.4.13/esptool-0.4.13-win32.zip). Unzip the archive and copy the `esptool.exe` executable from the `esptool-0.4.13-win32` directory into the `esp` directory.

5. Download and unzip the [Cygwin toolchain support package](http://www.moddable.tech/private/cygwin.win32.zip). Copy the `cygwin` directory into the `esp` directory.

6. Download and unzip the [Xtensa lx106 architecture GCC toolchain](http://www.moddable.tech/private/esp8266.toolchain.win32.zip). Copy the `xtensa-lx106-elf` directory into the `esp` directory.

7. Download the [ESP8266 core for Arduino repository](https://github.com/esp8266/Arduino/releases/download/2.3.0/esp8266-2.3.0.zip). Copy the extracted `esp8266-2.3.0` folder into your `esp` directory.

8. Clone the [ESP8266 SDK based on FreeRTOS](https://github.com/espressif/ESP8266_RTOS_SDK) repository into the `~/esp` directory:

   ```
   cd C:\Users\<user>\esp
   git clone https://github.com/espressif/ESP8266_RTOS_SDK.git
   ```

   We need the v3.0rc1 version:

   ```
   cd ESP8266_RTOS_SDK
   git checkout v3.0-rc1
   ```

9. Connect the ESP8266 to your computer with a USB cable.

10. Launch the Windows Device Manager, open the "Ports (COM & LPT)" section, and verify the "Silicon Labs CP210x USB to UART Bridge" is displayed. Note the COM port (e.g. COM3) for the next step.

    > The Device Manager interface may vary depending on the Windows OS version.

11. Set the `BASE_DIR` and `UPLOAD_PORT` environment variables to your `%USERPROFILE%` directory and device COM port:

    ```
    set BASE_DIR=%USERPROFILE%
    set UPLOAD_PORT=COM3
    ```

12. Edit the system `PATH` environment variable to include the `cygwin\bin` directory:

    ```
    %BASE_DIR%\esp\cygwin\bin
    ```

13. Launch the "Developer Command Prompt for VS 2017" command line console. Verify the setup by building `helloworld` for the `esp` target:

    ```
    cd %MODDABLE%\examples\helloworld
    mcconfig -d -m -p esp
    ```

    For Moddable Zero applications that rely on the screen or use I2C pins, build for the `esp` target with the `moddable_zero` subplatform.

    ```
    cd %MODDABLE%\examples\piu\balls
    mcconfig -d -m -p esp/moddable_zero
    ```

<a id="esp32-windows"></a>

### ESP32 setup

1. Complete ["Host environment setup"](#host-windows) for Windows.

2. Download and install the Silicon Labs [CP210x USB to UART VCP driver](https://www.silabs.com/products/development-tools/software/usb-to-uart-bridge-vcp-drivers).

3. Download the Espressif [all-in-one Windows toolchain and MSYS2](https://dl.espressif.com/dl/esp32_win32_msys2_environment_and_toolchain-20181001.zip) zip archive. Copy the extracted `msys32` directory into your home `%USERPROFILE%` directory, e.g. `C:\Users\<your-user-name>\msys32`.

4. Open a MSYS2 MINGW32 terminal window from a Windows command line console:

   ```
   %USERPROFILE%\msys32\mingw32.exe
   ```

5. From the MINGW32 terminal window, create an `esp` directory in the home `~` directory:

   ```
   mkdir ~/esp
   ```

6. Clone the `ESP-IDF` GitHub repository into the `~/esp` directory. Make sure to specify the `--recursive` option:

   ```
   cd ~/esp
   git clone --recursive https://github.com/espressif/esp-idf.git
   ```

   Use the v3.1 version:

   ```
   cd esp-idf
   git checkout release/v3.1
   git submodule update
   ```

7. Create a new script file `esp32_moddable.sh` in your `%USERPROFILE%\msys32\etc\profile.d\` directory. Add an export command for the ESP-IDF path to the script file:

   ```
   export IDF_PATH="C:/Users/<your-user-name>/msys32/home/<your-user-name>/esp/esp-idf"
   ```

8. Connect the ESP32 to your computer with a USB cable.

9. Launch the Windows Device Manager, open the "Ports (COM & LPT)" section, and verify the "Silicon Labs CP210x USB to UART Bridge" is displayed. Note the COM port (e.g. COM3) for the next step.

   > The Device Manager interface may vary depending on the Windows OS version.

10. Set the `BASE_DIR`, `UPLOAD_PORT` and `SERIAL2XSBUG` Windows environment variables to your `%USERPROFILE%` directory, device COM port and serial2xsbug.exe tool path. Note that forward slashes are required in the tool path:

    ```
    set BASE_DIR=%USERPROFILE%
    set UPLOAD_PORT=COM3
    set SERIAL2XSBUG=/c/Users/<your-user-name>/Projects/moddable/build/bin/win/release/serial2xsbug.exe
    ```

11. Launch the "Developer Command Prompt for VS 2017" command line console. Verify the setup by building `helloworld` for the `esp32` target:

    ```
    cd %MODDABLE%\examples\helloworld
    mcconfig -d -m -p esp32
    ```

    For ESP32-based Moddable Zero applications that rely on the screen or use I2C pins, build for the `esp32` target with the `moddable_zero` subplatform.

    ```
    cd %MODDABLE%\examples\piu\balls
    mcconfig -d -m -p esp32/moddable_zero
    ```

    > The mcconfig tool launches a MINGW32 shell to configure the ESP32 firmware build. After this configuration completes, the MINGW32 shell closes and control is returned back to the Developer Command Prompt. Press any key to complete the build and Flash the binary to the device. Another MINGW32 shell opens to complete the build.

    > Note that the first time you build an application for the ESP32 target, the toolchain may prompt you to enter configuration options. If this happens, accept the defaults.

<a id="linux"></a>

## Linux

<a id="host-linux"></a>

### Host environment setup

> The Moddable SDK has been tested on the Ubuntu 16.04 LTS (64-bit) and Raspberry Pi Desktop (32-bit) operating systems. These setup instructions assume that a GCC toolchain has already been installed.

1. Install the development version of the GTK+ 3 library:

   ```
   sudo apt-get install libgtk-3-dev
   ```

2. Create a `Projects` directory in your home directory at `~/Projects` for the Moddable SDK repository.

   > Note: The Moddable SDK repository can be downloaded to any directory. These setup instructions assume the Moddable SDK is downloaded to the `~/Projects` directory.

3. Download the [Moddable repository](https://github.com/Moddable-OpenSource/moddable), or use the `git` command line tool as follows:

   ```
   cd ~/Projects
   git clone https://github.com/Moddable-OpenSource/moddable
   ```

4. Setup the `MODDABLE` environment variable in your `~/.bashrc` file to point at your local Moddable SDK repository directory:

   ```
   MODDABLE=~/Projects/moddable
   export MODDABLE
   ```

5. Build the Moddable command line tools, simulator, and debugger from the command line:

   ```
   cd $MODDABLE/build/makefiles/lin
   make
   ```

6. Update the `PATH` environment variable in your `~/.bashrc` to include the tools directory:

   ```
   export PATH=$PATH:$MODDABLE/build/bin/lin/release
   ```

7. Install the Screen Test desktop simulator and xsbug debugger applications:

   ```
   cd $MODDABLE/build/makefiles/lin
   make install
   ```

   When prompted, enter your `sudo` password to copy the application's desktop, executable and icon files into the standard `/usr/share/applications`, `/usr/bin`, and `/usr/share/icon/hicolor` directories.

8. Launch the xsbug debugger:

   ```
   xsbug
   ```

9. Verify the host environment setup by building the starter `helloworld` application for the desktop simulator target:

   ```
   cd $MODDABLE/examples/helloworld
   mcconfig -d -m -p lin
   ```

<a id="esp8266-linux"></a>

### ESP8266 (Moddable Zero) setup

1. Complete ["Host environment setup"](#host-linux) for Linux.

2. Create an `esp` directory in your home directory at `~/esp` for required third party SDKs and tools.

3. Download and untar the [Xtensa lx106 architecture GCC toolchain](http://www.moddable.tech/private/esp8266.toolchain.linux.tgz). Copy the `toolchain` directory into the `~/esp` directory.

4. Download the [ESP8266 core for Arduino repository](https://github.com/esp8266/Arduino/releases/download/2.3.0/esp8266-2.3.0.zip). Copy the extracted `esp8266-2.3.0` folder into your `~/esp` directory.

5. Clone the [ESP8266 SDK based on FreeRTOS](https://github.com/espressif/ESP8266_RTOS_SDK) repository into the `~/esp` directory:

   ```
   cd ~/esp
   git clone https://github.com/espressif/ESP8266_RTOS_SDK.git
   ```

   We need the v3.0rc1 version:

   ```
   cd ESP8266_RTOS_SDK
   git checkout v3.0-rc1
   ```

6. Connect the ESP8266 to your computer with a USB cable.

7. Verify the setup by building `helloworld` for the `esp` target:

   ```
   cd $MODDABLE/examples/helloworld
   mcconfig -d -m -p esp
   ```

   For Moddable Zero applications that rely on the screen or use I2C pins, build for the `esp` target with the `moddable_zero` subplatform.

   ```
   cd $MODDABLE/examples/piu/balls
   mcconfig -d -m -p esp/moddable_zero
   ```

   > The ESP8266 communicates with the Linux host via the ttyUSB0 device. On Ubuntu Linux the ttyUSB0 device is owned by the `dialout` group. If you get a **permission denied error** when flashing the ESP8266, add your user to the `dialout` group:

   ```
   sudo adduser <username> dialout
   sudo reboot
   ```

<a id="esp32-linux"></a>

### ESP32 setup

1. Complete ["Host environment setup"](#host-linux) for Linux.

2. Create an `esp32` directory in your home directory at `~/esp32` for required third party SDKs and tools.

3. Download and untar the [64-bit](https://dl.espressif.com/dl/xtensa-esp32-elf-linux64-1.22.0-80-g6c4433a-5.2.0.tar.gz) or [32-bit](https://dl.espressif.com/dl/xtensa-esp32-elf-linux32-1.22.0-80-g6c4433a-5.2.0.tar.gz) ESP32 GCC toolchain compatible with your Linux host. Copy the extracted `xtensa-esp32-elf` directory into your `~/esp32` directory.

4. Clone the `ESP-IDF` GitHub repository into your `~/esp32` directory. Make sure to specify the `--recursive` option:

   ```
   cd ~/esp32
   git clone --recursive https://github.com/espressif/esp-idf.git
   ```

   Use the v3.1 version:

   ```
   cd esp-idf
   git checkout release/v3.1
   git submodule update
   ```

5. Install the packages required to compile with the `ESP-IDF`:

   ```
   sudo apt-get install gcc git wget make libncurses-dev flex bison gperf python python-pip python-setuptools python-serial
   ```

6. Set the `IDF_PATH` environment variable in your `~/.bashrc` to the `esp-idf` directory:

   ```
   IDF_PATH=~/esp32/esp-idf
   export IDF_PATH
   ```

7. Install the Python `pip` package management system:

   ```
   cd ~/esp32
   sudo easy_install pip
   ```

8. Install the required Python packages:

   ```
   python -m pip install --user -r $IDF_PATH/docs/requirements.txt
   ```

9. Update the `PATH` environment variable in your `~/.bashrc` to include the toolchain directory:

   ```
   export PATH=$PATH:$HOME/esp32/xtensa-esp32-elf/bin
   ```

10. Connect the ESP32 device to your Linux host with a USB cable.

11. Determine the USB device path used by the ESP32 device, e.g. `/dev/ttyUSB0`:

    ```
    ls /dev
    ```

12. Set the `UPLOAD_PORT` environment variable in your `~/.bashrc` to the ESP32 serial port:

    ```
    UPLOAD_PORT=/dev/ttyUSB0
    export UPLOAD_PORT
    ```

13. Verify the setup by building `helloworld` for the `esp32` target:

    ```
    cd $MODDABLE/examples/helloworld
    mcconfig -d -m -p esp32
    ```

    For ESP32-based Moddable Zero applications that rely on the screen or use I2C pins, build for the `esp32` target with the `moddable_zero` subplatform.

    ```
    cd ${MODDABLE}/examples/piu/balls
    mcconfig -d -m -p esp32/moddable_zero
    ```

    > The ESP32 communicates with the Linux host via the ttyUSB0 device. On Ubuntu Linux the ttyUSB0 device is owned by the `dialout` group. If you get a **permission denied error** when flashing the ESP32, add your user to the `dialout` group:

    ```
    sudo adduser <username> dialout
    sudo reboot
    ```

    > Note that the first time you build an application for the ESP32 target, the toolchain may prompt you to enter configuration options. If this happens, accept the defaults.

<a id="debugging-applications"></a>

## Debugging applications

The `xsbug` JavaScript source level debugger is built as part of the Moddable SDK build described above. `xsbug` is a full featured debugger that supports debugging modules and applications for [XS platforms](xs/XS%20Platforms.md). The `xsbug` debugger is automatically launched when deploying debug builds and connects to devices via USB or over Wi-Fi. Similar to other debuggers, `xsbug` supports setting breakpoints, browsing source code, the call stack and variables. The `xsbug` debugger additionally provides real-time instrumentation to track memory usage and profile application and resource consumption.

For additional details on `xsbug` please refer to the [xsbug](xs/xsbug.md) document.

<a id="arduino-version"></a>

## ESP8266 Arduino version 2.4

The Moddable SDK on ESP8266 is hosted by the [ESP8266 core for Arduino](https://github.com/esp8266/Arduino). The Moddable SDK uses version 2.3. Version 2.4 is supported as well. At this time, we do not recommend using version 2.4 as it requires more ROM and more RAM without providing significant benefits for most uses of the Moddable SDK. The team responsible for ESP8266 core for Arduino is aware of [these](https://github.com/esp8266/Arduino/issues/3740) [issues](https://github.com/esp8266/Arduino/issues/4089) and actively working to address them.

You can use version 2.4 today if building on macOS or Linux. Follow the instructions above, but use the [version 2.4](https://github.com/esp8266/Arduino/releases/download/2.4.0/esp8266-2.4.0.zip) of ESP8266 Core for Arduino. Next, in `{MODDABLE}/tools/mcconfig/make.esp.mk` change `ESP_SDK_RELEASE ?= esp8266-2.3.0` to `ESP_SDK_RELEASE ?= esp8266-2.4.0`. Finally, delete the contents of \$`{MODDABLE}/build/bin/esp/` and `${MODDABLE}/build/tmp/esp/` and build as above.
