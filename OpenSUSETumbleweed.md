---
layout: default
title: "OpenSUSE Tumbleweed"
---

{% include Computing_nav.html %}

> ## Warning
> I do not longer recommend using OpenSUSE. Having exclusively used OpenSUSE since 2007, I recently had more and more stuff breaking and lousy support.
> I leave this for anybody whom this might help

- [Preparation](./OpenSUSETumbleweed#preparation)
- [Actual installation](./OpenSUSETumbleweed#actual-installation)
- [First steps to get stuff working](./OpenSUSETumbleweed#first-steps-to-get-stuff-working)
- [Packman Repository](./OpenSUSETumbleweed#packman-repository)
- [C++ / Qt Delevelopment](./OpenSUSETumbleweed#c--qt-delevelopment)

# OpenSUSE Tumbleweed tips and tricks

A collection of lessons I learned when installing and using OpenSUSE Tumbleweed. This will also serve as the instructions for another installation.

## Preparation

You will need to create a bootable USB drive with OpenSUSE Tumbleweed on it. Find instructions here: [openSUSE Tumbleweed](https://get.opensuse.org/tumbleweed/)

Some information should be gathered and some decisions taken before any installation. When installing on a machine with an existing operating system, additional information might be useful to gather.
- How do you select a boot device on your machine?
- Screen resolution
- Partitions. I would suggest something like
    - Partition on the boot disk (BIOS or EFI boot partition, not mounted)
        - Several MB for BIOS
        - 100 to 550 MB for EFI
    - 10 GB swap partition
    - 100 GB root partition
    - 100 GB home partition
    - The rest as a data partition
- Wifi access information. You can install without network access or connected via cable, as well.
- Connect all USB accessories you might want to use. The installation process will then install the needed software during the installation process. That might be much easier then doing it later.

When installing on a machine with an existing operating system you should gather information about existing partitions to mount them in Tumbleweed. Assuming Linux is already installed on the machine issue:
```
df
```
to find out about existing partitions and there mount points. Using 
```
sudo fdisk -l
```
will provide much more information.

The you had another version of openSUSE Linux installed before, you can dump information about used repositories using:
```
zypper lr -U
```

Using the following command allows to dump all installed packages to a text file:
```
zypper se -s --installed-only > file
```

## Actual installation

This is actually quite simple and just takes a while. At the end, you will have a machine with the most necessary software already installed. I will only provide the main points here. There are instructions with screen shots:
[How to install OpenSUSE](https://linuxhint.com/install_opensuse/), [Install openSUSE](https://landoflinux.com/linux_install_opensuse_tumbleweed.html), [How to install openSUSE on your PC](https://www.fosslinux.com/44232/how-to-install-opensuse-on-your-pc.htm)

Here are the steps I followed to install on my Desktop. I only show information that has to be adapted, leave the rest on default:
- Installation from USB Stick (Kingston DataTraveler 8Gb 3G)
    - Select with F11 boot menu
- Boot with Language (F2) = Deutsch
- Screen = 1920x1080
- Wireless DHCP SSID: WPA-PSK Password
- Activate Main and update repositories, Source and Debug
- KDE Plasma
- Adapt the Partitiontable (see above)
- If you have an old OpenSUSE version:  SSH-key from openSUSE 15.1
- Only applicable for BIOS boot: Install Grup into MBR 
- Open SSH, start Firewall and SSH
- Leave the rest running. This will take a couple of hours.

Things I noticed after install:
- Auto login does not work, might be better
- Deactivate the USB drive repository (otherwise you get warnings all the time)
    - Launcher / System / YaST software / Configuration / Repositories: Disable openSUSE-20210111-0
- After reboot (on a machine with another OpenSUSE version on another boot drive): Need to change the sequence of hard drives in Bios and the correct hard drive to boot from is selected
    - F11 and select WDC-WD5000AAKS-0 for openSUSE_Leap_15.1
    - F11 and select WDC-WD10EZ for openSUSE_Tumbleweed
- Manage Network connection with network manager (bottom right in the status bar)

## First steps to get stuff working

### Printing and scanning with a HP network laser (HP Color LaserJet MFP M277dw)

```
sudo zypper in hplip
```

**Deactivate firewall in YaST**
As root:
```
  hp-setup
```
- Discovery in Network
- download on console
- Crtl-C and start again

```
hp-plugin
```
- Specify a path to the plug-in (download did not work)
- You need to provide a path to hplip-3.20.11-plugin.run

```
zypper in hplip-scan-utils

```

As a normal user:

**Activate firewall in YaST**

Use YaST for the setup of printer and scanner
- Printer (the printer is shown now)
    - Setup printer
        - Papertype A4
        - All Options for the Current Driver
            - HP-Duplex Option: true
            - DuplexNoTumble
- Scanner (the scanner is shown now)
    - Driver: hpaio

You could scan from the command line using
```
scanimage -d hpaio:/net/HP_Color_LaserJet_MFP_M277dw?ip=192.168.178.25 -v
```
Better use Applications Launcher / Grpahics / Skanlite

Wenn printing:
- Options / Setting / Duplex / long side

### Hostname

sudo hostnamectl set-hostname NewHostname

### KDEConnect, keepassxc, nextcloud, gimp, virtualbox and SSH

```
sudo zypper in kdeconnect-kde keepassxc nextcloud-desktop gimp virtualbox kdiff3 encfs
sudo systemctl enable sshd
sudo systemctl start sshd
```
In the status bar I get the option to Install KDE-Connect Firefox extension. Not in private windows and you need to run a server for that. Copying links is easy enough without it. Encfs allows to encrypt part of the file system.

Yast / Firewall / Device: wlp1s8 -> public
- add kdeconnect-kde and shh to zone public

On the android device install kdeconnect
Smartphone / kdeconnect / Add new device (left) / Update (right)

List kdeconnect devices:
```
kdeconnect-cli -l
```
Send notification to smartphone (id taken from command output above):
```
kdeconnect-cli --ping-msg "hello!" -d id
```

Start Nextcloud Desktop client and create accounts

Attach Wacom tablet
- Start gimp
    - with the pen select a dynamic for the brush
    
Correct /etc/fstab entry for /windows/D: user,owner,utf8,rw,umask=0

Add user to vboxusers and install Virtual box extensions (download form their webpage):
```
sudo grep vboxusers /etc/group
sudo /usr/sbin/usermod -a -G vboxusers username
  
sudo VBoxManage extpack install .../Oracle_VM_VirtualBox_Extension_Pack-6.1.18.vbox-extpack 
```

Applications Launcher / System / Virtual Machine
- To add an existing machine
    - Machine / Add / .../Windows8.vbox
- In the machine mount the extension Iso and install

### RTL8822bu USB WLAN dongle

```
sudo zypper addrepo https://download.opensuse.org/repositories/home:Sauerland:hardware/openSUSE_Tumbleweed/home:Sauerland:hardware.repo
sudo zypper refresh
sudo zypper in rtl8822bu-kmp-default
reboot
```
Manage Network connection with network manager (bottom right in the status bar). Will show an additional device

### My favorite KDE setting

```
sudo zypper in myspell-en_GB
```
- 4 virtual desktops
- Right click status bar / Add Widgets / Get New Widgets / Download New Plasma widgets
    - System Load Viewer mini program
    - Shutdown mini programms
- Blank screen saver with lock on Desktop
- Applications Launcher / Settings / System settings / Window behaviour / Window behaviour
    - Focus follows mouse
    - Delay focus by 300 ms
    - Raise on hover, delayed by: 750 ms
- Applications Launcher / Settings / System settings / Appearance / Window Decorations
    - Window border size: Normal
- Applications Launcher / Settings / System settings / Regional Settings / Language
    - Add top British English (Default)
- Applications Launcher / Settings / System settings / Regional Settings / Spell Check
    - Default Language: Deutsch
    - Active languages: American and British English, Deutsch
    - Enable autodetection of language
    - Automatic spell checking enabled by default
- Applications Launcher / Settings / System settings / Notifications
    - Applications: Setup tone for KDEMail, KDE Connect, ...
- Dolphin / Settings
    - Show Menubar (Ctrl+M) 
- Dolphin / Settings / Configure Settings
    - Startup / Make location bar editable

    ### Additional packages

```
sudo zypper in gimp-gap gimp-plugin-heif gimp-ufraw ufraw fortune inkscape inkscape-extensions-extra tvbrowser ispell-british words-british fetchmsttfonts libreoffice-l10n-en_GB zip oyranos argyllcms kolor-manager telnet
```

### Emacs

```
sudo zypper in emacs emacs-auctex emacs-el emacs-w3m emacs-gnuplot-mode
```

### LaTeX

emacs-auctex will pull most of texlive
```
sudo zypper in texlive-cjk ttf-arphic texlive-svg-inkscape texlive-wallpaper texlive-datatool
```

### Android Tools

```
sudo zypper addrepo https://download.opensuse.org/repositories/hardware/openSUSE_Tumbleweed/hardware.repo
sudo zypper refresh
sudo zypper install android-tools
```

### HEIF/HEIC images

```
sudo zypper in gimp-plugin-heif heif-examples

for file in *.HEIC; do heif-convert $file ${file/%.HEIC/.png}; done
```

## Packman Repository

Packman is a repository with software (software versions) that OpenSUSE cannot provide.

```
sudo zypper ar -cfp 90 https://ftp.gwdg.de/pub/linux/misc/packman/suse/openSUSE_Tumbleweed/ packman
sudo zypper dup --from packman --allow-vendor-change
sudo zypper in vivaldi smplayer mplayer youtube-dl smtube
sudo zypper in akonadiconsole
```

## C++ / Qt Delevelopment

```
sudo zypper in -t pattern devel_C_C++ devel_qt5
sudo zypper in autogen c_count cmake-gui colormake cppcheck cppcheck-gui cscope ctags doxygen doxywizard lcov
sudo zypper in libical-devel qwt6-devel
```
Install a source packge for development, libical in this example
```
sudo zypper si libical
> A src package is not really "installed".
> It's just downloaded, and its contents extracted.
>
> You should find all the files in ~/rpmbuild/ (if you ran zypper as user)
> or /usr/src/packages/ (if you ran zypper as root).
```

