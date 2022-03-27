# ESP32-CAM

## Install FTDI usb driver

_2022-01-09_


### Linux and CH340

Source [Olimex blog](https://olimex.wordpress.com/2020/06/05/how-to-configure-and-use-esp32-cam-with-arduino-ide-and-linux/)

This driver is needed for connecting to ESP32-CAM via USB.

CH340 Linux drivers has bad PLL settings for all Linux kernels before 5.5.

If your system happen to be with Linux Kernel before 5.5. here is the GitHub repository with the patch to install.

If you do not have this patch **CH340 will work**, but will not be able to communicate at speed over 115200 bps, with the patch up to 2Mbps communication is possible.

Start checking which version of Linux you have:

    uname -srm
    Linux 5.4.0-92-generic x86_64

The patch is needed for linux kernel < 5.5, this is the case of my Linux.

### Updating the USB driver

Install dkms tool:

    sudo apt install dkms

Clone the repo from
https://github.com/OLIMEX/ch340-dkms

    git clone https://github.com/OLIMEX/ch340-dkms.git
    cd ch340-dkms
    sudo dkms build .
    sudo dkms install ch340/1.0.0

open this file and add the default driver to the black list

    sudo nano /etc/modprobe.d/dkms.conf

add to the file the following line at the bottom

    blacklist ch341

## Setup ESP32-CAM

Open Arduino IDE and add the sources to download the examples and drivers for the ESP32. Go to the menu Files/Preferences and add in _Additional Boards Manager URLs_ the following address:

https://dl.espressif.com/dl/package_esp32_index.json

If you also have EPS8266, here you can add also the index for that one:
https://arduino.esp8266.com/stable/package_esp8266com_index.json

Then go to _Tools/Boards/Boards Manager_ and search for `ESP32` by `Espressif Systems` and install the package.

Now we need to know the exact model of the ESP32-CAM. 
Mine is `AI-Thinker`.

If you don't know, try to recognize it from the list in following post.
https://randomnerdtutorials.com/esp32-cam-camera-pin-gpios/

Setup your board:
- connect your ESP32-cam to the USB of your computer
  
running a short query you should see the port `ttyUSB0`

Note: if the USB does not show up, try another USB port.

    ls -l /dev/ttyUSB*
    crw-rw----+ 1 root dialout 188, 0 Jan  9 12:16 /dev/ttyUSB0


- select the board: _Tools/boards/ESP32 WROVER Module_ 
- select the port: _/dev/ttyUSB0_ 
- enable the `PSRAM` if available via the menu: 
_Tools/PSRAM -> Enabled_

# Building with Arduino IDE

When I tried to compiler an example for ESP32 I've got the following build error:

    File "../tools/esptool.py", line 26, in
    import serial
    ImportError: No module named serial

I was checking if the module `serial` was installed.


    pip list | grep serial
    Package               Version             
    --------------------- --------------------
    pyserial              3.5                 
    serial                0.0.97 

but then I realized that `pip3` also was returning the same result, and `esptool` requires `python3`.

First I checked whether there were two installation for python 2.7 and 3.

    which python
    /usr/bin/python

    which python3
    /usr/bin/python3

The paths are different, this I tried to import the module serial with `python3`, the one that `esptool` should be using

    $ python3
    Python 3.8.10 (default, Nov 26 2021, 20:14:08) 
    [GCC 9.3.0] on linux
    Type "help", "copyright", "credits" or "license" for more information.
    >>> import serial
    >>> 

this worked, then I tried with Python 2.7, and here is were I figured that esptool somehow was being called with python2 instead of python 3.

    $ python
    Python 2.7.18 (default, Mar  8 2021, 13:02:45) 
    [GCC 9.3.0] on linux2
    Type "help", "copyright", "credits" or "license" for more information.
    >>> import serial
    Traceback (most recent call last):
    File "<stdin>", line 1, in <module>
    ImportError: No module named serial
    >>> 

A quick hack to this is to change platform configuration, this is under the user space.

    /home/snake/.arduino15/packages/esp32/hardware/esp32/1.0.6/platform.txt

Since I know that Arduino requires Python3, I searched and replaced the 4 instaces of the word "python" with "python3"

here are the 4 instaces in `platform.txt`:

    tools.esptool_py.network_cmd=python3 "{runtime.platform.path}/tools/espota.py"
...

    tools.gen_esp32part.cmd=python3 "{runtime.platform.path}/tools/gen_esp32part.py"
...

    recipe.objcopy.bin.pattern.linux=python3 "{tools.esptool_py.path}/{tools.esptool_py.cmd}" --chip esp32 elf2image --flash_mode "{build.flash_mode}" --flash_freq "{build.flash_freq}" --flash_size "{build.flash_size}" -o "{build.path}/{build.project_name}.bin" "{build.path}/{build.project_name}.elf"
...

    tools.esptool_py.upload.pattern.linux=python3 "{path}/{cmd}" --chip esp32 --port "{serial.port}" --baud {upload.speed}  --before default_reset --after hard_reset write_flash -z --flash_mode {build.flash_mode} --flash_freq {build.flash_freq} --flash_size detect 0xe000 "{runtime.platform.path}/tools/partitions/boot_app0.bin" 0x1000 "{runtime.platform.path}/tools/sdk/bin/bootloader_{build.boot}_{build.flash_freq}.bin" 0x10000 "{build.path}/{build.project_name}.bin" 0x8000 "{build.path}/{build.project_name}.partitions.bin"
...