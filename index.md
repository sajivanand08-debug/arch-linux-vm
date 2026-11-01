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

