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
