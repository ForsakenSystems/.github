# Web Related Stuff

---

### Table of Contents
- [PHP](#postgresql)
  * [Enable Error Logging](#enable-error-logging)

---

## PHP
Stuff related to `php`.

### Enable Error Logging
- Usually adjusting `php.ini` (e.g. `/etc/php/8.1/apache2/php.ini`) as follows is enough (restart/reload of service afterwards may be needed)

```bash
display_errors = On
```
