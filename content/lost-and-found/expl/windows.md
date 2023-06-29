# Windows

---

### Table of Contents
- [Run Powershell Elevated](#run-powershell-elevated)
- [Disable Windows Defender](#disable-windows-defender)
- [Creating low Integrity cmd](#creating-low-integrity-cmd)

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
