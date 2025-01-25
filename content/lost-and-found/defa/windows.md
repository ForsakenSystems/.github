# Windows

---

### Table of Contents
- [Windows Share Across Users](#windows-share-across-users)
- [Set Windows NTP Server](#set-windows-ntp-server)
- [Windows 11 Install Without Internet](#windows-11-install-without-internet)
---

## Windows Share Across Users
- If you have connected a network share for a certain user and want to access it, e.g. via an elevated powershell it might not be visable (e.g. caused by UAC)

```powershell
# create/set DWORD to 1 and reboot
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System\EnableLinkedConnections
```

## Set Windows NTP Server
- Per default Windows is calling Mircosoft NTP servers, to change this do the following

```powershell
# set new ntp server
Set-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Services\w32time\Parameters" -Name "NtpServer" -Value "my.fancy.ntpserver"

# restart service
Restart-Service w32Time

# sync
w32tm /resync

# check
w32tm /query /status
```

# Windows 11 Install Without Internet
- Since `M$` is forcing to use a Microsoft account for installation of 11, hence an active internet connection, this sucks for some people
- `Out of Office Experience` for the help
```cmd
# Step through installation procedure until network requirements
# Press 
SHIFT + F10

# In the the opened command prompt enter
oobe\bypassnro.cmd

# after automatic reboot, step through installation procedure again ...
# ... in the network requirements menu, you can now use the "I don't have internet" option
```
