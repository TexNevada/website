---
title: "How to fix: Lost ethernet on Proxmox after GPU install!"
date: 2024-04-08T20:03:00+02:00
description: Ever lost ethernet on Proxmox after a GPU install? So have I. That's why I wrote this guide so you can easily fix it as well.
author: Tex Nevada
authorEmoji: 
draft: false
pinned: false
tags:
  - Proxmox
  - Debian
categories:
  - Linux
  - Virtualization
  - Tech
series:
  - Tech Guides
image: https://cdn.edb.tools/edbTools/2024/Proxmox-ethernet-fix/Proxmox.png
---
# Introduction
I run a homelab at home and tend to play around with a lot of different setups. Be it gaming, docker, Ubuntu or have you. Recently in the last 6 months. I've been running Proxmox with a Windows VM for a dev lab. 

This gave me the idea that I could use my old GPU for it as a passthrough device! However I noticed when I added my old GPU (GTX 980 Ti) that the network card didn't work properly. After some research I found out why. 

It turns out that the ethernet port isn't assign a static name but rather uses a dynamic name. The dynamic name is decided based on what PCIe devices are plugged in. The GPU just so happened to grab ID 1. Bumping up the network card one number. This of course changed the ethernet port's name in Proxmox.

This causes issues with Proxmox as the VM bridge (vmbr0) is assigned to the dynamic name's label. The label being static in the config. 

# How we can slove the problem
Create this file:  `/etc/systemd/network/90-eth0.link` with the name `90-eth0.link` being important. Keep this in mind later. 

Now edit the file using your favorite editor. Be it `Nano` or `vi` like so: `vi /etc/systemd/network/90-eth0.link`

Add the following in the file's content:
_Change the MAC address field to what the MAC address is on to your ethernet adapter._
```
[Match]
MACAddress=00:11:22:33:44:55:66

[Link]
Name=eth0
```

As you see. We are naming the new interface `eth0` here. Proxmox doesn't do this compared to a lot of other Linux/Unix distributions. 

Now we go to: `/etc/network/interfaces` using your favorite editor again.
Edit the following line `iface eth0 inet static` where `eth0` is would be a different name for you.

Here I've already made the changes. where `eth0` is name we created before.
```
auto lo
iface lo inet loopback

iface eth0 inet static

auto vmbr0
iface vmbr0 inet static 
		address 192.168.1.100/24
		address 192.168.1.1
		bridge-ports eth0
		bridge-stp off
		bridge-fd 0

iface wlp0s0 inet manual
```
This config file might not be 100% similar to yours. My server just happened to have a wireless card due to it being built with Desktop parts.

Save it all and reboot the machine. Now it should work as normal! Any new PCIe devices should installed without interference. 