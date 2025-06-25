# SSRF

Server-Side Request Forgery is a very common vulnerability where the user can execute commands as the server (outside of the intended purpose) due to impromper input validation and filtering.

## Identifying SSRF

SSRF can commonly be found in input fields. Try inputting these payloads to see if the given field is vulnerable:

- `http://127.0.0.1/etc/passwd`
- `http://<yourip>/test`

If you find these fields are vulnerable there a couple things you should try to get something like a file read or upload. Most importantly, you need to try and determine the exact ``command`` or ``action`` the server is running on your behalf. This will help you determine the ways in which it can be exploited.

## Exploiting SSRF

## Fuzzing local ports

The following command will fuzz all ports:
```bash
ffuf -u http://<box.htb>/<Wherever the HTTP request posts too> -request ssrf.request -w <( seq 0 65535) -ac
```
