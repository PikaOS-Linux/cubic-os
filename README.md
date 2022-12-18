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
