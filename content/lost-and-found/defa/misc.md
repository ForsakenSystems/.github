# Miscellaneous

---

### Table of Contents
- [SSH Multiple Identities](#ssh-multiple-identities)
- [Grub Rescure Shell vs LUKS](#grub-rescue-shell-vs-luks)

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
