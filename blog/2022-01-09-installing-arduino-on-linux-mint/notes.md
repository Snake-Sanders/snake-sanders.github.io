# Installing Arduino on Linux Mint

_2022-01-09_

- [Installing Arduino on Linux Mint](#installing-arduino-on-linux-mint)
- [Environment](#environment)
- [Installation](#installation)
- [USB port](#usb-port)
  - [Listing ports](#listing-ports)
  - [Port access configuration](#port-access-configuration)
- [Arduino IDE](#arduino-ide)
  - [Board setup](#board-setup)
  - [Test the configuration](#test-the-configuration)

# Environment

Linux 5.4.0-92-generic x86_64 on a MacBookPro retinal 2015.

In order to check your enviroment you can type the following command in a terminal: `uname -srm`.

Linux Mint 20.2 Uma.

You can check it with the command `cat /etc/issue`

# Installation

Following the instructions from the currently read-only page of Arduino, it recommends to manually download the software from the link below:

https://www.arduino.cc/en/Main/Software

for installing it, extract the files and open a terminal in the same folder.

    cd ~/Downloads/arduino-1.8.19
    sudo ./install.sh

# USB port

## Listing ports

We want to know if the Arduino USB port is recognized when is connected.
After the board is connected a new port should be listed.

The port list can be printed with the folliwg command.

    ls -l /dev/tty*
    crw-rw-rw-  1 root tty       5,  0 Jan  9 09:59 /dev/tty
    crw--w----  1 root tty       4,  0 Jan  9 08:16 /dev/tty0
    crw--w----  1 root tty       4,  1 Jan  9 08:16 /dev/tty1
    crw--w----  1 root tty       4, 10 Jan  9 08:16 /dev/tty10
    ...

the list is long and we only want to know if there is a new port listed.
So before connecting the board check how many ports are, in my case there are 98:

    ls -l /dev/tty* | wc --line
    98

After connecting the board run the previous command again, and a new port should be added.

    ls -l /dev/tty* | wc --line
    98

Now, this is a typical issue with MacBooks which brough me so much headache. If the port is not shown then try to use the **right-USB port** of your MacBook. Apparently the left-USB port is already in use.

I am using an Arduino Mega 2560

The port for this is `ttyACM0` you can list it with:

    ls -l /dev/ttyA*
    crw-rw----+ 1 root dialout 166,  0 Jan  9 10:08 /dev/ttyACM0

- `crw-`
We are shown a so-called character device (c) owned by user "root", with both read (r) and write (w) permissions granted to "root" owner.

- `rw-`
both read (r) and write (w) permissions granted to members of UNIX-group "dialout".

- `---`
no permissions granted whatsoever to anyone else.

You are supposedly not running Arduino as "root" but as your user, as you should be, so it remains to check said user's membership of "dialout":

after connecting board to the USB, there is a new interface

## Port access configuration
Your use must have read/write permissions on this port, to do that you need to add your user to the `dialout` group.
It can be donw with the following command.

    sudo usermod -a -G dialout $USER

or, similarly with:

    sudo usermod -aG dialout $(whoami)

`$USER` returns the same as the command `whoami`, in my case my user is `snake`.

In order to verify that now your user is in dialout group type the following command: `groups`

    groups
    snake adm dialout cdrom sudo dip plugdev lpadmin sambashare

another way to check it is with the command `id`:

    id
    uid=1000(snake) gid=1000(snake) groups=1000(snake),4(adm),20(dialout),24(cdrom),27(sudo),30(dip),46(plugdev),114(lpadmin),134(sambashare)

This can also be added with the GUI, administration/Users and groups

# Arduino IDE

## Board setup

Open Arduino IDE and setup the board settings.

Go to the menu /tools/boards, and select your variant
Go to the menu /tools/processors, and select your variant's processor
Go to the menu /tools/port, and select the new serial port we configured before: `/dev/ttyACM0`

## Test the configuration

Go to the menu /tools/board info, and you should see a pop up window with some information about your board, such as the serial number.
