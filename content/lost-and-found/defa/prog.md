# Miscellaneous

---

### Table of Contents
- [Python Flask TypeError send_file](#python-flask-typeerror-send-file)
- [Perlbrew](#perlbrew)
---

## Python Flask TypeError send_file
- If a script is build for an older version of `python flask` following error might occur:

```bash
TypeError: send_file() got an unexpected keyword argument 'attachment_filename'
```

- Fix this by using `download_name` instead of `attachment_filename` (see [here](https://github.com/pallets/flask/issues/4753))

## Perlbrew
- Install this handy management tool for `perl` installations, e.g. on Arch

```bash
sudo pacman -S perlbrew
perlbrew init
```

- For `cpan` stuff

```bash
perlbrew install-patchperl
perlbrew install-cpanm
```

- List `perl` versions available and install one

```bash
# List
perlbrew available
  perl-x.yz.a
  ...

# Install
perlbrew install perl-x.yz.a
```

- Make a specific version permanent or similar stuff

```bash
# Current
which perl
  /usr/bin/perl

# List current used
perlbrew list
  * perl-x.yz.a
    perl-a.ab.c

# Switch version
perlbrew switch perl-a.ab.c

perlbrew use
  Currently using perl-a.ab.c

which perl
  /home/user/perlA/perlbrew/perls/perl-a.ab.c/bin/perl 
```

- Disable usage permanent `perlbrew` usage

```bash
perlbrew switch-off
```

- Exec script with specific version

```bash
perlbrew exec perl programm.pl
```

- Update `cpan` modules

```bash
# Needed dep
cpanm App::cpanoutdated

# Update outdated
cpan-outdated -p | cpanm
```

- Reinstall modules, e.g. when changing `perl` version

```bash
# Old version
perlbrew use perl-x.yz.a

# List all modules and install with new version (e.g. perl-a.ab.c)
perlbrew list-modules | perlbrew exec --with perl-a.ab.c cpanm
```

- Troubleshooting

```bash
# Perl switch is not working permanently
echo 'source ~/perl5/perlbrew/etc/bashrc' >> ~/.bash_profile

# Threaded perl needed (not a good idea ^^)
perlbrew install perl-a.ab.c -Dusethreads --as threaded-perl-a.ab.c

# Install crashes - check language
cat /etc/locale.conf
  LC_MESSAGES=en_US.UTF-8

## Or just add
echo 'export LC_MESSAGES=en_US.UTF-8' >> ~/.bashrc
```
