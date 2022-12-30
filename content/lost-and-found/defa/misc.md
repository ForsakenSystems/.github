# Miscellaneous

---

### Table of Contents
- [Python Flask TypeError send_file](#python-flask-typeerror-send)

---

## [Python Flask TypeError send_file]
- If a script is build for an older version of `python flask` follwoing error might occur:

```bash
TypeError: send_file() got an unexpected keyword argument 'attachment_filename'
```

- Fix this by using `download_name` instead of `attachment_filename` (see [here](https://github.com/pallets/flask/issues/4753))


