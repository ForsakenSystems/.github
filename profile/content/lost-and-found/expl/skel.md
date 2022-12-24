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
vp = 0x41414141   # VirtualProtect addy
vp += 0x42424242  # shellcode return addy to return to, after VirtualProtect was called
vp += 0x43434343  # lpAddress ---> same as above
vp += 0x44444444  # dwSize ---> size of shellcode (0x400 or something like that)
vp += 0x45454545  # flNewProtect ---> 0x40
vp += 0x46464646  # lpflOldProtect ---> some writable memory address)
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
va += 0x42424242  # shellcode return addy to return to, after VirtualAlloc was called
va += 0x43434343  # lpAddress ---> shellcode addy
va += 0x44444444  # dwSize ---> 0x1
va += 0x45454545  # flAllocationType ---> 0x1000
va += 0x46464646  # flProtect ---> 0x40
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
wpm = 0x41414141    # WriteProcessMemory addy
wpm += 0x42424242   # shellcode return addy to return to, after WriteProcessMemory was called
wpm += 0xffffffff   # hProcess ---> pseudo Process handle ---> -1
wpm += 0x44444444   # lpBaseAddress ---> addy of code cave
wpm += 0x45454545   # lpBuffer ---> shellcode addy
wpm += 0x46464646   # nSize ---> shellcode size
wpm += 0x47474747   # lpNumberOfBytesWritten ---> writable memory addy
```
