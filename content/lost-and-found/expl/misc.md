# Miscellaneous

---

### Table of Contents
- [Simple Webserver](#simple-webserver)
- [Simple SMTP Server](#simple-smtp-server)
- [Simple SMB Server](#simple-smb-server)
- [Simple WebDAV Server](#simple-webdav-server)

---

## Simple Webserver
- Python

```bash
python3 -m http.server 9090

# Deprecated, but still sometimes an option
python2 -m SimpleHTTPServer 9090
```

- Php

```bash
# Use -t to specify webroot
php -S 0.0.0.0:9090
```

- Perl

```bash
# Module HTTP::Server::Brick needed
perl -MHTTP::Server::Brick -e '$s=HTTP::Server::Brick->new(port=>9090); $s->mount("/"=>{path=>"."}); $s->start'
```

- Ruby

```bash
ruby -run -ehttpd . -p9090
```

## Simple SMTP Server
- Python

```bash
# Add -c DebuggingServer for discard msg
python3 -m smtpd -n 0.0.0.0:25
```

## Simple SMB Server
- Python

```bash
# Needy options: -smb2support -user USER -password PASS
# https://raw.githubusercontent.com/SecureAuthCorp/impacket/master/examples/smbserver.py
python3 smbserver.py SHARENAME .

# If impacket stuff is installed
impacket-smbserver SHARENAME .
```

## Simple WebDAV Server
- Python

```bash
# Maybe install package python-wsgidav/python3-wsgidav or similar, otherwise use pip/pip3 install wsgidav
wsgidav --host=0.0.0.0 --port=80 --auth=anonymous --root /directory
```
