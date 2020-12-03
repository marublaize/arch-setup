# Arch Linux Installation Guide

This document is a guide for installing Arch Linux in VirtualBox using the live system booted from an installation medium made from an official installation image.

## Set the keyboard layout

```bash
loadkeys br-abnt2
```

## Update the system clock

```bash
timedatectl set-ntp true
```

## Partition the disks

```bash
fdisk /dev/sda
# Create root partition (/dev/sda1)
n
p
1
\n
+7G

# Create swap partition (/dev/sda2)
n
p
2
\n
\n
t
2
swap

# Write partitions
w
```

## Format the partitions

```bash
mkfs.ext4 /dev/sda1
mkswap /dev/sda2
```

## Mount the file systems

```bash
mount /dev/sda1 /mnt
swapon /dev/sda2
```

## Install essential packages

```bash
pacstrap /mnt base linux linux-firmware grub vim nano
```

## Generate Fstab configuration

```bash
genfstab -U /mnt >> /mnt/etc/fstab
```

## Enter Chroot

```bash
arch-chroot /mnt
```

## Configure time zone

```bash
ln -sf /usr/share/zoneinfo/America/Sao_Paulo /etc/localtime
hwclock --systohc
```

## Configure localization

```bash
locale-gen
echo "LANG=en_US.UTF-8" > /etc/locale.conf
echo "KEYMAP=br-abnt2" > /etc/vconsole.conf
```

## Configure networking

The hard way:

```bash
echo "archlinux" > /etc/hostname
cat <<'EOF' >> /etc/hosts
127.0.0.1   localhost
::1         localhost
127.0.1.1   archlinux.localdomain   archlinux
EOF
```

The easy way:

```bash
echo "archlinux" > /etc/hostname
nano /etc/hosts
127.0.0.1   localhost
::1         localhost
127.0.1.1   archlinux.localdomain   archlinux
```

## Enable/start networking services

```bash
systemctl enable systemd-networkd
systemctl enable systemd-resolved
```

## Set root password

```bash
echo -e "archlinux\narchlinux" | passwd
```

## Initramfs

```bash
mkinitcpio -P
```

## Install GRUB boot loader

```bash
grub-install /dev/sda
grub-mkconfig -o /boot/grub/grub.cfg
```

## Exit Chroot

```bash
exit
```

## Reboot

```bash
reboot
```

## Optional steps

### Install Gnome desktop environment (optional)

```bash
pacman -S xorg gnome
```

### Enable/start the display manager (optional)

```bash
systemctl enable gdm && systemctl start gdm
```

### Enable/start Gnome Network Manager (optional)

```bash
systemctl enable NetworkManager && systemctl start NetworkManager
```
