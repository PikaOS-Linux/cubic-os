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
* Add proper groups to liveuser
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
  AutomaticLogin = liveuser
```
* allow the liveuser to access sudo without password
```
cp /etc/sudoers /etc/sudoers.orig
```
```
nano /etc/sudoers
```
Edit in the follwing:
```
liveuser ALL=(ALL) NOPASSWD:ALL
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
wget https://launchpad.net/~pikaos/+archive/ubuntu/baseos/+files/pika-sources_2.0_all.deb
apt install ./pika-sources_2.0_all.deb
rm pika-sources_2.0_all.deb
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
nala update
nala install linux-firmware
nala install pika-amdgpu-core
nala upgrade
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
nala upgrade
```

* Install the pika meta package
```
nala install pika-gnome-desktop-minimal auto-cpufreq zram-config
```
* Install our Kernel
```
nala install kernel-pika
```
⚠️ WARNING ⚠️ : IN CASE NALA OR APT HOLDS ANY PACKAGE FROM UPGRADE OR PUTS ANYTHING IN AUTOREMOVE USE "apt install" TO FIX IT IMMEDIATELY 

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
nala install calamares-settings-pika
```
* remove calamares duplicate desktop entry
```
rm /usr/share/applications/calamares.desktop
```
### Clean up
```
nala clean
apt clean
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

* boot => grub => grub.cfg
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

* boot => grub => loopback.cfg

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
