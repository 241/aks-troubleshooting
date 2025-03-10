---
title: 'Using ProcDump'
---

## Scenario

Install procdump from the URL below
https://github.com/microsoft/ProcDump-for-Linux/blob/master/INSTALL.md#debian-12

#### 1. Register Microsoft key and feed
```sh
wget -q https://packages.microsoft.com/config/debian/12/packages-microsoft-prod.deb -O packages-microsoft-prod.deb
sudo dpkg -i packages-microsoft-prod.deb
```

#### 2. Install Procdump
```sh
sudo apt-get update
sudo apt-get install apt-transport-https
sudo apt-get update
sudo apt-get install procdump
```


### Using Procdump

```sh
procdump 1 /tmp
procdump -c 50 1 /tmp
```
