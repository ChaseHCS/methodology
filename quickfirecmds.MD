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
ffuf -w ~/Seclists -u https://$ip -H "Host: FUZZ.$ip.oscp" ~/SecLists/Discovery/DNS/subdomains-top1million-20000.txt  -ac
```

### Parameter fuzzing
```bash
ffuf -w ~/Seclists -u https://$ip/script.php?FUZZ=test_value -fs 4242
ffuf -w /Seclists -u https://$ip/script.php?valid_name=FUZZ -fc 401
```

### Nikto
```bash
cd ~/binaries/nikto
./nikto.pl -h http://$ip -output nikto
```
