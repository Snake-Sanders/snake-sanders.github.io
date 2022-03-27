# Reinstalling MacbookPro SD card driver on Linux

_2022-03-27_

Continuing my small battles with Linux Mint on MacbookPro, I recently let _Update Manager_ to do its thing, I clicked on _Install updates_ and afterwards I noticed the embedded SD card reader did not recognize my card.

I did a quick search and I found a solution for Ubuntu that worked for many but not for me:

1. Reinstall package udisks2

```
sudo apt-get install --reinstall udisks2
```

2. Reboot the machine

```
systemctl reboot
```

Reference: https://askubuntu.com/a/790308/175325

## Reinstall the FaceTimeHD camera

After thinking for a while, I was wonder if this update also affected the **FaceTimeHD** camera, and yes, in deed. It seems that the software update wipes out the drives I have installed manually.
Anyway, I am not sure how this relates but reinstalling the FaceTimeHD camera also solved my issues with the SD card drivers.

Here is the link of a previous post on how to install the FaceTimeHD camera on Linux Mint.

[Install MacbookPro Facetime HD camera on Linux](../2022-01-09-install-macbookpro-facetime-hd-camera-on-linux/notes.md)