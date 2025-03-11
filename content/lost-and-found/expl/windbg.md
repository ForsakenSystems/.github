# WinDbg FuFu

---

### Table of Contents
- [Grep Like Feature](#grep-like-feature)
- [ASCII to DWORDS](#ascii-to-dwords)
- [Levels not Implemented for This Platform](#levels-not-implemented-for-this-platform)
- [SMAP During Debugging](#smap-during-debugging)
- [Mona in WinDbg](#mona-in-windbg)
- [Conditional Breakpoints](#conditional-breakpoints)
- [Enable Debug Print](#enable-debug-print)
- [Misc Commands](#misc-commands)
---


## Grep Like Feature
- Grep like feature by using `.shell` command

```powershell
.shell -ci "lm" findstr /i kern 
```

## ASCII to DWORDS
- Create DWORDS from an ASCII string, useful for in terms of, e.g. shellcoding
- Same works for `unicode` with `eu`
- Be aware it might be more reasonbale to use `.dvalloc` to allocate some space and use this, instead of messing around with `rsp`

```powershell
ea rsp+8 "\\\\192.168.73.128\\pwn\\met.dll"

da rsp+8
	00000044`cf0ffef0  "\\192.168.73.128\pwn\met.dll"

dd rsp+8 L8
	00000044`cf0ffef0  39315c5c 36312e32 33372e38 3832312e
	00000044`cf0fff00  6e77705c 74656d5c 6c6c642e 00000000
```

## Levels not Implemented for This Platform
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

## SMAP During Debugging
- If Supervisor Mode Access Prevention (SMAP, bit 21) comes in your way during debugging, this might be useful
- For Supervisor Mode Execution Prevention (SMEP, bit 20) needs to be zeroed out

```powershell
r cr4
	cr4=0000000000370678

# switch 3 to 1, zero'ing out bit 21
r cr4=0000000000170678

r cr4
	cr4=0000000000170678
```

## Mona in WinDbg
- If you want ot use `mona` in `WinDbg` ... writeup provided by [@yurgiur](https://github.com/yurgiur)
- Installation:
  - `Mona` needs python2 -> Path to python2 should be in PATH variable
  - Be sure that `Windows Modules Installer` Service is running
  - Install python2 and add it to PATH variable
  - Download [pykd.zip](https://github.com/corelan/windbglib/raw/master/pykd/pykd.zip) and save it to a temporary location on your computer
  - Check the properties of the file and "Unblock" the file if necessary
  - Extract the archive, you should get two files: `pykd.pyd` and `vcredist_x86.exe`
  - Run `vcredist_x86.exe` with administrator privileges and accept the default values
  - Copy `pykd.pyd` to `C:\Program Files (x86)\Windows Kits\8.0\Debuggers\x86\winext` or `C:\Program Files (x86)\Windows Kits\10\Debuggers\x86\winext`
  - Open a command prompt with administrator privileges and run the following commands:

  ```powershell
  c:
  cd "C:\Program Files (x86)\Common Files\Microsoft Shared\VC"
  regsvr32 msdia90.dll
  (You should get a messagebox indicating that the dll was registered successfully)
  ```

  - Download [windbglib.py](https://github.com/corelan/windbglib/raw/master/windbglib.py)
  - Save the file under `C:\Program Files (x86)\Windows Kits\8.0\Debuggers\x86` or `C:\Program Files (x86)\Windows Kits\10\Debuggers\x86` ("Unblock" the file if necessary)
  - Download [mona.py](https://github.com/corelan/mona/raw/master/mona.py)
  - Save `mona.py` under `C:\Program Files (x86)\Windows Kits\8.0\Debuggers\x86` or `C:\Program Files (x86)\Windows Kits\10\Debuggers\x86` ("Unblock" the file if necessary)

- Basic options:
  - Set workdir: `!py mona config -set workingfolder c:\logs\%p`
  - `-o`: Ignore OS modules
  - `-m`: Specify module names to search in, e.g. `-m "gtk*,*win*,shell32.dll"`
  - `-cm`: Set criteria a module should comply with, e.g. `-cm aslr=false,safeseh=false,rebase=false`
  - `-cp`: Set criteria a pointer should match, e.g. `-cp asciipint` -> only show pointers with pritable bytes
  - `-cpb`: Specify bad chars, e.g. `-cpb '\x00\x0a\x0d'`

- Locate EIP:
  - Pattern create: `!py mona pattern_create 5000` (pattern is generated in text file in workdir)
  - Pattern offset: `!py mona pattern_offset [EIP_VALUE]`

- Generate rop chain without bad chars
  - `!py mona rop -m "[Module names]" -cpb '[Bad chars]'`
  - `!py mona rop -m "libeay32IBM019" -cpb '\x00\x09\x0a\x0b\x0c\x0d\x20'`
  - Files:
    - `rop.txt`: All gadgets, interessting and other
    - `rop_suggestions.txt`: Suggested gadgets grouped by meaning
    - `rop_chains.txt`: ROP chains for VirtualAlloc and VirtualProtect in ruby, python, javascript and C
    - `stackpivot.txt`: Instructions that add a value to ESP; organized in two sections: Non-Safeseh protected, Safeseh protected

- Find references to a module name with filter
  - Find all references to a module name in all non-ASLR/non-rebase modules
  - `!py mona getiat -cm aslr=false,rebase=false -s [MODULE NAME]`


## Conditional Breakpoints
- Simple software breakpoint to print some values (no conditional breakpoint)
```powershell
bp ntdll+31337 ".printf \"alloc(0x%x) = 0x%p from 0x%p \\n\", poi(rsp+50), rax, poi(rsp); g"
```

- Compare `QWORD` in register `rcx` at offset `0x18` with value `0x300` (processor breakpoint)
```powershell
ba e1 /p <process> example+1337 ".if ( qwo(@rcx+0x18) != 0x300 ) { dqs @rcx+0x18 L1; } .else { gc; }
```

## Enable Debug Print
- Enabling `DbgPrint` monitoring can be done either by `registry key` or in `WinDbg`

```cmd
# WinDbg
ed kd_default_mask 0xf

# Registry
- Computer\HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Session Manager\Debug Print Filter
- Add DWORD
  - Value name: Default
  - Value data: 0xf
```

## Misc Commands
- Just some commands

```md
# help
.hh <command>
.hh !<command>

# Infos on structs
dt <module>!<struct>
dt <module>!<struct> <address>
dt <module>!<struct> <address> -y <keyword/attribute>

dt nt!_EPROCESS
dt nt!_EPROCESS <address by e.g. !process command> -y Unique
dt nt!_EPROCESS <address by e.g. !process command> -y Unique -y Header

# dq vs !dq --> virt vs  physical addr
dq <address> 
!dq <address>

# Module Infos
lm 
lm m <keyword/modulename>
lm a <address to check if belongs to a module>




```







