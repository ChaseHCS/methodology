# Web Enumeration
The following is my methodology for website enumeration. It is important to fingerprint the site to get a picture of what kind of website you are dealing with **AND** to find the potential attack vectors.
#### Here is what we are looking for
- Login pages
- Deprecated technologies being used
- Cleartext credentials
- Input fields
- Hidden directories
- IDOR

There are ofc many more attack vectores then these but as a baseline this is what to look out for.

## Fingerprinting
First, we will do basic fingerprinting to get an inital understanding of the site. Specfically, it is good to look at http headers on the code on the site.

#### Curl

```bash
curl http://deeznuts.htb/AnyJuicySubDirectories
```
For headers.

```bash
curl -I http://deeznuts.htb
```
Next, lets pull up the site on FireFox or Chromium and navigate the site as an external site to see if we can find anything. Also, make sure to use `Wappalyzer` to identify technology stack on website.

From our output we can determine things like,
- Webserver being used
- Code being used
- Type of site (CMS, blog, etc...)

Lastly, there is some misc. things we should always be trying.
- Check robots.txt
- Check for .git files

It is crucial we gather as much information as possible in this step so we don't waste anytime looking for something that was right in front of us the whole time.

## Directory Bruteforcing

After initial fingerprinting we need to make sure we bruteforce almost every possible subdirectory on the site to find any hidden content.

There are several tools you can utilize to achieve this, all with their own pros and cons.

#### Feroxbuster
`Rust` is epic... This scanner utilizes rust to make for an ultra fast directory brute forcer. The only cons are that the output is weird and sometimes it misses things. Still my *go-to*. Example usage looks like this,

```bash
feroxbuster -u http://box.htb --auto-bail -d 0 -E -w /usr/SecLists/top-1-million.txt -o ferox.scan
```
The syntax of this cmd is as follows. `-u` specifies url.`--auto-bail` will automatically quit the scan if it recieves too many errors. `-d` specifies the depth (0 = infinite depth). `-E` will output file extensions. If you find file extensions, rerun the scan with `-x php txt docx` instead of `-E` to bruteforce the specific extensions.`-w` tells ferox which wordlist to use. `-o` specifies output. There is plenty of other awesome flags that you can find on their documentation on github but these are the ones I commonly use.

It is extremely important to be thorough with this step. Try different wordlists, different file extensions, and try them multiple times with different tools. You could literally fail OSCP for simply not bruting the correct wordlist.

#### Gobuster
Lorem

#### ffuf
Lorem

## Subdomain Brute Forcing

If we find a vald Domain we also want to try subdomain brute forcing. Whenever you see `port 53` open you want to try this. Can lead to lots of juicy sites. It is important to note any `emails` you might find on a website as that is telling you there is a valid domain.

#### ffuf

`ffuf` is powerful brute forcer that has tons of utility. The follow syntax will brute force subdomains:

```bash
ffuf -u http://<boxip> -H "Host: FUZZ.box.htb" -w /usr/share/SecLists/Discovery/DNS/subdomains-top1million-20000.txt -mc all -ac
```

## VHost Fuzzing

I will be back, need to do more research.
