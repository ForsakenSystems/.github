# Miscellaneous

---

### Table of Contents
- [Git Reduce Threads Used](#git-reduce-threads-used)
- [Git Adjust HTTP Post Buffer](#git-post-buffer)
- [Git Change HTTP Version](#git-change-http-version)
- [Git Remove file from history](#remove-file-from-git-history)

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

## Remove file from git history
- Sometimes a file gets committed accidentally, removing it from git history is challenging
- With git-filter-branch you can filter and remove parts of commits
```bash
git filter-branch --force --index-filter \
  'git rm --cached --ignore-unmatch directory/and/filename.extension' \
  --prune-empty --tag-name-filter cat -- --all
```
- Remove the remaining references to the old history in the local repository
```bash
git for-each-ref --format='delete %(refname)' refs/original | git update-ref --stdin
```
- Now expire the reflog
```bash
git reflog expire --expire=now --all
```
- Local repo still contains objects for olf commits, but without refs - objects itself need to be removed
```bash
git gc --prune=now
```
- Push rewritten  history
```bash
git push origin --force --all
git push origin --force --tags
```

- If necessary, update other clones
```bash
git fetch origin 
git reset --hard origin/master
```
- Old commits need to be removed in order to cleanup the local clone
```bash
git for-each-ref --format='delete %(refname)' refs/original | git update-ref --stdin
git reflog expire --expire=now --all
git gc --prune=now
```
