---
layout: default
title: "Raspberry Pi"
---

{% include Computing_nav.html %}

  - [Prepare the SD card](./RaspberryPi#prepare-the-sd-card)
  - [First boot](./RaspberryPi#first-boot)
  - [Basic setup, adding user and securing SSH](./RaspberryPi#basic-setup-adding-user-and-securing-ssh)

# Raspberry Pi

Tips to install a Raspberry Pi 4 $GB RAM based server. This will server emails and my own cloud services.

## Prepare the SD card:

[Installing images on Linux](https://www.raspberrypi.org/documentation/installation/installing-images/linux.md)
Download Raspberry Pi OS Lite from 
[raspberry-pi-os-32-bit](https://www.raspberrypi.org/software/operating-systems/#raspberry-pi-os-32-bit)
Move to /var/Data/RaspPi
```
cd /var/Data/RaspPi
unzip 2021-03-04-raspios-buster-armhf-lite.zip
```

```
sudo dd if=2021-03-04-raspios-buster-armhf-lite.img of=/dev/sdf bs=4M conv=fsync
445+0 records in
445+0 records out
1866465280 bytes (1,9 GB, 1,7 GiB) copied, 207,3 s, 9,0 MB/s
```

To enable headless mode with SSH access:
Mount boot partition

```
touch /run/media/ukoehler/boot/ssh
Umount partition
sync
```
remove SD card

## First boot

On another device open [my router](https://192.168.178.1/) and check available devices
There only is odroid at 192.168.178.27 using cable connection

Insert SD card into RaspPi
Connect network cable
Connect power supply

Fritzbox shows a new device: raspberrypi at 192.168.178.29 with 1 Gbit/s

On my openSUSE Tumbleweed machine open a terminal
```
ssh pi@raspberrypi
```
- accept the key
-initial password for pi is raspberry

## Basic setup, adding user and securing SSH

In a SSH connection to the RaspPi from my desktop:
```
sudo nano /etc/hostname
```
- change raspberrypi to pi

```
sudo nano /etc/hosts
```
- change raspberrypi to pi

```
sudo reboot
```
Fritzbox shows the device: pi at 192.168.178.29 with 1 Gbit/s

```
ssh pi@pi
```
- accept the key
- initial password for pi is raspberry

```
sudo raspi-config
```
- 6 - A1

```
reboot
```

Change password for root user
```
sudo passwd
```
    
Change password for pi user
```
passwd
```

This command will update your system’s package list:
```
sudo apt-get update
```

This command will upgrade all installed packages to the newest version
```
sudo apt-get upgrade
```

Set the timezone
```
sudo timedatectl set-timezone Europe/Berlin
date
timedatectl
```

Add a user:
```
sudo adduser --uid 1001 username
```

Reset password for user
```
sudo passwd user
```

Copy SSH setup from odroid to RaspPi to enable rsync
Use fish://ukoehler@odroid/home/ukoehler/ and fish://ukoehler@pi/home/ukoehler/
in Dolphin in openSUSE Tumbleweed to copy .ssh folder
ssh ukoehler@pi now works without password
Use fish://mkoehler@odroid/home/mkoehler/ and fish://mkoehler@pi/home/mkoehler/

From scratch this would be
    From desktop
    (enable easy copying of data from plug)
    ssh-keygen -t rsa
    ssh-copy-id -i ~/.ssh/id_rsa.pub ukoehler@scholandpi
    ssh-copy-id -i ~/.ssh/id_rsa.pub mkoehler@scholandpi

    On Tablet and Mobile
    VX ConnectBot
        Menu / Manage Public Keys
        Longpress key and export
    Amaze
        Rename key and move to owncloud
    Owncloud upload key

To allow rsync from odroid to pi we need to install public ssh keys on odroid
First check that there are no *.pub files in ~/.ssh on pi
Create a private/public key pair for ukoehler and mkoehler
ssh-keygen -t rsa -b 4096
Use fish (see above) to copy /home/ukoehler/.ssh/id_rsa.pub to odroid (rename id_rsa_pi.pub)
Add the contents of the file to authorized_keys

Need to update ssh on odroid (and create new keys for my openSUSE machines)
sudo apt-get install --only-upgrade ssh
sudo service ssh restart

sudo nano /etc/ssh/sshd_config
  PermitRootLogin no 
  PasswordAuthentication no
sudo service ssh restart

sudo visudo
  add ukoehler ALL=(ALL) ALL
  comment #%sudo  ALL=(ALL:ALL) ALL

sudo apt-get install fortune

As ukoehler
nano ~/.bashrc
  force_color_prompt=yes
  add at the end
  alias fortune="echo -e '\033[31m'; /usr/games/fortune; echo -e '\033[30m'"
  if [ -x /usr/games/fortune ] ; then
        echo
        fortune
        echo
  fi
