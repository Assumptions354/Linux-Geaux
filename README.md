# Linux Geaux

Linux Geaux is a remix of Debian 12 bookworm, built with Debian's official live-build framework.

## Features
Added the uncomplicated firewall (ufw) sets up the firewall by default with no setup.

Added a file editor (vim) for ease of access to make it easier to edit a file

Added an interactive process manager (htop) for users to check and see what processes are being run

Added a system information summary by using neofetch

Added git incorporated to the distro instead of having to manually install it.

## Motivation
Debian live-build makes the whole distro a folder of plain config files that makes a reproducible build.

XFCE runs well in a virtual machine.

The firewall changes give a secure system default



## Reproducible build instructions

Requirements: a Debian 12 or 13 machine or VM. I built on a VirtualBox VM running Debian 13.

### Step 1: Install the tools

su -

apt update

apt install live-build debootstrap git


### Step 2: Get the configuration (To install from Github choose option A, to create manually pick option B)

### Option A: use the source/ folder from this repo

git clone https://github.com/Assumptions354/Linux-Geaux

cp -r Linux-Geaux/source ~/mydistro

cd ~/mydistro

Then skip to Step 3.

### Option B: recreate the configuration from scratch

Create a working folder and the base config:

mkdir ~/mydistro && cd ~/mydistro

lb config --distribution bookworm --architectures amd64 --binary-images iso-hybrid --archive-areas "main contrib non-free non-free-firmware" --iso-application "Linux Geaux" --iso-volume "LINUX_GEAUX" --bootappend-live "boot=live components quiet splash hostname=linuxgeaux username=geaux"


echo "xfce4 xfce4-goodies lightdm firefox-esr thunar htop vim git ufw neofetch" > config/package-lists/custom.list.chroot




cat > config/hooks/live/0100-linuxgeaux.hook.chroot << 'EOF'

#!/bin/sh

systemctl enable ufw

echo "Welcome to Linux Geaux" > /etc/motd

EOF

chmod +x config/hooks/live/0100-linuxgeaux.hook.chroot


mkdir -p config/includes.chroot/etc/systemd/system/multi-user.target.wants

mkdir -p config/includes.chroot/etc/ufw

mkdir -p config/includes.chroot/etc/live/config.conf.d

mkdir -p config/includes.chroot/etc/lightdm/lightdm.conf.d

mkdir -p config/includes.chroot/etc/skel/.config/xfce4/xfconf/xfce-perchannel-xml

mkdir -p config/includes.chroot/usr/share/backgrounds


echo "Welcome to Linux Geaux" > config/includes.chroot/etc/motd

echo 'Linux Geaux \n \l' > config/includes.chroot/etc/issue

echo "Linux Geaux" > config/includes.chroot/etc/issue.net


printf 'ENABLED=yes\nLOGLEVEL=low\n' > config/includes.chroot/etc/ufw/ufw.conf

ln -sf /lib/systemd/system/ufw.service config/includes.chroot/etc/systemd/system/multi-user.target.wants/ufw.service

echo 'LIVE_USER_FULLNAME="Linux Geaux"' > config/includes.chroot/etc/live/config.conf.d/linuxgeaux.conf

### Auto Login

printf '[Seat:*]\nautologin-user=geaux\nautologin-user-timeout=0\n' > config/includes.chroot/etc/lightdm/lightdm.conf.d/50-autologin.conf





### Step 3: Build the ISO

lb build



## Known limitations
The boot menu still shows the stock Debian GNU/Linux 12 title. That text is built into the bootloader components and could not be changed with a config rewrite.

The base is Debian, so some system files like /etc/os-release still use Debian values.

# Demo

In the Linux Geaux Demo video and the Linux Geaux Desktop screenshot, it shows a built in wallpaper. I could not figure out how to get that into the final build.
