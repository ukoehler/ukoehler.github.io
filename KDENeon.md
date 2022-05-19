## Preparation

Install KDE neon 5.23.2
=======================

Create a bootable USB drive. For the old ThinkPad that needs to be the (Kingston DataTraveler 8Gb 3G)

This distribution is installed from a system running live. You can play around a bit before installation. Mind you you
might have to change the keyboard layout in system settings.

Before you start
================

Make sure you have an idea of the available partitions on the system or the partition scheme you want to use.
If you already have a version of Linux on the machine mounting some of the partitions that will get formatted during
installation, make sure to disable the partitions in /etc/fstab before the installation to avoid ending in a system
with emergency mode due to mounting problems


DizietSma
=========

During startup press F12 and select USB drive to boot from the USB stick
Connect the machine to WLan as usual with the network manager (bottom right)
Bootloader will be in sda MBR


Both
====

Start installer
    Deutsch
    Europe/Berlin
    German Keyboard
    / Partition Btrfs
    Select correct bootloader location (see above)


One immediate fix
=================

sudo nano /etc/sysctl.conf
    at the end add "vm.swappiness=10"
reboot


Some checks
===========

Open System Settings → Search → File Search → Disable Also index file content → Apply

System panel
------------
Looks like I have almost all the widgets in the panel I want already. Add Shutdown

KDEConnect
----------
KDEconnect works. Add "Speicherort on the phone and tablet for access via Dolphin
Samsung
    Einstellungen / Apps / Menu / Spezieller Zugriff / Deactivate Akkuverbrauch optimieren for KDEConnect

List kdeconnect devices:
kdeconnect-cli -l
Send notification to smartphone:
kdeconnect-cli --ping-msg "hello!" -d 6413bcde688c2dcd
Send notification to tablet:
kdeconnect-cli --ping-msg "hello!" -d b23b3d1bcd34d601 (KDEConnect must be open on the tablet)

Cannot see SDCard content on Samsung directly, but in Dolphin make the location editable and just append an existing
folder. Seems to be quite slow, though.

USB-Devices
-----------
Neither smartphone nor tablet work
E-Reader works and can be mounted multiple times

Install ssh
-----------
sudo pkcon update
sudo apt install openssh-server
sudo systemctl start ssh
sudo systemctl enable ssh

Install graphical package manager
---------------------------------
Use commandline for install to collect the commands here.
sudo apt install muon
When compared with Discover, the latter seems to find newer versions eg. Smplayer

Install Kate
------------
sudo apt install kate (got thje same version than Discover)
Settings - Configure Language
    British english to have the correct terms for this document
Settings - Configure Kate
    Editing
        General
            Enable static word wrap
            Show static word wrap marker
            120
        Spellcheck
            British English
            Automatic spell checking enabled by default

Install time update
-------------------
systemd-timesyncd.service is running
kate /etc/systemd/timesyncd.conf
    NTP=de.pool.ntp.org ntp.ubuntu.com

systemctl status systemd-timesyncd
● systemd-timesyncd.service - Network Time Synchronization
     Loaded: loaded (/lib/systemd/system/systemd-timesyncd.service; enabled; vendor preset: enabled)
     Active: active (running) since Mon 2021-11-15 11:23:18 CET; 10s ago
       Docs: man:systemd-timesyncd.service(8)
   Main PID: 3217 (systemd-timesyn)
     Status: "Initial synchronization to time server 94.130.49.186:123 (de.pool.ntp.org)."
      Tasks: 2 (limit: 3369)
     Memory: 1.2M
     CGroup: /system.slice/systemd-timesyncd.service
             └─3217 /lib/systemd/systemd-timesyncd

Nov 15 11:23:18 DizietSma systemd[1]: Starting Network Time Synchronization...
Nov 15 11:23:18 DizietSma systemd[1]: Started Network Time Synchronization.
Nov 15 11:23:18 DizietSma systemd-timesyncd[3217]: Initial synchronization to time server 94.130.49.186:123
(de.pool.ntp.org).

Printing
--------
sudo apt install hplip hplip-gui

sudo hp-setup
    Discovery in Network
    download on console
    Crtl-C and start again
    Do not setup fax
    Print test page is ok

System / Settings / Printer
    Make first (recommended entry default)
    Configure
        Duplex long side
        Duplex: Yes

When printing:
  Options / Setting / Duplex / long side

Scanner
-------
hp-toolbox scans via xsane seems more powerful than scanlite

Nextcloud Client
----------------
sudo apt install nextcloud-desktop nextcloud-desktop-cmd  dolphin-nextcloud
System / Utilities / Nextcloud Desktop Client
    Create connections for ukoehler and mkoehler
    Make sure you really log out before attempting to create another connection

Audio and webcam setup
----------------------
Laptop webcam doesn't work, yet.
The logitech one should work out of the box
Install Audacity snap from Discover to set the input level

Install webcamoid: sudo apt install webcamoid
Check webcam works

Signal
------
Install Signal Desktop Flatpak via Discover
System / Internet / Signal


KDE Configuration
=================

System / System settings / Virtual desktops
    2 lines
Open System Settings → Regional Settings → Language: Add Britsh English and German, download packages
Open System Settings → Regional Settings → Spell Check → Enable Automatic spell checking enabled by default → Apply
Dolphin → Hamburger menu (the top right corner) → Configure Dolphin → Enable Remember display style for each folder.

4 virtual desktops
Right click status bar / Add Widgets / Get New Widgets / Download New Plasma widgets
    System Load Viewer
Blank screen saver with lock on Desktop
Suse - Einstellungen - Systemeinstellungen - Fensterverhalten - Fensterverhalten - Aktivierung bei Mauskontakt - Vorrang
für Maus 450 ms
Suse - Einstellungen - Aussehen - Fenster - Window border size: normal
Suse / Einstellungen / Benachrichtungen /
    Setup notification tones
sudo zypper in myspell-en_GB
Start / Settings / System Settings / Regional Settings / Spell Check / Enable all languages
Dolphin / Show menu
Dolphin / Settings / Startup / Make location bar editable
Dolphin / Settings / Navigation / Open new tab at end


DizietSma
=========

Mounting encryted device in Dolphin mounts to the wrong location
Use blkid | grep crypto to get the correct dev. Kpartitionmanager gives different ones.

edit /etc/fstab
    /dev/mapper/home      /var/Leap15.1_home                 ext4    defaults        0 0
    /dev/mapper/data      /var/Data                 ext4    defaults        0 0
edit /etc/crypttab
    home  /dev/sda5       none
    data  /dev/sda6       none
works at least for data, which is needed in the following

sudo apt install auctex emacs emacs-el emacs-goodies-el
Install KDiff3 via Discover

Check rc files against 15.1 version
ls bin
rm -rf bin
ln -s /var/Data/ukoehler/bin bin
dito with devel, Dokumente, Downloads, emacs, share, tex, Musik, Videos, Workspace
Adapt .bashrc with my stuff from /var/Leap15.1_home/ukoehler/.bashrc
cp -r /var/Leap15.1_home/ukoehler/.gnu-emacs* .
rm -r .mozilla
cp -r /var/Leap15.1_home/ukoehler/.mozilla/ .
use firefox -p to select the correct profile to use
rsync -avuz /var/Leap15.1_home/ukoehler/tmp ~
rsync -avuz /var/Leap15.1_home/ukoehler/MailArchive ~
mkdir -p .kde4/share/apps/kabc
cp /var/Leap15.1_home/ukoehler/.kde4/share/apps/kabc/std.vcf ~/.kde4/share/apps/kabc/
cp /var/Leap15.1_home/ukoehler/.kde4/share/apps/kabc/archive.vcf ~/.kde4/share/apps/kabc/
cp /var/Leap15.1_home/ukoehler/.kde4/share/apps/kabc/kolibri.vcf ~/.kde4/share/apps/kabc/
rsync -avuz /var/Leap15.1_home/ukoehler/.qactivitytracker ~
rsync -avuz /var/Leap15.1_home/ukoehler/.local/share/local-mail ~/.local/share/
rm -rf .gnupg/
rsync -avuz /var/Leap15.1_home/ukoehler/.gnupg ~
rm -rf .ssh
rsync -avuz /var/Leap15.1_home/ukoehler/.ssh ~
rm /home/ukoehler/.ssh/known_hosts
rsync -avuz /var/Leap15.1_home/ukoehler/.kde4/share/kde4/services ~/.kde4/share/kde4/

rsync -avuv --delete GreyArea:/home/ukoehler/Dokumente /var/Data/ukoehler/
rsync -avuz --delete GreyArea:/home/ukoehler/.gnupg /home/ukoehler/
rsync -avuz --delete GreyArea:/home/ukoehler/devel /var/Data/ukoehler/
rsync -avuz --delete GreyArea:/home/ukoehler/tex /var/Data/ukoehler/
rsync -avuz --delete GreyArea:/home/ukoehler/Downloads /var/Data/ukoehler/
rsync -avuz --delete GreyArea:/home/ukoehler/.qactivitytracker /home/ukoehler/
rsync -avuz --delete GreyArea:/home/ukoehler/MailArchive /home/ukoehler
rsync -avuz --delete GreyArea:/var/DataSATA/PictureDVD19 /var/Data/Pictures/
rsync -avuz --delete GreyArea:/var/OneT/VideosPhotos/PictureDVD19 /var/Data/Pictures/


Both
====

Wacom
-----
Install Systemsettings for Wacom tablets in Discover
sudo apt install gimp gimp-dcraw gimp-data-extras

Attach the wacom tablet
System / Settings / System settings / Input / Graphics tablet
Gimp somehow works, but the new way is quite complicated

Software development
--------------------
Install KDE software development kit via discovery
sudo apt install libqwt-qt5-6 libqwt-qt5-dev pdftk-java

gcc -v
gcc version 9.3.0 (Ubuntu 9.3.0-17ubuntu1~20.04)

cd devel/C++/qt/QActivityTracker5/build/
rm -rf *
cmake ..
cmake --build .
    Fix /home/ukoehler/devel/C++/qt/QActivityTracker5/src/analysispanel.cpp axes -> axis
src/qactivitytracker  works
cp src/qactivitytracker ~/bin
cd
qactivitytracker  works

cd devel/C++/qt/QYearlyCalender5
kate src/main.cpp &  fix password
cd build
rm -rf *
cmake ..
cmake --build .
cd ..
build/src/qyearlycalender
pdftk calender1page_uwe.pdf cat 3 output calender1page_uwe_2021.pdf
okular calender1page_uwe_2021.pdf
pdftk calender4page_uwe.pdf cat 5-8 output calender4page_uwe_2021.pdf
okular calender4page_uwe_2021.pdf

WLAN dongle driver
------------------
apt install dkms
https://askubuntu.com/questions/1178802/proper-way-of-installing-wifi-drivers-rtl8822bu
sudo -i
cd /usr/local/src
git clone https://github.com/cilynx/rtl88x2bu.git
cd rtl88x2bu
VER=$(sed -n 's/\PACKAGE_VERSION="\(.*\)"/\1/p' dkms.conf)
rsync -rvhP ./ /usr/src/rtl88x2bu-${VER}
dkms add -m rtl88x2bu -v ${VER}
dkms build -m rtl88x2bu -v ${VER}
dkms install -m rtl88x2bu -v ${VER}
sudo modprobe 88x2bu

Libre Office
------------
sudo apt install libreoffice mythes-de libreoffice-writer2latex libreoffice-writer2xhtml openclipart-libreoffice
libreoffice-kde mythes-en-us libreoffice-l10n-en-gb libreoffice-l10n-de

Video Previews
--------------
Install  Dolphin FFMpeg Video thumnailer (Vorschaugenerator) via Discover
Dolphin - Settings - Dolphin settings - General - Preview - Video (auto selected after install)

Additional drivers
------------------
sudo apt install kubuntu-driver-manager
System / Settings / System settings / Driver Manager

Checks
------
firefox (ok),
gpg --list-keys,
gpg --list-secret-keys,
ssh (ok),
LibreOffice with Haushaltsbuch
Video preview
qactivitytracker
qyearlycalender
keepassxc

Kontact setup
-------------

Install Kontact and keepassxc from Discover

Kontact / Open Email
Einrichten / KMail einrichten
Identitäten / Dr. Uwe Köhler (Standard)
    Allgemein / Email-Adresse
    Kryptographie / OpenPGP Keys
    Erweitert / Wörterbuch: Deutsch
    Signatur
 |                                                                 |
-+-----------------------------------------------------------------+-
 |  Dr. Uwe Köhler 柯佛          Davertstr. 55                      |
 |                              D - 48163 Münster                  |
 |          ////                Germany                            |
 |         (@ @)                Phone: +49 177 47 97 545           |
-+-----oOO--(_)--OOo-----------------------------------------------+-
 |                                       <mailto:U.Koehler@gmx.de> |

Identitäten / Dr. Uwe Köhler (Sec)
    Dr. Uwe Köhler
    U.Koehler_sec@gmx.de
    "U.Koehler_sec@gmx.de" <u.koehler_sec@gmx.de>
    Odroid/Sent
    GMX_Neijia

Zugänge / Versand / GMX (Standard)
 SMTP
 mail.gmx.net
 U.Koehler@gmx.de
 keepassxc
 TLS 587 PLAIN

 Zugänge / Versand / GMX_Neijia
 mail.gmx.net
 U.Koehler_neijia@gmx.de
 keepassxc
 TLS 587 PLAIN


Zugänge / Empfang / MailArchive
maildir: /home/ukoehler/MailArchive

Zugänge / Empfang / Lokale Ordner
Maildir: /home/ukoehler/.local/share/akonadi_maildir_resource_0

Zugänge / Empfang / Odroid
odroid
ukoehler
0pi
Offline
Lokale Ordner/Papierkorb
STARTTLS 143 Plain

#Odroid / Erweitert / Papierkorb / Odroid/Trash

Email Ansicht: Standard Nachrichten Liste
#Email: Erscheinungsbild / Nachrichtenfenster / HTMLStatus
#Email: Erscheinungsbild / Abschnitt der Kontrollleiste / Immer Anzeigen
Email: E-Mail-Editor / Allgemeine / Keine Antwort in HTML
Email: Antwort / Angezeigte Werkzeugleisten / Kein HTML
Email: Einstellungen / Reschtreibüberprüfung / Deutsch (Deutschland)

Kontact / Addressbook / Address Book Panel / right click
  Std vCard /home/ukoehler/.kde4/share/apps/kabc/std.vcf
  Archive vCard /home/ukoehler/.kde4/share/apps/kabc/archive.vcf
  Kolibri vCard /home/ukoehler/.kde4/share/apps/kabc/kolibri.vcf

odroid card and cal
Nextcloud
ukoehler
0.5pintown
CalDav https://odroid/nextcloud/remote.php/dav/ Global
CardDav https://odroid/nextcloud/remote.php/dav/ Global

Android ADB
-----------
sudo apt install adb
adb shell 'pm list packages -f'
adb shell pm path org.thoughtcrime.securesms
adb pull /data/app/org.thoughtcrime.securesms-2/base.apk

adb shell dumpsys package my.package | grep versionName
adb shell dumpsys package my.package | grep versionCode

Checks
------
Send emails from all identities
Calendars
Contacs

Get temperature
---------------
sudo apt install lm-sensors

Encfs
-----
sudo apt install encfs
already installed

LaTeX
-----

emacs-auctex will pull most of texlive
sudo apt install texlive-lang-german aspell-de cjk-latex foiltex fonttools hyphen-de hyphen-en-gb
hyphen-en-us latex-cjk-chinese tex-gyre texlive-extra-utils texlive-font-utils texlive-fonts-extra
texlive-fonts-recommended texlive-lang-cjk texlive-lang-english texlive-pictures texlive-pstricks texlive-science
texlive-latex-extra

cd tex/Tauchen/
pdflatex DiveLogBook.tex

cd ../Chinesisch/
pdflatex TaiJi.tex

Collect installs
================
sudo apt install kate hplip hplip-gui nextcloud-desktop nextcloud-desktop-cmd  dolphin-nextcloud webcamoid auctex emacs
emacs-el emacs-goodies-el gimp gimp-dcraw gimp-data-extras dkms libreoffice mythes-de libreoffice-writer2latex
libreoffice-writer2xhtml openclipart-libreoffice libreoffice-kde mythes-en-us libreoffice-l10n-en-gb
libreoffice-l10n-de kubuntu-driver-manager adb pdftk-java libqwt-qt5-6 libqwt-qt5-dev lm-sensors texlive-lang-german
aspell-de cjk-latex foiltex fonttools hyphen-de hyphen-en-gb hyphen-en-us
latex-cjk-chinese tex-gyre texlive-extra-utils texlive-font-utils texlive-fonts-extra texlive-fonts-recommended
texlive-lang-cjk texlive-lang-english texlive-pictures texlive-pstricks texlive-science texlive-latex-extra


Install Signal Desktop Flatpak via Discover
Install Audacity Snap via Discover
Install Systemsettings for Wacom tablets in Discover
Install KDE software development kit via Discover
Dolphin FFMpeg Video thumnailer (Vorschaugenerator) via Discover
Install Kontact and keepassxc from Discover


Fonts
=====

sudo apt install ttf-mscorefonts-installer fonts-crosextra-caladea fonts-crosextra-carlito
sudo fc-cache -vr


Hints
=====

Update using
    sudo pkcon refresh && sudo pkcon update
Root shell with sudo -i

