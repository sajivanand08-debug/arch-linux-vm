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


## Step 5: Installing GRUB and the LXQt Desktop

### Installing the GRUB Bootloader

I installed GRUB so Arch Linux could boot from the virtual hard drive.

```bash
grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=GRUB
```

The installation completed without errors.

Next, I generated the GRUB configuration:

```bash
grub-mkconfig -o /boot/grub/grub.cfg
```

This detected the Linux kernel and generated the configuration needed to boot Arch Linux.

GRUB displayed an `os-prober` warning, but this did not prevent the configuration from completing.

### Enabling NetworkManager

I enabled networking to start automatically:

```bash
systemctl enable NetworkManager
```

This ensures the VM can connect to the Internet after startup.

### Installing the LXQt Desktop

I chose LXQt because it is a lightweight graphical desktop environment.

I installed LXQt, Xorg, SDDM, Firefox, and an icon theme:

```bash
pacman -S xorg-server lxqt breeze-icons sddm firefox
```

When prompted to select packages from the LXQt group, I accepted the default selection.

I also accepted the default JACK audio provider.

The packages installed successfully.

### Enabling the Graphical Login Screen

I enabled SDDM:

```bash
systemctl enable sddm
```

SDDM provides a graphical login screen.

I then configured Arch Linux to boot into graphical mode:

```bash
systemctl set-default graphical.target
```

I verified the default startup target:

```bash
systemctl get-default
```

The expected result was:

```text
graphical.target
```

### Results

After completing the installation, I successfully booted into the LXQt desktop and opened QTerminal.


## Step 6: SSH, Zsh, and AUR Installation

### Enabling SSH

I enabled the SSH service using:

```bash
systemctl enable sshd.service
```

This allows the SSH server to start automatically when Arch Linux boots.

I verified the service configuration:

```bash
systemctl is-enabled sshd.service
```

The result was `enabled`.

### Configuring Zsh

I installed Zsh as an alternative to Bash and configured it as my user's default shell.

I edited my configuration file:

```bash
nano /home/sajiv/.zshrc
```

I added a colored terminal prompt and useful aliases:

```bash
PROMPT='%F{cyan}%n@%m%f:%F{green}%~%f %# '

alias ll='ls -lah --color=auto'
alias update='sudo pacman -Syu'
alias myip='ip -br addr'
alias ..='cd ..'
```

The aliases provide shortcuts for listing files, updating packages, viewing IP addresses, and navigating directories.

I assigned ownership of the configuration file:

```bash
chown sajiv:sajiv /home/sajiv/.zshrc
```

I checked the configuration for syntax errors:

```bash
zsh -n /home/sajiv/.zshrc
```

The configuration passed the syntax check.

### Installing an AUR Package

I chose the `yay-bin` package from the Arch User Repository.

I downloaded its build files:

```bash
git clone https://aur.archlinux.org/yay-bin.git
```

I entered the package directory:

```bash
cd yay-bin
```

I reviewed the build instructions:

```bash
less PKGBUILD
```

I installed the package using:

```bash
makepkg -si
```

I verified that the package was installed:

```bash
pacman -Q yay-bin
```

Finally, I verified the program's version:

```bash
command yay --version
```

The command displayed the installed Yay version.

## Step 7: Troubleshooting and Final Results

### Problems Encountered

**Problem 1: Operating System Not Found**

VMware initially failed to boot the installation media.

Solution: I corrected the ISO selection and connected the proper Arch Linux installation image.

**Problem 2: Incorrect Command**

I accidentally typed `ls -sf` instead of `ln -sf` when configuring the time zone.

Solution: I corrected the command.

**Problem 3: Password Command Error**

I typed `password sajiv` instead of `passwd sajiv`.

Solution: I corrected the command and successfully configured the password.

**Problem 4: Sudo Permissions**

The wheel group permission was initially commented out in the sudoers file.

Solution: I enabled the permission and verified the configuration with `visudo -c`.

**Problem 5: Yay Error**

After installing Yay, I encountered an error when checking its version.

I investigated the package installation and Pacman configuration.

The command `command yay --version` successfully displayed the version.

### Final Installation Results

My completed virtual machine includes:

- Arch Linux installed manually using VMware
- A 40 GB virtual hard drive
- UEFI booting using GRUB
- LXQt graphical desktop
- SDDM graphical login
- User accounts: sajiv and codi
- Sudo permissions through the wheel group
- Zsh shell with a colored prompt
- Custom Linux aliases
- NetworkManager and OpenSSH
- Firefox web browser
- Yay installed from the AUR

### Conclusion

This project helped me understand how to install and configure Arch Linux manually.

I learned about disk partitioning, filesystems, Linux user administration, package management, bootloaders, networking, and graphical desktop environments.

I also gained troubleshooting experience by correcting mistakes during the installation process.

### Reference

Official Arch Linux Installation Guide:

https://wiki.archlinux.org/title/Installation_guide


I also launched Firefox and confirmed that the virtual machine could access the Internet.





