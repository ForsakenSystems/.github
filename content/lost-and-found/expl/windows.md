# Windows

---

### Table of Contents
- [Run Powershell Elevated](#run-powershell-elevated)
- [Disable Windows Defender](#disable-windows-defender)
- [Creating low Integrity cmd](#creating-low-integrity-cmd)
- [WinDbg Fu](#windbg-fu)
	- [Grep Like Feature](#grep-like-feature)
	- [ASCII to DWORDS](#ascii-to-dwords)
	- [Levels not Implemented for This Platform](#levels-not-implemented-for-this-platform)
	- [SMAP During Debugging](#smap-during-debugging)

---

# Run Powershell Elevated
- Sometime is might be needed to start an elevated powershell from commandline

```powershell
# Directly from cmd
powershell -Command "Start-Process PowerShell -Verb RunAs"

# Start powershell first and than elevate
powershell
Start-Process PowerShell -Verb RunAs
```

## Disable Windows Defender
- In general (not directly working after compromise, even with system privileges )

```powershell
# use "query" to check status
sc stop WinDefend
```

- ... use one or more of these

```powershell
# Remove without reboot
Dism /online /Disable-Feature /FeatureName:Windows-Defender /Remove /NoRestart /quiet

# ... with reboot
Uninstall-WindowsFeature -Name Windows-Defender

# Disable real-time-protection
Set-MpPreference -DisableRealtimeMonitoring $true

# Exclude path
Set-MpPreference -ExclusionPath PATH_HERE

# Remove signatures
& 'C:\Program Files\Windows Defender\MpCmdRun.exe' -removedefinitions

# Disable application guard
Disable-WindowsOptionalFeature -FeatureName "Windows-Defender-ApplicationGuard" -Online
```

## Creating low Integrity cmd
- Creating cmd.exe with low integrity (here: from commmon cmd.exe, which runs by default as medium integrity)
- Same works for switching to high integrity, but this has to be done with elevated privileges: high > medium > low

```powershell
copy C:\Windows\System32\cmd.exe cmd_low.exe

icacls cmd_low.exe /setintegritylevel low

icacls.exe cmd_low.exe
	cmd_low.exe NT AUTHORITY\SYSTEM:(F)
	               BUILTIN\Administrators:(F)
	               PC\useer:(F)
	               NT AUTHORITY\SYSTEM:(I)(F)
	               BUILTIN\Administrators:(I)(F)
	               PC\user:(I)(F)
	               Mandatory Label\Low Mandatory Level:(NW)
```

## WinDbg Fu
- Just some `WinDbg` commands for the lazy

### Grep Like Feature
- Grep like feature by using `.shell` command

```powershell
.shell -ci "lm" findstr /i kern 
```

### ASCII to DWORDS
- Create DWORDS from an ASCII string, useful for in terms of, e.g. shellcoding
- Same works for `unicode` with `eu`

```powershell
ea rsp+8 "\\\\192.168.73.128\\pwn\\met.dll"

da rsp+8
	00000044`cf0ffef0  "\\192.168.73.128\pwn\met.dll"

dd rsp+8 L8
	00000044`cf0ffef0  39315c5c 36312e32 33372e38 3832312e
	00000044`cf0fff00  6e77705c 74656d5c 6c6c642e 00000000
```

### Levels not Implemented for This Platform
- If `pte` command, e.g `!pte 0x0` reports `Levels not implemented for this platform`, a reboot of the system might help
- If this happens during kernel debugging, it might help to reboot both, debugger and debuggee

```powershell
!pte 0x0
	Levels not implemented for this platform

# reboot

!pte 0x0
	                                           VA 0000000000000000
	PXE at FFFFADD6EB75B000    PPE at FFFFADD6EB600000    PDE at FFFFADD6C0000000    PTE at FFFFAD8000000000
	contains 8A00000082A29867  contains 0000000000000000
	pfn 82a29     ---DA--UW-V  contains 0000000000000000
	not valid
```

### SMAP During Debugging
- If Supervisor Mode Access Prevention (SMAP) comes in your way during debugging, this might be useful

```powershell
r cr4
	cr4=0000000000370678

# switch 3 to 1
r cr4=0000000000170678

r cr4
	cr4=0000000000170678
```








