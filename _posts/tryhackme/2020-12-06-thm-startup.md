---
title: "TryHackMe - Startup"
classes: wide
search: true
categories: 
  - TryHackMe
tags:
  - wireshark
  - pcap analysis
last_modified_at: 2020-12-06T15:58:26+08:00
toc: true
toc_label: "Contents"
---

The introduction from THM's Startup room. _"We are Spice Hut, a new startup company that just made it big! We offer a variety of spices and club sandwiches (in case you get hungry), but that is not why you are here. To be truthful, we aren't sure if our developers know what they are doing and our security concerns are rising. We ask that you perform a thorough penetration test and try to own root"._ Good luck!_

![1df50e5336be92a6c4ebc04608cf09fc.png](/assets/images/thm-startup/4ae7b8261e0c4277a4913af1f20d15af.png)

## Summary

Startup machine is an easy machine from TryHackMe, we need to learn how to enumeration ftp anonymous login, listing directory path from website, put and run a shellcode to target machine via ftp upload, read and analyze TCP/IP traffic from pcapng/pcap file using wireshark, and for privilege escalation is how to  bypass a script program to create our reverse shell

## Network Scanning

nmap scanning

```
┌──(twseptian㉿lab)-[~/lab/TryHackMe/rooms/startup]
└─$ sudo nmap -sC -sV -T4 -A 10.10.177.173 -oA startup.nmap
[sudo] password for twseptian: 
Starting Nmap 7.91 ( https://nmap.org ) at 2020-12-05 14:07 WIB
WARNING: RST from 10.10.177.173 port 21 -- is this port really open?
Nmap scan report for 10.10.177.173
Host is up (0.21s latency).
Not shown: 997 closed ports
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
| drwxrwxrwx    2 65534    65534        4096 Nov 12 04:53 ftp [NSE: writeable]
| -rw-r--r--    1 0        0          251631 Nov 12 04:02 important.jpg
|_-rw-r--r--    1 0        0             208 Nov 12 04:53 notice.txt
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to 10.9.19.3
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 2
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.10 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 b9:a6:0b:84:1d:22:01:a4:01:30:48:43:61:2b:ab:94 (RSA)
|   256 ec:13:25:8c:18:20:36:e6:ce:91:0e:16:26:eb:a2:be (ECDSA)
|_  256 a2:ff:2a:72:81:aa:a2:9f:55:a4:dc:92:23:e6:b4:3f (ED25519)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Maintenance
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.91%E=4%D=12/5%OT=21%CT=1%CU=39056%PV=Y%DS=2%DC=T%G=Y%TM=5FCB31E
OS:2%P=x86_64-pc-linux-gnu)SEQ(CI=RD%TS=A)SEQ(SP=104%GCD=1%ISR=10D%TI=Z%CI=
OS:I%II=I%TS=8)SEQ(SP=105%GCD=1%ISR=10C%TI=Z%CI=I%TS=8)OPS(O1=M505ST11NW6%O
OS:2=%O3=M505NNT11NW6%O4=M505ST11NW6%O5=M505ST11NW6%O6=M505ST11)WIN(W1=68DF
OS:%W2=0%W3=68DF%W4=68DF%W5=68DF%W6=68DF)ECN(R=Y%DF=Y%T=40%W=6903%O=M505NNS
OS:NW6%CC=Y%Q=)T1(R=Y%DF=Y%T=40%S=O%A=S+%F=AS%RD=0%Q=)T2(R=N)T3(R=N)T4(R=Y%
OS:DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T5(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%
OS:O=%RD=0%Q=)T6(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T7(R=Y%DF=Y%T=40%
OS:W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)U1(R=Y%DF=N%T=40%IPL=164%UN=0%RIPL=G%RID=G%
OS:RIPCK=G%RUCK=G%RUD=G)IE(R=Y%DFI=N%T=40%CD=S)

Network Distance: 2 hops
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE (using port 993/tcp)
HOP RTT       ADDRESS
1   271.16 ms 10.9.0.1
2   271.22 ms 10.10.177.173

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 48.90 seconds  
```

## Enumeration

ftp enumeration using anonymous login, and check directory

![2d9243410be647cffc29c6886a3cd587.png](/assets/images/thm-startup/0bb2d5f63be64c07895238632d7774a8.png)

download notice.txt file

![fb753346350129cbf7adfc0b607ccf49.png](/assets/images/thm-startup/be27b3adc48c4704a1c5287418742034.png)

notice.txt file

![e0147068dbcafb7233a620f2c82c56c6.png](/assets/images/thm-startup/575e446a3c4345be8d27cd0326c3bf38.png)

let's enumerate web's directory using ffuf

![a325591ab6aa46a32473c3007a9ceae1.png](/assets/images/thm-startup/b23123574ab846ef9e50b02cd506f859.png)

```
┌──(twseptian㉿lab)-[~/lab/TryHackMe/rooms/startup]
└─$ ffuf -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt:FUZZ -u http://10.10.177.173/FUZZ -mc 200,301 -fs 808                                     1 ⨯

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v1.1.0-git
________________________________________________

 :: Method           : GET
 :: URL              : http://10.10.177.173/FUZZ
 :: Wordlist         : FUZZ: /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,301
 :: Filter           : Response size: 808
________________________________________________

files                   [Status: 301, Size: 314, Words: 20, Lines: 10]
:: Progress: [87664/87664] :: Job [1/1] :: 159 req/sec :: Duration: [0:09:11] :: Errors: 0 ::
```

webfile directory `/files`

![5c670f2e412f6b0ef7475eebbeddef50.png](/assets/images/thm-startup/55f07b57b5354f10ad21f51553832d02.png)

upload webshell php file to upload directory

![3a39b8c95f32c83953d577d8581d6023.png](/assets/images/thm-startup/10da53bb1fcb42b9b3620a0bb6fa09ce.png)

click our php shell code,

![8c6e4fb84153c92efeb4ca77488c0005.png](/assets/images/thm-startup/c842c873da5a461aaf1d8056c4b93956.png)

and create reverse connection

![956e172348703f951362454eb8a3fe05.png](/assets/images/thm-startup/d98f29f4fd0e4a3583a7e95e1118c752.png)

list www-data's directory

![afcfc4e11a2025319dfbe80435f8d2f7.png](/assets/images/thm-startup/54b60535111a4a2b97e9d21b25b39f12.png)

recipe.txt

```
$ cat recipe.txt
Someone asked what our main ingredient to our spice soup is today. I figured I can't keep it a secret forever and told him it was love.
$ 
```

incidents directory

![47d2c3e766b7f505aa378c2dcd4f1835.png](/assets/images/thm-startup/84d77baa3007470982700892d0452953.png)

download pcapng file and open using wireshark,and right click follow> TCP Stream

![691cfd70f776f48aca9ab55211ca7389.png](/assets/images/thm-startup/f5ba55a504264d659a10f2b11cb84d05.png)

username: lennie  
password: c4ntg3t3n0ughsp1c3

![5418b83ec51f2b6d77302fc175c524f0.png](/assets/images/thm-startup/23b3e96a40734ffb8b29b2f00431626f.png)

![b953c6654ca1c30dc4b99067f122e59e.png](/assets/images/thm-startup/489e6436787345549e8ab38b14612bd4.png)

user.txt  
`THM{03ce3d619b80ccbfb3b7fc81e46c0e79}`

## Privilege Escalation

upload and [linpeas.sh](http://linpeas.sh) script, we will found a writables file is `print.sh`

![0619a730d67ce028e0d4ea33c7c9f06e.png](/assets/images/thm-startup/533b09c9707a4681b2ab54c43f011cb2.png)

add bash tcp reverse shell, and we need to waiting [print.sh](http://print.sh) will be execute from root

![b32080d2df83734e357db0285a33c40a.png](/assets/images/thm-startup/eacb6d996608493f87a719cc3de7751b.png)

![9227068b2d39b0f21a209310466e9d29.png](/assets/images/thm-startup/771eb34ed17d4ec79469a0a77c2caa78.png)

we can get root.txt

![ba3bc1359183efda50676ec0f7361862.png](/assets/images/thm-startup/784e46ba756e43a085f13be638f359aa.png)