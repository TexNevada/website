---
author: Tex Nevada
title: "How to update HP Firmware on Linux!"
date: 2023-10-04T10:30:00+02:00
description: An easy guide to updating HP Firmware on linux!
draft: false
pinned: false
image: https://cdn.edb.tools/edbTools/2023/2023-update-hp-firmware-on-linux/hp-logo.png
tags:
- HP
- Firmware
- Linux
categories:
- Linux
series:
---

# So how we update HP Firmware on Linux?

HP isn’t exactly known as the most friendliest vendor when it comes to linux client machines be it Software or Firmware but that doesn’t mean there is other ways to install HP firmware on your machine.

This assumes you run your linux distro on UEFI and not legacy BIOS.

If you are not sure which HP module you have exactly. Just run the following. 
```bash
sudo /usr/sbin/dmidecode | grep "Product Name:"
```

In my case I got the HP ZBook
![](https://cdn.edb.tools/edbTools/2023/2023-update-hp-firmware-on-linux/product-name.png)

Copy the entire Product name of your machine and paste it into google with download at the end like so: (Make sure the Model matches up with your result on HP’s website).

![](https://cdn.edb.tools/edbTools/2023/2023-update-hp-firmware-on-linux/Pasted%20image%2020231004093333.png)

Now on HP’s website. Select Windows 10/11 and the latest Windows 10/11 update. We are going to extract the .bin file from the windows 10/11 firmware installer.
![](https://cdn.edb.tools/edbTools/2023/2023-update-hp-firmware-on-linux/Pasted%20image%2020231004093436.png)

Once on the driver page. Go to BIOS Firmware and press download.
Now be sure to ignore the big blue box and press the blue text on the bottom to “install manually“
![](https://cdn.edb.tools/edbTools/2023/2023-update-hp-firmware-on-linux/Pasted%20image%2020231004093609.png)

The download should have now installed a file similarly named `sp000000.exe` where 00000 is the file ID. Don't worry to much about this. Open up your console and go to the directory you downloaded the file in.

Create a new folder named hpfirmware in tmp using 
```bash
mkdir /tmp/hpfirmware
```
 Now move the file to `tmp/hpfirmware` using `mv ./sp000000.exe /tmp/hpfirmware` and cd into it like so: `cd /tmp/hpfirmware`.

Before we continue. We will need 7zip. If you don’t already have it. You can install it like so: 
```bash
sudo apt install 7zip
```


Once we have 7zip. Use the following command inside the `/tmp/hpfirmware` folder: 
`7zz x ./sp000000.exe` replace the 0s with the actual file name.

This should have unpacked your exe file into many files. Now you need to use the root account and do the following commands

`sudo su -l` to log into root. Move the bin file into the device firmware folder like so: 
```bash
mv /tmp/hpfirmware/*.bin /boot/efi/EFI/HP/DEVFW/firmware.bin`
```


All you need to do now is reboot your machine and select the UEFI Firmware Settings in the GRUB menu. If you don’t get the GRUB menu on boot. Press esc on boot. This should bring the menu up.

The device should now boot up into HP’s own menu (not all HP devices get the firmware button if you try going into the BIOS settings but you can try!) you should see a menu entry at the bottom called “Update system and Supported Device Firmware“. This should install the latest firmware we got from the folder we put the .bin folder into!