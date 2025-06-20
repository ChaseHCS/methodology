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
sudo nmap -p22,53,80,135,139,445,3389,8000,47001 -A -sCV box.htb -oN aggressive.scan
```
This scan provides you with a basic outfile that provides with ports open **AND** an outfile with results from the aggressive scan.

Also, you can always run a UDP scan.
```bash
sudo nmap -sU box.htb -oN udp.scan
```
You might not use `nmap` as your goto but it is important to know how to use it.

#### Rustscan
`Rust` is taking over the world and for good reason.

Rustscan is the fastest portscanner out there right now. Always being updated, plenty of utility. The *best* portscanner out rn IMO.
It is not nativeley installed on Kali so you will have to install it.
The following command does the `Nmap` two-liner above but in one command and way faster.
```bash
rustscan -a box.htb -- -A -sCV box.htb -oN box.scan
```
The syntax works like this, `rustscan -a box.htb` scans all ports in seconds. Then, `--` tells rustscan to pass all cmds after it to nmap **ONLY** using the ports it found open. So the end of the command `-A -sCV box.htb -oN box.scan` is all for the nmap.

#### Netcat
Netcat is another tool like `nmap` where you might not use it as your *go-to* but, it is essential for you to understand how to use it. Netcat is primiarily used in situation where you on a restricted shell or where heavy scanners are being blocked. It blends in with common network activity. Not really useful on CTFs but useful in real world engagements.
