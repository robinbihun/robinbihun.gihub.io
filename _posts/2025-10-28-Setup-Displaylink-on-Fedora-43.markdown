---
title:  "How to install the DisplayLink drivers on Fedora 43"
date:   2025-10-28 13:00:00 -0500
categories:
  - troubleshooting
tags:
  - displaylink
  - software
  - configuration
---

DisplayLink technology enables USB-connected displays and docking stations, allowing you to extend your desktop workspace beyond your laptop's built-in ports. However, getting DisplayLink to work on Fedora 43 requires some manual setup, as the drivers aren't included in the default repositories.

This guide walks you through installing the necessary EVDI (Extensible Virtual Display Interface) kernel module and DisplayLink drivers to get your USB displays working seamlessly.

## Clean Up Previous Installations

If you're upgrading from a previous version of Fedora, remove any older copies of the DisplayLink and EVDI drivers to avoid conflicts:
```
sudo dnf remove displaylink evdi
```

## Install Build Dependencies

Before compiling the EVDI kernel module, you'll need some development tools and Python bindings:

```
sudo dnf install python3-pybind11 python3-config
```

## Configure Compiler Flags

The EVDI module needs specific compiler flags to build successfully on Fedora 43. These flags prevent build warnings from being treated as errors and ensure the compiler can find Python header files:

```
export CFLAGS=-w
export CPLUS_INCLUDE_PATH="/usr/include/python3.14:$CPLUS_INCLUDE_PATH"
```

## Build and Install the EVDI Kernel Module

Now you're ready to compile the EVDI kernel module from source:

```
git clone https://github.com/DisplayLink/evdi
cd evdi
make
sudo make install
```

This process clones the official DisplayLink EVDI repository, compiles the kernel module, and installs it into your system.

## Install the DisplayLink Driver Package

The community-maintained displaylink-rpm project provides pre-built packages that set up the necessary services and configurations. Since Fedora 43 packages may not be available yet, the Fedora 42 package should work fine after manually installing EVDI:

1. Download the appropriate RPM from the [displaylink-rpm GitHub releases page](https://github.com/displaylink-rpm/displaylink-rpm/releases). At the time of writing, `fedora-42-displaylink-1.14.10-1.github_evdi.x86_64.rpm` works well.

2. Install the RPM package:
```
sudo dnf install fedora-42-displaylink-1.14.10-1.github_evdi.x86_64.rpm
```

## Activate DisplayLink

After installation, you can either reboot your system or restart the DisplayLink service:

```
sudo systemctl restart displaylink-driver
```

Once the service is running, your USB-connected displays should be automatically detected and ready to use!

## Troubleshooting

If your displays don't appear after following these steps:
- Verify the EVDI module is loaded: `lsmod | grep evdi`
- Check the DisplayLink service status: `sudo systemctl status displaylink-driver`
- Review system logs for errors: `journalctl -xeu displaylink-driver`

