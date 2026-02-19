# Patches for WSL to PCI passthrough

This repository contains patches to enable PCI passthrough on WSL VM. Server edition of Windows is required because it rely on a feature of server kernel.

This patch enables DDA (Discrete Device Assignment) via HCS API.

## Prerequisites

Get SurveyDDA.ps1 script from [this link](https://learn.microsoft.com/en-us/windows-server/virtualization/hyper-v/deploy/deploying-graphics-devices-using-dda). And run it from admin power shell.
Check if your device's location path from the output of the script. And then run:
```
> Dismount-VMHostAssignableDevice -LocationPath $locationPath
# If it failed, try the following:
> Dismount-VMHostAssignableDevice -Force -LocationPath $locationPath
```

## Config

Open `Device Manager` -> `Storage controllers` -> `Standard NVM Express Controller` -> `Detail` -> `Device instance path`. Then copy the value and replace `PCI\VEN...` with `PCIP\VEN...`.

Put the following config in ~/.wslconfig \[wsl2\] section.
```
[wsl2]
pciPassthroughDevices=PCIP\\VEN_144D&DEV_A80D&SUBSYS_A801144D&REV_00\\4&447857B&0&00D0
```

Only tested on NVMe disk. GPU passthrough did not work on my test.

## Limitations

1. Only work on windows server
2. VM memory must be allocated on boot. That is, dynamic allocation or shrinking are not supported.
