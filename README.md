# cubic-os
A Guide on how to build a PikaOS ISO using Cubic

# Host Builder
## What should it be:
Anything based Ubuntu 18.04.4 LTS or higher with X11 capabilities 
## Getting Cubic
Follow the guide in [Cubic Ubuntu ISO Wizard](https://launchpad.net/cubic)

# Cubic

## Create a new project based Ubuntu 22.10 Kinetic
<img src="https://github.com/PikaOS-Linux/cubic-os/blob/main/assets/cubic_project.png" width="256"/>

## Cubic ISO Naming

* Set Volume ID to 
```
PikaOS 22.10 amd64
```

* Set Release to
```
Kinetic Kudo
```

* Set Disk Name to
```
PikaOS 22.10 "Kinetic Kudu"
```

* Untick Update Release Description

The end result should look something like this:

<img src="https://github.com/PikaOS-Linux/cubic-os/blob/main/assets/cubic_name.png" width="256"/>

## Virtual Environment

<img src="https://github.com/PikaOS-Linux/cubic-os/blob/main/assets/cubic_virtenv.png" width="256"/>

### Setup Live Session User

* Create user and set his password to "pika2022"
```
adduser pikaos
```
* Add proper groups to pikaos
```
usermod -a -G adm,dialout,cdrom,floppy,sudo,audio,dip,video,plugdev,lpadmin pikaos
```
* Make the live session auto-login with liveuser

```
cp /etc/gdm3/custom.conf /etc/gdm3/custom.conf.orig  
```
```
nano /etc/gdm3/custom.conf
```
Edit in the follwing under the "[daemon]" section:
```
#Enabling automatic login
  AutomaticLoginEnable = true
  AutomaticLogin = pikaos
```
* allow the pikaos to access sudo without password
```
cp /etc/sudoers /etc/sudoers.orig
```
```
nano /etc/sudoers
```
Edit in the follwing:
```
pikaos ALL=(ALL) NOPASSWD:ALL
```
### Setup default user groups to allow CUDA/ROCm Usage
```
echo 'ADD_EXTRA_GROUPS=1' | tee -a /etc/adduser.conf

echo 'EXTRA_GROUPS=video' | tee -a /etc/adduser.conf

echo 'EXTRA_GROUPS=render' | tee -a /etc/adduser.conf

```
### Ubuntu is an ancient African word meaning 'humanity', we are mutating it into a cocketiel
* Removing Snap and weird ubuntu's stuff
```
apt purge snapd
apt remove yaru*
apt remove update-manager*
apt remove gnome-initial-setup
```
* Installing PikaOS Sources
```
apt update
apt install wget
```
go to https://launchpad.net/~pikaos/+archive/ubuntu/baseos/+packages and copy the download link to the latest pika-sources deb

example : https://launchpad.net/~pikaos/+archive/ubuntu/baseos/+files/pika-sources_2.0_all.deb

then download it using wget and install it

```
wget https://launchpad.net/~pikaos/+archive/ubuntu/baseos/+files/pika-sources_3.0-99pika18_all.deb
```
Install Source: (eventually it will ask for confirmation: Y)
```
apt install ./pika-sources_3.0-99pika18_all.deb
```
Remove Source:
```
rm pika-sources_3.0-99pika18_all.deb
```
Update Sources/PPAs
```
apt update
```
* Getting and configuring nala (10x faster apt)

install nala

```
apt install nala
```

disable nala autoremove

```
nano /etc/nala/nala.conf
```
Edit "auto_remove = true" to:
```
auto_remove = false
```

* Update the system
```
apt update
apt install linux-firmware
apt install pika-amdgpu-core
apt upgrade
```
if mesa gives you non-sense do:

```
dpkg -r libgl1-amber-dri
```

then

```
apt --fix-broken install
```

and again

```
apt upgrade
```

* Install the pika meta package
```
apt install pika-gnome-desktop-minimal zram-config
```
~~* Install our Kernel~~

~~apt install kernel-pika~~

⚠️ WARNING ⚠️ : IN CASE NALA OR APT HOLDS ANY PACKAGE FROM UPGRADE OR PUTS ANYTHING IN AUTOREMOVE USE "apt install" TO FIX IT IMMEDIATELY 
Example:
	Held packages: (make sure to double check that snapd ain't sneaking back like: gir1.2-snapd-2)
```
The following packages were automatically installed and are no longer required:
  gir1.2-snapd-2 gnome-themes-extra gnome-themes-extra-data gtk2-engines-murrine gtk2-engines-pixbuf
  libsysmetrics1 python3-distupgrade python3-update-manager ubuntu-release-upgrader-core xwayland
Use 'apt autoremove' to remove them.
The following packages have been kept back:
  plymouth plymouth-label plymouth-theme-spinner
```
Install:
```
apt install gnome-themes-extra gnome-themes-extra-data gtk2-engines-murrine gtk2-engines-pixbuf libsysmetrics1 python3-distupgrade python3-update-manager ubuntu-release-upgrader-core xwayland plymouth plymouth-label plymouth-theme-spinner
```

### Getting the PikaOS installer

* remove shim stuff as it breaks non secure boot capable OSes
```
apt purge shim* --allow-remove-essential
```
* remove ubiquity
```
apt purge ubiquity*
```
* install pika-installer
```
apt install calamares-settings-pika
```
* remove calamares duplicate desktop entry
```
rm /usr/share/applications/calamares.desktop
```

### Clean up

* Remove Ubuntu extra stuff:
```
apt remove *libreoffice* thunderbird shotwell remmina totem
```
```
apt remove gnome-mines gnome-sudoku gnome-mahjongg aisleriot
```

* Clean
```
apt autoremove
nala clean
apt clean
```
Ex of autoremove: 
```
The following packages were automatically installed and are no longer required:
  gir1.2-goa-1.0 gir1.2-snapd-2 python3-dateutil ubuntu-advantage-desktop-daemon
  gir1.2-totem-1.0 gir1.2-totemplparser-1.0 guile-3.0-libs libabw-0.1-1 libavahi-ui-gtk3-0
  libboost-filesystem1.74.0 libboost-locale1.74.0 libcdr-0.1-1 libclucene-contribs1v5
  libclucene-core1v5 libe-book-0.1-1 libeot0 libepubgen-0.1-1 libetonyek-0.1-1 libexttextcat-2.0-0
  libexttextcat-data libfreehand-0.1-1 libfreerdp-client2-2 libgc1 libgnome-games-support-1-3
  libgnome-games-support-common liblangtag-common liblangtag1 libmhash2 libmspub-0.1-1 libmwaw-0.3-3
  libmythes-1.2-0 libodfgen-0.1-1 liborcus-0.17-0 liborcus-parser-0.17-0 libpagemaker-0.0-0
  libqqwing2v5 libraptor2-0 librasqal3 librdf0 librevenge-0.0-0 libtotem0 libuno-cppu3
  libuno-cppuhelpergcc3-3 libuno-purpenvhelpergcc3-3 libuno-sal3 libuno-salhelpergcc3-3 libvisio-0.1-1
  libvncclient1 libwpd-0.10-10 libwpg-0.3-3 libwps-0.4-4 libxmlsec1 libxmlsec1-nss lp-solve
  python3-debconf remmina-common shotwell-common totem-common uno-libs-private ure

```
## Prepare Page

<img src="https://github.com/PikaOS-Linux/cubic-os/blob/main/assets/cubic_prepare.png" width="256"/>

See the fruits of your work!

## Package Page

Sometimes Cubic bugs out and shows this page:

<img src="https://github.com/PikaOS-Linux/cubic-os/blob/main/assets/cubic_package.png" width="256"/>

this for the ubiquity package selections and you can just ignore it

## Option Page => Boot Tab

<img src="https://github.com/PikaOS-Linux/cubic-os/blob/main/assets/cubic_boot.png" width="256"/>

this is where you configure grub of the live session

please overwrite the following with:

* boot => grub => grub.cfg (You can remove quiet and splash to have full verbose)
```
set timeout=30

loadfont unicode

set menu_color_normal=white/black
set menu_color_highlight=black/light-gray

menuentry "Start PikaOS Live Session" {
	set gfxpayload=keep
	linux	/casper/vmlinuz boot=casper file=/cdrom/preseed/ubuntu.seed quiet splash --- 
	initrd	/casper/initrd.gz
}
menuentry "Start PikaOS Live Session with nomodeset (safe graphics)" {
	set gfxpayload=keep
	linux	/casper/vmlinuz boot=casper nomodeset file=/cdrom/preseed/ubuntu.seed quiet splash --- 
	initrd	/casper/initrd.gz
}
grub_platform
if [ "$grub_platform" = "efi" ]; then
menuentry 'Boot from next volume' {
	exit 1
}
menuentry 'UEFI Firmware Settings' {
	fwsetup
}
else
menuentry 'Test memory' {
	linux16 /boot/memtest86+.bin
}
fi
```

* boot => grub => loopback.cfg (You can remove quiet and splash to have full verbose)

```

menuentry "Start PikaOS Live Session" {
	set gfxpayload=keep
	linux	/casper/vmlinuz boot=casper file=/cdrom/preseed/ubuntu.seed iso-scan/filename=${iso_path} quiet splash --- 
	initrd	/casper/initrd.gz
}
menuentry "Start PikaOS Live Session with nomodeset (safe graphics)" {
	set gfxpayload=keep
	linux	/casper/vmlinuz boot=casper nomodeset file=/cdrom/preseed/ubuntu.seed iso-scan/filename=${iso_path} quiet splash --- 
	initrd	/casper/initrd.gz
}

```

## Compression Page

<img src="https://github.com/PikaOS-Linux/cubic-os/blob/main/assets/cubic_compression.png" width="256"/>

Choose whatever you like

I recommend gzip

## Creation Page

<img src="https://github.com/PikaOS-Linux/cubic-os/blob/main/assets/cubic_final.png" width="256"/>

After this a PikaOS iso will generate in the project files

Congrats!
