+++
title = 'Some Notes on Installing Arch Linux with LVM on LUKS, BTRFS, and Systemd-Boot on SSD'
tags = 'archlinux'
slug = 'stylish-arch-doc-1'
summary = 'Arch Linux Install The Stylish Way'
+++

I love Arch Linux for its simplicity, flexibility, and bleeding-edge software. Plus, it's a distro with great memes. However, its installation process can be intimidating, especially when incorporating advanced features like **LVM on LUKS**, **the BTRFS file system**, and **systemd-boot** for an encrypted, robust, and modern system. This guide will walk you through the entire process of installing Arch Linux on a solid-state drive (SSD) with these basic configurations.

---

## Table of Contents
1. [Prerequisites](#prerequisites)
2. [Booting into the Live Environment](#booting-into-the-live-environment)
3. [Setting Up Disk Partitions](#setting-up-disk-partitions)
4. [Setting Up LUKS Encryption](#setting-up-luks-encryption)
5. [Configuring LVM (Logical Volume Manager)](#configuring-lvm-logical-volume-manager)
6. [Formatting with BTRFS](#formatting-with-btrfs)
7. [Mounting Partitions](#mounting-partitions)
8. [Installing Essential Packages](#installing-essential-packages)
9. [Configuring systemd-boot](#configuring-systemd-boot)
10. [Generating Filesystem Snapshots with BTRFS](#generating-filesystem-snapshots-with-btrfs)
11. [Final Steps](#final-steps)
12. [Post-Installation Tasks](#post-installation-tasks)

---

## Prerequisites

Before you start, ensure you have the following:
- A live Arch Linux ISO burned to a USB drive.
- A reliable internet connection.
- Basic familiarity with the Linux command line.

### Recommended Partition Scheme
We'll use the following partition layout:
- **EFI Partition**: `/dev/sdX1` (mounted as `/boot`)
- **Encrypted Partition**: `/dev/sdX2` (contains LVM volumes)
  - Root (`/`): Logical volume formatted with BTRFS.
  - Swap: Logical volume for swap space.

---

## Booting into the Live Environment

1. Insert your bootable USB and start your system. Select the USB as the boot device.
2. Once in the Arch live environment, verify your internet connection:
   ```bash
   ping -c 3 archlinux.org
   ```
   If needed, set up Wi-Fi:
   ```bash
   iwctl
   ```
   Inside `iwctl`:
   ```bash
   station wlan0 connect "YourSSID"
   ```

---

## Setting Up Disk Partitions

1. Identify your disk:
   ```bash
   lsblk
   ```
2. Launch `fdisk` to create a GPT partition table:
   ```bash
   fdisk /dev/sdX
   ```
3. Create partitions:
   - **Partition 1**: EFI partition (512 MB, type `EFI System`).
   - **Partition 2**: Encrypted partition (remaining space).

### Example using `fdisk`:
```bash
g # Create a new GPT table
n # Create EFI partition
+512M
t # Change partition type
1 # Set type to EFI System
n # Create LUKS partition (use default size for the rest of the disk)
t # Change type
44 # Set type to Linux LVM
w # Write changes
```

---

## Setting Up LUKS Encryption

1. Initialize LUKS encryption on the second partition:
   ```bash
   cryptsetup luksFormat /dev/sdX2
   ```
   - Confirm the operation and set a passphrase.
2. Open the LUKS container:
   ```bash
   cryptsetup open /dev/sdX2 cryptlvm
   ```

---

## Configuring LVM (Logical Volume Manager)

1. Initialize the LVM physical volume:
   ```bash
   pvcreate /dev/mapper/cryptlvm
   ```
2. Create a volume group:
   ```bash
   vgcreate vg0 /dev/mapper/cryptlvm
   ```
3. Create logical volumes:
   - Root:
     ```bash
     lvcreate -L 30G -n root vg0
     ```
   - Swap:
     ```bash
     lvcreate -L 4G -n swap vg0
     ```
   - (Optional) Home:
     ```bash
     lvcreate -l 100%FREE -n home vg0
     ```

---

## Formatting with BTRFS

1. Format the EFI partition as FAT32:
   ```bash
   mkfs.fat -F32 /dev/sdX1
   ```
2. Format the root volume as BTRFS:
   ```bash
   mkfs.btrfs /dev/vg0/root
   ```
3. (Optional) Format the home volume as BTRFS:
   ```bash
   mkfs.btrfs /dev/vg0/home
   ```
4. Initialize swap space:
   ```bash
   mkswap /dev/vg0/swap
   ```

---

## Mounting Partitions

1. Mount the root volume:
   ```bash
   mount /dev/vg0/root /mnt
   ```
2. Create and mount subvolumes:
   ```bash
   btrfs subvolume create /mnt/@
   btrfs subvolume create /mnt/@home
   btrfs subvolume create /mnt/@snapshots
   umount /mnt
   mount -o noatime,compress=zstd,subvol=@ /dev/vg0/root /mnt
   mkdir -p /mnt/{boot,home,.snapshots}
   mount -o noatime,compress=zstd,subvol=@home /dev/vg0/home /mnt/home
   ```
3. Mount the EFI partition:
   ```bash
   mount /dev/sdX1 /mnt/boot
   ```
4. Enable swap:
   ```bash
   swapon /dev/vg0/swap
   ```

---

## Installing Essential Packages

1. Install the base system:
   ```bash
   pacstrap /mnt base linux linux-firmware btrfs-progs lvm2 vim
   ```
2. Generate the fstab file:
   ```bash
   genfstab -U /mnt >> /mnt/etc/fstab
   ```
   Verify the `fstab` entries to ensure the subvolumes and mount options are correct.

---

## Configuring systemd-boot

1. Chroot into the new system:
   ```bash
   arch-chroot /mnt
   ```
2. Install systemd-boot:
   ```bash
   bootctl install
   ```
3. Create a boot entry for Arch Linux:
   - Edit `/boot/loader/loader.conf`:
     ```plaintext
     default arch
     timeout 3
     editor 0
     ```
   - Create `/boot/loader/entries/arch.conf`:
     ```plaintext
     title Arch Linux
     linux /vmlinuz-linux
     initrd /initramfs-linux.img
     options cryptdevice=/dev/sdX2:cryptlvm root=/dev/vg0/root rw
     ```

---

## Generating Filesystem Snapshots with BTRFS

1. Install the `snapper` package:
   ```bash
   pacman -S snapper
   ```
2. Create a configuration for root:
   ```bash
   snapper -c root create-config /
   ```
3. Adjust the configuration file `/etc/snapper/configs/root` as needed.

---

## Final Steps

1. Set the root password:
   ```bash
   passwd
   ```
2. Enable essential services:
   ```bash
   systemctl enable systemd-networkd
   systemctl enable systemd-resolved
   ```

---

## Post-Installation Tasks

1. Boot into your system and verify everything is functioning:
   - Ensure the encryption prompt appears.
   - Confirm the BTRFS subvolumes are mounted correctly.
2. Update the system:
   ```bash
   pacman -Syu
   ```
3. Install additional software as needed:
   ```bash
   pacman -S networkmanager git base-devel
   ```

---

By following this guide, you now have a secure Arch Linux installation with **LVM on LUKS**, **BTRFS**, and **systemd-boot**, optimized for modern SSDs. This setup provides encryption for data security, snapshots for easy backup and rollback, and systemd-boot for a lightweight bootloader experience.

Feel confident in when you tell someone, "I use Arch, BTW", you'll mean it. 