# cubic-os
A Guide on how to build a PikaOS ISO using Cubic

# Host Builder
## What should it be:
Anything based Ubuntu 18.04.4 LTS or higher with X11 capabilities 
## Getting Cubic
Follow the guide in [Cubic Ubuntu ISO Wizard](https://launchpad.net/cubic)

# Cubic
## Create a new project based Ubuntu 22.10 Kinetic
[<img src="https://github.com/PikaOS-Linux/cubic-os/blob/main/assets/cubic_project.png" width="256"/>]
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

[<img src="https://github.com/PikaOS-Linux/cubic-os/blob/main/assets/cubic_name.png" width="256"/>]

## Virtual Environment

[<img src="https://github.com/PikaOS-Linux/cubic-os/blob/main/assets/cubic_virtenv.png" width="256"/>]

### Setup Live Session User

* Create user and set his password to "pika2022"
```
adduser liveuser
```
* Add proper groups to liveuser
```
usermod -a -G adm,dialout,cdrom,floppy,sudo,audio,dip,video,plugdev,lpadmin liveuser
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
```
* Installing PikaOS Sources
```
sudo apt update
sudo apt install wget
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
sudo apt install nala
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
* Install the pika meta package
```
nala install pika-gnome-desktop-minimal auto-cpufreq zram-config
```
* Install our Kernel
```
nala install linux-image-liquorix-amd64 linux-headers-liquorix-amd64
```

⚠️ WARNING ⚠️ : IN CASE NALA OR APT HOLDS ANY PACKAGE FROM UPGRADE USE "apt install" TO UPGRADE IMMEDIATELY 

### Getting the PikaOS installer

* remove shim stuff as it breaks non secure boot capable OSes
```
apt purge shim*
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
