# Common Privesc Methods

For Linux privesc I usually like enumerating all directories and unusual files I have access too, and then, running a common binary and some one-liners.

## Identifying Privesc

Run the following one liners to enumerate your privs on the box:

- `uname -a` == Gives you linux kernel ver.
- `sudo -l` == Shows you where you have SUDO privs.
- `find / -perm -u=s -type f 2>/dev/null` == Locate SUID Binaries.


