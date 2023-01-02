# Miscellaneous

---

### Table of Contents
- [SSH Multiple Identities](#ssh-multiple-identities)

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


