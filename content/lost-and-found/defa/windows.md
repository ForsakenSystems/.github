# Miscellaneous

---

### Table of Contents
- [Windows Share Across Users](#windows-share-across-users)

---

## Windows Share Across Users
- If you have connected a network share for a certain user and want to access it, e.g. via an elevated powershell it might not be visable (e.g. caused by UAC)

```powershell
# create/set DWORD to 1 and reboot
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System\EnableLinkedConnections
```

