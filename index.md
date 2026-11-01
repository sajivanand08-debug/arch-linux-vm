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


## Step 3: Installing the Arch Linux Base System

After formatting and mounting my partitions, I installed the Arch Linux base system.

### Installing Required Packages

I ran the following command:

```bash
pacstrap -K /mnt base linux linux-firmware networkmanager grub efibootmgr sudo nano zsh openssh git base-devel
```

This installed the Linux kernel and essential tools, including networking, the GRUB bootloader, SSH, Zsh, and sudo.

The installation completed successfully.

### Generating the Filesystem Table

Next, I generated the filesystem table:

```bash
genfstab -U /mnt >> /mnt/etc/fstab
```

This recorded the partitions that Arch Linux should mount automatically.

I verified the configuration using:

```bash
cat /mnt/etc/fstab
```

The output confirmed that the Linux root partition and EFI boot partition were configured correctly.

### Entering the Installed System

I then entered the installed Arch Linux environment:

```bash
arch-chroot /mnt
```

This allowed me to configure the operating system before rebooting.

### Installation Notes

During package installation, I encountered a warning that `/etc/vconsole.conf` was missing. However, the initial RAM filesystem was generated successfully.

I was able to continue the installation without needing to reinstall any packages.


## Step 4: Configuring Arch Linux

After installing the base system, I configured the time zone, language, hostname, and user accounts.

### Setting the Time Zone

I configured Arch Linux to use Central Time:

```bash
ln -sf /usr/share/zoneinfo/America/Chicago /etc/localtime
```

This creates a symbolic link to the correct time zone.

I then synchronized the hardware clock:

```bash
hwclock --systohc
```

**Problem encountered:** I initially typed `ls -sf` instead of `ln -sf`, causing an error. I corrected the command, and it executed successfully.

### Configuring the System Language

I opened the locale configuration:

```bash
nano /etc/locale.gen
```

I uncommented the following line:

```text
en_US.UTF-8 UTF-8
```

I generated the language settings:

```bash
locale-gen
```

Then I configured English as the default language:

```bash
echo 'LANG=en_US.UTF-8' > /etc/locale.conf
```

I verified the setting:

```bash
cat /etc/locale.conf
```

The result was `LANG=en_US.UTF-8`.

### Configuring the Hostname

I named my virtual machine `archvm`:

```bash
echo 'archvm' > /etc/hostname
```

I verified the hostname:

```bash
cat /etc/hostname
```

### Setting the Root Password

I ran:

```bash
passwd
```

I entered and confirmed a password for the root account. Linux does not display characters when entering passwords.

### Creating User Accounts

I created two accounts named `codi` and `sajiv`.

```bash
useradd -m -G wheel -s /bin/zsh codi
passwd codi
```

```bash
useradd -m -G wheel -s /bin/zsh sajiv
passwd sajiv
```

The `-m` option creates home directories, `-G wheel` adds users to the administrative group, and `-s /bin/zsh` sets Zsh as their default shell.

### Configuring Sudo Permissions

I opened the sudo configuration using:

```bash
EDITOR=nano visudo
```

I uncommented:

```text
%wheel ALL=(ALL:ALL) ALL
```

This allows users in the `wheel` group to execute administrative commands using sudo.

I checked the configuration:

```bash
visudo -c
```

The output confirmed `/etc/sudoers: parsed OK`.

Finally, I verified both accounts:

```bash
id sajiv
id codi
```

Both accounts belonged to the `wheel` group.

### Additional Troubleshooting

I initially typed `password sajiv` instead of `passwd sajiv`. The terminal reported that the command was not found.

I corrected the command and successfully configured the account password.




