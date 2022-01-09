# Install MacbookPro Facetime HD camera on Linux

_2022-01-09_

There is a good guide on how to install the front camera of MacBookPro on Linux here:

https://github.com/patjak/facetimehd/wiki/Installation#get-started-on-debian

For Linux Mint I follow the Debian section.

    git clone https://github.com/patjak/bcwc_pcie.git
    cd bcwc_pcie
    make clean
    make
    sudo checkinstall

> Note: here in `checkinstall` say `no` to everything, we want to include all the local files in the release package.

    sudo depmod
    sudo modprobe facetimehd
    # echo facetimehd >> /etc/modules

## GitHub troubleshooting

I had some issues after installing the camera and I could not re-install it again. I left my comments on Github about the issue and how to solve it.

https://github.com/patjak/facetimehd-firmware/issues/8

Extract of my comment about the re-installation issue.

I also recently have this issue, it was working few weeks ago.

```bash
sudo modprobe facetimehd
modprobe: FATAL: Module facetimehd not found in directory /lib/modules/5.4.0-88-generic
``` 
I am running a Linux Mint Cinnamon

```bash
cat /etc/issue
Linux Mint 20.2 Uma \n \l
```

After trying to re-install everything I noticed that the driver works fine, it was not a system update issue, the only problem is that after reboot the module goes missing, and trying to re-install it without a clean enviroment leads to different errors.

Here is the summary of the installation steps, assuming the modules are downloaded already. (For the complete steps I follow this page https://github.com/patjak/facetimehd/wiki/Installation#get-started-on-debian) 

```bash
cd bcwc_pcie
make clean
make
sudo checkinstall
sudo depmod
sudo modprobe facetimehd
sudo su
echo facetimehd >> /etc/modules
exit

```

Here are some steps I have done differently:
1. If `make clean` is not done before installing you might see other issue already reported (Facetimehd 'Exec format error').
2. This time I am not following the recommended options in `checkinstall` and I say I **no** to everything, I want to add the local files in HOME to the release package.

now the camera works but after reset the configuration is lost so I only need to re run in the directory `bcwc_pcie` the commands

```bash
sudo depmod
sudo modprobe facetimehd
```

this is not needed if we add this module to be loaded at startup, as it was mentioned in the troubleshooting section which I forgot to read.

    echo facetimehd >> /etc/modules

This will add the module to be launched at startup.
To verify it, you can see if the module `facetimehd` is at the bottom of the file.

    cat /etc/modules
    # /etc/modules: kernel modules to load at boot time.
    #
    # This file contains the names of kernel modules that should be loaded
    # at boot time, one per line. Lines beginning with "#" are ignored.

    facetimehd
