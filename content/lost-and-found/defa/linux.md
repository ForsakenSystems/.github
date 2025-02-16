# Linux and BSD

---

### Table of Contents
- [SSH Multiple Identities](#ssh-multiple-identities)
- [Grub Rescure Shell vs LUKS](#grub-rescue-shell-vs-luks)
- [Convert m4a to mp3](#convert-m4a-to-mp3)
- [Some Linux IP Commands](#some-linux-ip-commands)
- [Kali EXPKEYSIG](#kali-expkeysig)
- [Remmina Not Connecting to Windows](#remmina-not-connecting-to-windows)
- [Exim Mail Queue CleanUp](#exim-mail-queue-cleanup)
- [Temp Change Language](#temp-change-language)
- [Convert From Binary to ASCII](#convert-from-binary-to-ascii)
- [Rolling back package on Debian/Ubuntu](#rolling-back-package-on-debian)
- [Reset Admin Password pfSense](#reset-admin-password-pfsense)
- [Create Bootable Windows From ISO](#create-bootable-windows-from-iso)

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

## Kali EXPKEYSIG
- If you encounter something like `The following signatures were invalid: EXPKEYSIG ...` the following might be a better idea than loading and adding the key manually

```bash
apt install kali-archive-keyring
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

### Remmina Not Connecting to Windows Server (KB5039705 and freerdp v3)
- If `remmina` is unable to connect to Windows via `rdp` and no error message is displayed and just connecting `...`, this could be a kerberos problem
- Check the correct domain in the config for the Windows host you are trying to connect
- If the host is not part of a domain just use `.\`

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

## Temp Change Language
- Sometimes you might need to change the language for only one command, e.g. if you have set you system language to spanish and would like to have the error output of a command in english

```bash
LANG=C $somecommand
```

## Convert From Binary to ASCII
- Simple onliner

```bash
echo "011000110111100101100010011001010111001" | perl -lpe '$_=pack"B*",$_'
cyber
```

## Rolling back package on Debian
- Check dpkg.log to package name and date 
```bash
cat /var/log/dpkg.log
```
- Get old / new version Number of the package during the determined date
```bash
awk '$1=="2024-09-29" && $3=="upgrade"' /var/log/dpkg.log
```
- Check if package is still on disk 
```bash
awk '$1=="2024-09-29" && $3=="upgrade" {gsub(/:/, "%3a", $5); split($4, f, ":"); print "/var/cache/apt/archives/" f[1] "_" $5 "_" f[2] ".deb"}' /var/log/dpkg.log | xargs -r ls -ld
```
- Install the old package
```bash
dpkg -i /var/cache/apt/archives/<PACKAGE_NAME>.deb
```

## Reset Admin Password pfSense
- To reset the admin password of a pfSense two options are possible
  - First one is to connect via `ssh` or simple `console` and choose the corresponding option. This only works if no password protection is enabled.
  - Second one is to connect via `console` and boot into `single user mode` and use the following commands
```zsh
# in single user mode (filesystem zfs)
/sbin/mount -u /

/sbin/zfs mount -a

/sbin/zfs mount pfSense/ROOT/default/cf

/sbin/zfs mount pfSense/ROOT/default/var_db_pkg

/etc/rc.initial.password

/sbin/nextboot -D

/etc/rc.reboot
```

## Create Bootable Windows From ISO
- Since using just `dd` to create a bootable Windows install USB from an ISO is not working, `WoeUSB` might help
- `ventoy` might also be ok
```bash
# WindowsXYZ ... windows install iso
# sdb ... usb drive to install to
woeusb --device WindowsXYZ.iso /dev/sdb
```
