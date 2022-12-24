# Database Related Stuff

---

### Table of Contents
- [PostgreSQL](#postgresql)
  * [Enable Logging Queries](#enable-logging-queries)

---

## PostgreSQL
Stuff related to `postgresql`.

### Enable Logging Queries
- Usually adjusting `postgres.conf` as follows is enough (restart/reload of service afterwards may be needed)

```bash
log_statement = 'all'
```
