# Miscellaneous

---

### Table of Contents
- [SSH Multiple Identities](#ssh-multiple-identities)
- [Grub Rescure Shell vs LUKS](#grub-rescue-shell-vs-luks)
- [Convert m4a to mp3](#convert-m4a-to-mp3)
- [Some Linux IP Commands](#some-linux-ip-commands)
- [Git Reduce Threads Used](#git-reduce-threads-used)
- [Kali EXPKEYSIG](#kali-expkeysig)
- [Windows Share Across Users](#windows-share-across-users)
- [Remmina Not Connecting to Windows](#remmina-not-connecting-to-windows)
- [Exim Mail Queue CleanUp](#exim-mail-queue-cleanup)

---

## SSH Multiple Identities
- If the keychain handles multiple identities, match the correct one without running through all other

```bash
# Use pub key and identities only option
ssh user@a.b.c.d -p22 -i ~/.ssh/id_key.pub -o IdentitiesOnly=yes

# Or in ~/.ssh/config
host myhost
  user user
  hostname a.b.c.d
  port 22
  identityfile ~/.ssh/id_key.pub
  IdentitiesOnly yes
```

## Grub Rescue Shell vs LUKS
- Deppending on the setup (here: `boot` encrypted), it might happen, that some get dropped into `grub rescue shell` when `luks` failed to decrypt

```sh
# id partition to decrypt, e.g. hd0
ls

# mount with or without using UUID (-u)
cryptomount (hd0,gpt2)

# Enter passphrase and if slot gets opened load "normal" module
insmod normal

# Boot as usual
normal
```

## Convert m4a to mp3
- Converting between different audio formats, e.g. `m4a` to `mp3`

```bash
# -v ... verbosity
# -y ... overwrite outputfile
# -acodec ... set audio codec
# -ac ... number of audio channels 
# -ab ... bit rate of audio
ffmpeg -v 5 -y -i INFILE.m4a -acodec libmp3lame -ac 2 -ab 192k OUTFILE.mp3
```

## Some Linux IP Commands
- Of course `RTFM` would work, too ... but still having some stuff in one place is better ^^ 

```bash
# On a system with multiple routes or huge routing tables, it might be helpful to know exactly which `route` would be used beforehand
ip route get 8.8.8.8

# No arp command? No problem
ip neighbour show

# Need a vlan? ... do not forget to turn the device on (up), incl. the parent
ip l a link ethXYZ name ethXYZ.42 type vlan id 42

```

## Git Reduce Threads Used
- Reducing the threads, which are used by `git` client (here: only for the current push)

```bash
git -c pack.threads=1 push
```

## Kali EXPKEYSIG
- If you encounter something like `The following signatures were invalid: EXPKEYSIG ...` the following might be a better idea than loading and adding the key manually

```bash
apt install kali-archive-keyring
```

## Windows Share Across Users
- If you have connected a network share for a certain user and want to access it, e.g. via an elevated powershell it might not be visable (e.g. caused by UAC)

```powershell
# create/set DWORD to 1 and reboot
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System\EnableLinkedConnections
```

## Remmina Not Connecting to Windows
- If `remmina` struggles to successfully connect to a Windows via `rdp`, this could have a lot of possible causes
- Most of the time if the connection is stable etc., the problem might be caused by `tls` settings

```bash
# e.g. (this is an obvious error)
...
Protocol security negotiation failure.
...
```

- What might help is playing arroud with the `Security protocol negotiation` advanced setting and ...
- Enable/disable `Glyph cache` and/or `Relax order checks` advanced setting

## Exim Mail Queue CleanUp
- Check and clean-up `exim` mail queue fast

```bash
# check/view
exim -bp

# flush (forced even for frozen msg)
exim -qff

# delete all from queue
exiqgrep -i | xargs exim -Mrm
```
