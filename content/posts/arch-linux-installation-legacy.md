---
title: "Arch Linux Installation (Legacy)"
date: 2021-11-29T00:10:24+03:00
categories: [Tutorials & How-to's]
tags: [arch-linux,secure]
description: "My ArchLinux installation steps."
ready: "in progress"
draft: false
---

> [Legacy] BIOS mode

1. [Make Bootable USB](#step-1)
1. [Wipe Disk](#step-2)
1. [Disk partitioning](#step-3)
1. [Encryption](#step-4)
1. [Create Physical&Logical Volume(s)](#step-5)
1. [Formatting&Mounting](#step-6)
1. [Install base](#step-7)
1. [Generate fstab](#step-8)
1. [Enter new system](#step-9)
1. [Timezone](#step-10)
1. [Language](#step-11)
1. [root&user setup](#step-12)
1. [Luks key](#step-13)
1. [TODO](#step-14)
1. [Grub](#step-15)
1. [Exit](#step-16)
1. [Update System](#step-17)
1. [Install Gnome](#step-18)
1. [Optional](#step-19)

### Step 1
➥ (make bootable USB):
- Download iso: [https://archlinux.org/download/](https://archlinux.org/download/)
- Burn <cite>archlinux-*version*-x86_64.iso</cite> file to a USB drive.
> to do that;
```text
lsblk
# find out name of USB --> e.g. "sdb" !choose right one.
# Make sure USB drive is empty. This process destroy all data in sdb.

sudo dd bs=4M if=archlinux-*version-x86_64.iso of=/dev/< sdX > conv=fsync oflag=direct status=progress
```
- Setup BIOS for booting from USB & boot arch-linux

### Step 2
➥ (wipe disk):
> Before this step <mark>you may need backup your important files.</mark> This process will wipe out all data in your hard drive.

```text
lsblk
# find out name of HardDrive --> e.g. "sda" !choose right one.
DEVICE="/dev/sdX"
PASS=$(tr -cd '[:alnum:]' < /dev/urandom | head -c128)
openssl enc -aes-256-ctr -pass pass:"$PASS" -nosalt < /dev/zero | dd obs=64K ibs=4K of=$DEVICE oflag=direct status=progress
```

### Step 3
➥ (disk partitioning --> [legacy]):
```text
cfdisk
- !label type [dos]
- new --> [enter] --> primary
- bootable
- write --> type < yes >
- quit
lsblk
# e.g. sda, sda1
```

### Step 4
➥ (encryption --> cryptsetup/luks):
```text
cryptsetup luksFormat --type luks1 --use-random -S 1 -s 512 -h sha512 /dev/< sdX1 >
- type < YES > --> enter pass
cryptsetup luksOpen /dev/sda1 < enc-arch >
```

### Step 5
➥ (create physical&logical volume(s)):
```text
pvcreate /dev/mapper/< enc-arch >
vgcreate < Arch > /dev/mapper/< enc-arch >
lvcreate -L < 3G > < Arch > -n swap # !for 3gb ram
lvcreate -L < 32G > < Arch > -n root # !32|50 GB should be enough
lvcreate -l 100%FREE < Arch > -n home
```

### Step 6
➥ (formatting&mounting):
```text
mkswap /dev/mapper/< Arch >-swap
mkfs.ext4 /dev/mapper/< Arch >-root
mkfs.ext4 /dev/mapper/< Arch >-home
mount /dev/mapper/< Arch >-root /mnt
mkdir /mnt/home
mount /dev/mapper/< Arch >-home /mnt/home
swapon /dev/mapper/< Arch >-swap
mkdir /mnt/boot
```

### Step 7
➥ (install base):
```text
pacstrap /mnt base base-devel linux linux-firmware linux-headers mkinitcpio networkmanager wpa_supplicant dialog dhcpcd lvm2  vim terminus-font grub
```

### Step 8
➥ (create fstab):
```text
genfstab -U /mnt >> /mnt/etc/fstab
cat /mnt/etc/fstab
```

### Step 9
➥ (enter new system chroot):
```text
arch-chroot /mnt
```

### Step 10
➥ (timezone):
```text
timedatectl set-timezone "$(curl -s --fail https://ipapi.co/timezone)"
timedatectl set-ntp true
hwclock --systohc --utc
```

### Step 11
➥ (language):
```text
echo < dev > > /etc/hostname
sed -i '/en_US.UTF-8 UTF-8/s/^#//g' /etc/locale.gen
echo LANG=en_us.UTF-8 > /etc/locale.conf
locale-gen
```

### Step 12
➥ (root&user setup):
```text
passwd
# root password
useradd -m -G network,users,storage,lp,input,audio,wheel -s /bin/bash < arch-user >
passwd < arch-user >
```

### Step 13
➥ (luks key):
```text
mkdir /root/< .cryptlvm > && chmod 700 /root/< .cryptlvm >
dd bs=512 count=4 if=/dev/random of=/root/< .cryptlvm >/< archluks >.bin iflag=fullblock
chmod 600 /root/< .cryptlvm >/< archluks >.bin
cryptsetup -v luksAddKey /dev/sda1 /root/< .cryptlvm >/< archluks >.bin
cryptsetup luksDump /dev/sda1 #luks key slot 0-1
```

### Step 14
➥ (TODO):
```text
vim /etc/mkinitcpio.conf
    FILES(/root/< .cryptlvm >/< archluks >.bin)
    HOOKS(base udev autodetect modconf block encrypt lvm2 filesystems keyboard fsck)
mkinitcpio -p linux
```

### Step 15
➥ (grub):
```text
UUID=$(blkid /dev/< sdX1 > -s UUID -o value)
sed -i "/^GRUB_CMDLINE_LINUX=/cGRUB_CMDLINE_LINUX=\"cryptdevice=UUID=${UUID}:< enc-arch > root=/dev/mapper/< Arch >-root cryptkey=rootfs:/root/< .cryptlvm >/< archluks >.bin\""  /etc/default/grub
sed -i "/GRUB_ENABLE_CRYPTODISK=/cGRUB_ENABLE_CRYPTODISK=y" /etc/default/grub

grub-install --target=i386-pc --bootloader-id=ArchLinux /dev/< sdX >
grub-mkconfig -o /boot/grub/grub.cfg
```

### Step 16
➥ (base installation done, exit now):
```text
exit
umount -R /mnt
swapoff -a
reboot
#unplug USB
```

### Step 17
➥ (update system):
```text
#login as root
visudo
    %wheel ALL=(ALL:ALL) NOPASSWD: ALL #uncomment this line
ping < network addr > #test connection
pacman -Syu #update system
pacman -S linux-headers dkms
```

### Step 18
➥ (install gnome):
```text
pacman -S gnome
systemctl enable gdm #!&for user
systemctl enable NetworkManager
reboot
```

### Step 19
➥ (optional):
```text
pacman -Rns gnome-software gnome-maps gnome-books epiphany ...
pacman -S chromium
```

&(Thanks to [@huntrar](https://gist.github.com/huntrar) & [@pad92](https://gitlab.com/pad92))

[1] https://wiki.archlinux.org/title/Installation_guide\
[2] https://gist.github.com/huntrar/e42aee630bee3295b2c671d098c81268\
[3] https://gitlab.com/pad92/dotfiles/-/blob/master/archlinux/install.md
