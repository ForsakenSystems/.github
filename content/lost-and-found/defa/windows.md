# Windows

---

### Table of Contents
- [Windows Share Across Users](#windows-share-across-users)
- [Set Windows NTP Server](#set-windows-ntp-server)
- [Windows 11 Install Without Internet](#windows-11-install-without-internet)
- [Hyper-V Nested Virtualization](#hyper-v-nested-virtualization)
- [Remote Desktop Shared Folder Not Showing up](#remote-desktop-shared-folder-not-showing-up)
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

## Windows 11 Install Without Internet
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

## Hyper-V Nested Virtualization
- To enable nested virtualization or lets say the cpu feature needed
```ps
# Choose tareget VM
Get-VM

Name                                                                             State   CPUUsage(%) MemoryAssigned(M)
----                                                                             -----   ----------- -----------------
WinServer2025                                                                      Running 0           16192

# Expose cpu features
Set-VMProcessor -VMName 'WinServer2025' -ExposeVirtualizationExtensions $True
```

## Remote Desktop Shared Folder Not Showing up
- Linux user might have problems to redirect a shared folder to at least Windows 11 or Server 2025 when using `remmina` or `freexrdp`
- A solution could be to do the following:
  - use `gpgpedit.msc` to edit local group policy
  - Set `Do not allow drive redirection` to `enabled`
```ps
Administrative Templates
    Windows Components
        Remote Desktop Services
            Remote Desktop Session Host
                Device and Resource Redirection
```

