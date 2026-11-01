# My Arch Linux VM Installation Documentation

## Introduction

This document describes how I manually installed and configured Arch Linux using VMware.

I installed the LXQt desktop environment, configured user accounts, enabled sudo and SSH, customized Zsh, and installed an AUR package.

## Step 1: Creating My Virtual Machine

I used VMware Workstation to create my Arch Linux virtual machine.

### VM Configuration

- Operating System: Arch Linux (64-bit)
- RAM: 4 GB
- Processors: 2
- Virtual Hard Drive: 40 GB
- Firmware: UEFI
- Network: NAT
- Desktop Environment: LXQt

### Downloading Arch Linux

I downloaded the Arch Linux ISO from:
https://archlinux.org/download/

### Problems Encountered

Initially, VMware displayed an
"Operating System not found" error.

I discovered that I had selected the wrong
installation file. I also encountered an
Ubuntu installation menu instead of Arch Linux.

I corrected this by downloading the proper
Arch Linux ISO and selecting it under
VMware's CD/DVD settings.

After connecting the correct ISO, I successfully
booted into the Arch Linux installation terminal.

### Checking Internet Connectivity

I used the following command:

```bash
ping -c 4 archlinux.org
```

This tested my Internet connection.

The command successfully received a response,
confirming that my virtual machine had
Internet access.


## Step 2: Partitioning the Virtual Hard Drive

After booting Arch Linux, I identified my virtual hard drive.

### Identifying the Disk

I ran:

```bash
lsblk
```

This showed my 40 GB virtual hard drive as `/dev/sda`.

### Creating the Partitions

I opened the partition editor:

```bash
cfdisk /dev/sda
```

I used the GPT partition table and created two partitions:

- `/dev/sda1`: 1 GB, EFI System
- `/dev/sda2`: 39 GB, Linux filesystem

I selected Write, confirmed the changes, and exited.

### Formatting the Partitions

I formatted the EFI partition as FAT32:

```bash
mkfs.fat -F 32 /dev/sda1
```

I formatted the Linux partition as ext4:

```bash
mkfs.ext4 /dev/sda2
```

Both commands completed successfully.

### Mounting the Partitions

I mounted the Linux filesystem:

```bash
mount /dev/sda2 /mnt
```

Then I mounted the EFI partition:

```bash
mount --mkdir /dev/sda1 /mnt/boot
```

Finally, I verified the mount points:

```bash
lsblk
```

The output confirmed that both partitions were mounted correctly.


