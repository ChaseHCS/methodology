# Web Enumeration
The following is my methodology for website enumeration. 


## Information Gathering
It is important to fingerprint the site to get a picture of what kind of website you are dealing with **AND** to find the potential attack vectors.

#### Fingerprinting
First it is good to look at http headers on the code on the site.

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
The information gathered here will help us in the future.

#### Directory Bruteforcing
