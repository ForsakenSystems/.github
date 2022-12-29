# Web Related Stuff

---

### Table of Contents
- [PHP](#php)
  * [Enable Error Logging](#enable-error-logging)
- [Python](#python)
  * [Prevent Request Encoding](#prevent-request-encoding)

---

## PHP
Stuff related to `php`.

### Enable Error Logging
- Usually adjusting `php.ini` (e.g. `/etc/php/8.1/apache2/php.ini`) as follows is enough (restart/reload of service afterwards may be needed)

```bash
display_errors = On
```

## Python
Stuff related to `python` in terms of web.

### Prevent Request Encoding
- Prevent python requests from url encoding when doing `POST` might not be ideal (is some kind of standard?), but might be needed.
- For this just convert the data to string.

```python
val = ';COPY+'

data = {'attr1': 1,
	'attr2': "%s" % val
       }

data = "&".join("%s=%s" % (k,v) for k,v in data.items())

r = requests.post('https://127.0.0.1/index.php', data=data)
```

