# Windows

---

### Table of Contents
- [Windows Share Across Users](#windows-share-across-users)
- [Set Windows NTP Server](#set-windows-ntp-server)

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

