# Web Enumeration
The following is my methodology for website enumeration. 


## Information Gathering
It is important to fingerprint the site to get a picture of what kind of website you are dealing with.

##### Curl
First it is good to look at http headers on the code on the site.

```bash
curl http://deeznuts.htb/AnyJuicySubDirectories
```
For headers.

```bash
curl -I http://deeznuts.htb
```
