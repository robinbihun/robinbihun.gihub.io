---
title:  "How to install the DisplayLink drivers on Fedora 43"
date:   2025-10-28 13:00:00 -0500"
categories:
  - troubleshooting
tags:
  - displaylink
  - softare
  - configuration
---

# Setup
Ensure you remove any older copies of the evdi driver, especially if you are upgrading from a previous version of Fedora.
```
sudo dnf remove displaylink 'evdi'
```

## Install Pre-Requisites
```
sudo dnf install python3-pybind11 python3-config
```

## Setup C-Flags
These will ignore warnings as errors and setup the path to your python dependencies

```
export CFLAGS=-w
export CPLUS_INCLUDE_PATH="/usr/include/python3.14:$CPLUS_INCLUDE_PATH"
```

## Building evdi

```
git clone https://github.com/DisplayLink/evdi
cd evdi
make
sudo make install
```

# Install DisplayLink
There are community supported rpm packages that setup services for displaylink, after installing evdi manually you should be able to use a package build for Fedora 42 until newer packages are available.

Download the most appropriate file from the [displaylink-rpm github releases page](https://github.com/displaylink-rpm/displaylink-rpm/releases). At the time of this writing, I downloaded `fedora-42-displaylink-1.14.10-1.github_evdi.x86_64.rpm`.

Install the rpm package with
```
sudo dnf install fedora-42-displaylink-1.14.10-1.github_evdi.x86_64.rpm
```

After a reboot (or `systemctl restart displaylink-drivers`) the module should be loaded and your external displays should "just work."

