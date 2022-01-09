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

