# Miscellaneous

---

### Table of Contents
- [Git Reduce Threads Used](#git-reduce-threads-used)

---

## Git Reduce Threads Used
- Reducing the threads, which are used by `git` client (here: only for the current push)

```bash
git -c pack.threads=1 push
```

