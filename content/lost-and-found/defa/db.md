# Database Related Stuff

---

### Table of Contents
- [PostgreSQL](#postgresql)
  * [Enable Logging Queries](#enable-logging-queries)
- [MySQL](#mysql)
  * [Enable Logging Queries](#enable-logging-queries)

---

## PostgreSQL
Stuff related to `postgresql`.

### Enable Logging Queries
- Usually adjusting `postgres.conf` (main config usually `/etc/postgresql/_VERSION_/main/postgresql.conf`) as follows is enough (restart/reload of service afterwards may be needed)

```bash
log_statement = 'all'
```

## MySQL
Stuff related to `mysql`.

### Enable Logging Queries
- Usually adjusting `my.cnf` (main config usually `/etc/mysql/my.cnf`) as follows is enough (restart/reload of service afterwards may be needed)

```bash
# section [mysqld]
general_log             = 1
general_log_file        = /var/log/mysql/mysql.log
```
