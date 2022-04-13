---
title: "Arch Linux Installation (Legacy)"
date: 2021-11-29T00:10:24+03:00
categories: [Tutorials & How-to's]
tags: [arch-linux,secure]
draft: false
---

:heavy_exclamation_mark: (BIOS type {Legacy})

1. [Make Bootable USB](#step-1)
1. [Wipe Disk](#step-2)
1. [Disk partitioning –> {legacy}](#step-3)
1. [Encryption --> cryptsetup/luks](#step-4)
1. [Formatting&Mounting](#step-5)
1. [Downloading {base}](#step-6)
1. [TODO](#step-7)
1. [Timezone](#step-8)
1. [Language](#step-9)
1. [root&user setup](#step-10)
1. [TODO](#step-11)
1. [TODO](#step-12)
1. [TODO](#step-13)
1. [TODO](#step-14)
1. [General setup](#step-15)
1. [Install Gnome](#step-16)
1. [Optional](#step-17)

## Step 1:
(Make bootable USB)
- Download iso --> !verify
- Use gnome-disk for make bootable USB --> restore disk image
- Setup bios for booting from USB --> boot arch-linux

## Step 2:
(Wipe Disk)

:heavy_exclamation_mark: Before this step you may need backup your important files. This process will wipe out all data in your hard drive.
- DEVICE="/dev/sdX"
- PASS=$(tr -cd '[:alnum:]' < /dev/urandom | head -c128)
- openssl enc -aes-256-ctr -pass pass:"$PASS" -nosalt < /dev/zero | dd obs=64K ibs=4K of=$DEVICE oflag=direct status=progress

## Step-3:
(Disk partitioning --> {legacy})
- cfdisk --> !label type [dos]
    - new --> !primary
    - bootable
    - write --> type < yes >
    - quit

- lsblk # sda, sda1

## Step-4:
(Encryption --> cryptsetup/luks)
- cryptsetup luksFormat --type luks1 --use-random -S 1 -s 512 -h sha512 /dev/< sda1 > # type < YES > --> enter pass
- cryptsetup luksOpen /dev/sda1 < enc-arch >

## Step-5
(physical&logical volume(s))
- pvcreate /dev/mapper/enc-arch
- vgcreate < Arch > /dev/mapper/enc-arch
- lvcreate -L < 3G > Arch -n swap # !for 3gb ram
- lvcreate -L < 32G > Arch -n root # !32|50 gb should be enough
- lvcreate -l 100%FREE Arch -n home

## Step 6:
(Formatting&Mounting)
- mkswap /dev/mapper/Arch-swap
- mkfs.ext4 /dev/mapper/Arch-root
- mkfs.ext4 /dev/mapper/Arch-home
- mount /dev/mapper/Arch-root /mnt
- mkdir /mnt/home
- mount /dev/mapper/Arch-home /mnt/home
- swapon /dev/mapper/Arch-swap
- mkdir /mnt/boot

## Step 7:
(Downloading base)
- pacstrap /mnt base base-devel linux linux-firmware linux-headers mkinitcpio networkmanager wpa_supplicant dialog dhcpcd lvm2  vim terminus-font grub

## Step 8:
(TODO)
- genfstab -U /mnt >> /mnt/etc/fstab
- cat /mnt/etc/fstab

## Step 9:
(enter new system chroot)
- arch-chroot /mnt

## Step 9:
(Timezone)
- timedatectl set-timezone "$(curl -s --fail https://ipapi.co/timezone)"
- timedatectl set-ntp true
- hwclock --systohc --utc

## Step 10:
(Language)
- echo < dev > > /etc/hostname
- sed -i '/en_US.UTF-8 UTF-8/s/^#//g' /etc/locale.gen
- echo LANG=en_us.UTF-8 > /etc/locale.conf
- locale-gen

## Step 11:
(root&user setup)
- passwd # root password
- useradd -m -G wheel -s /bin/bash arch-user
- passwd arch-user

## Step 12:
(TODO)
- mkdir /root/.cryptlvm && chmod 700 /root/.cryptlvm
- dd bs=512 count=4 if=/dev/random of=/root/.cryptlvm/archluks.bin iflag=fullblock
- chmod 600 /root/.cryptlvm/archluks.bin
- cryptsetup -v luksAddKey /dev/sda1 /root/.cryptlvm/archluks.bin #enter pass
- cryptsetup luksDump /dev/sda1 #luks key slot 0-1

## Step 13:
(TODO)
- vim /etc/mkinitcpio.conf
    - FILES(/root/.cryptlvm/archluks.bin)
    - HOOKS(base udev autodetect modconf block encrypt lvm2 filesystems keyboard fsck)
- mkinitcpio -p linux


## Step 14:
(TODO)
- vim /etc/default/grub
    - GRUB_ENABLE_CRYPTODISK=y #uncomment this line
    - GRUB_CMDLINE_LINUX="crypdevice=/dev/sda1:enc-arch resume=/dev/mapper/Arch-swap"
- grub-install --target=i386-pc --bootloader-id=ArchLinux /dev/sda
- grub-mkconfig -o /boot/grub/grub.cfg

## Step 15:
(TODO)
- exit
- umount -R /mnt
- swapoff -a
- reboot #unplug USB

## Step 16:
(General Setup)
- #login as root
- vim /etc/sudoers
    - %wheel NOPASS #uncomment
- systemctl start dhcpcd #connect network
- ping github.com #test connection
- pacman -Syu #update system
- pacman -S linux-headers dkms networkmanager?
- pacman -S xorg xorg-apps #(optional?)

## Step 17:
(Install Gnome)
- pacman -S gnome
- systemctl enable gdm #!for user

## Step 18:
(Optional)
- pacman -Rns gnome-software gnome-maps epiphany
- pacman -S chromium

---
&(Thanks to [@huntrar](https://gist.github.com/huntrar) & [@pad92](https://gitlab.com/pad92))
- https://wiki.archlinux.org/title/Installation_guide
- https://gist.github.com/huntrar/e42aee630bee3295b2c671d098c81268
- https://gitlab.com/pad92/dotfiles/-/blob/master/archlinux/install.md
