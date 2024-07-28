# Miscellaneous

---

### Table of Contents
- [Git Reduce Threads Used](#git-reduce-threads-used)
- [Git Adjust HTTP Post Buffer](#git-post-buffer)
- [Git Change HTTP Version](#git-change-http-version)

---

## Git Reduce Threads Used
- Reducing the threads, which are used by `git` client (here: only for the current push)

```bash
git -c pack.threads=1 push
```

## Git Adjust HTTP Post Buffer
- Sometimes a good idea when not working with `ssh` but `https` instead

```
git config --global http.postBuffer 157286400
```

## Git Change HTTP Version
- Mght be useful in case of errors like `error: RPC failed; curl 92 HTTP/2 stream 5 was not closed cleanly: CANCEL (err 8)`

```bash
git config --global http.version HTTP/1.1
# do stuff
git config --global http.version HTTP/2
```
