# Port Scanning
The first step to most pentests ...

It is important we use an efficient **AND** sufficient scanner. There are several ways to do this and I will go through them here.

#### Nmap
Good ol nmap *ALWAYS* (not rlly) gets the job done. Just a little slower and more lumbersome.

I like using the following two liner to get an initial and aggressive scan.
```bash
sudo nmap -p- --min-rate=10000 box.htb -oN initial.scan
```
Next, use this scan against the ports that we found open.
```bash
sudo nmap -p22,53,80,135,139,445,3389,8000,47001 -A box.htb -oN aggressive.scan
```
This scan provides you with a basic outfile that provides with ports open **AND** an outfile with results from the aggressive scan.

Also, you can always run a UDP scan.
```bash
sudo nmap -sU box.htb -oN udp.scan
```
You might not use `nmap` as your goto but it is important to know how to use it.
