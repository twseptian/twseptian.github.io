---
title: "TryHackMe - Brooklyn Nine Nine"
classes: wide
search: false
categories: 
  - TryHackMe
last_modified_at: 2020-08-27T15:58:26+08:00
toc: true
toc_label: "Contents"
---

![Brooklyn Nine Nine](/assets/images/thm-brooklyn99/01.png)

> This room is aimed for beginner level hackers but anyone can try to hack this box. There are two main intended ways to root the box.

## Summary
- Anonymous's FTP login allowed
- Brute Force SSH password
- Privilege Escalation via `less`

## Network Scannning

```shell
╭─twseptian@lab ~/lab/thm/rooms/brooklyn-nine-nine 
╰─$ sudo nmap -sC -sV -T4 -A -oA brookly-nine-nine.nmap 10.10.63.65
Starting Nmap 7.80 ( https://nmap.org ) at 2020-08-11 21:22 WIB
Nmap scan report for 10.10.63.65
Host is up (0.26s latency).
Not shown: 997 closed ports
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_-rw-r--r--    1 0        0             119 May 17 23:17 note_to_jake.txt
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
|      At session startup, client count was 2
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 16:7f:2f:fe:0f:ba:98:77:7d:6d:3e:b6:25:72:c6:a3 (RSA)
|   256 2e:3b:61:59:4b:c4:29:b5:e8:58:39:6f:6f:e9:9b:ee (ECDSA)
|_  256 ab:16:2e:79:20:3c:9b:0a:01:9c:8c:44:26:01:58:04 (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: Site doesn't have a title (text/html).
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.80%E=4%D=8/11%OT=21%CT=1%CU=33463%PV=Y%DS=2%DC=T%G=Y%TM=5F32A9F
OS:0%P=x86_64-pc-linux-gnu)SEQ(SP=101%GCD=1%ISR=10D%TI=Z%CI=Z%II=I%TS=A)SEQ
OS:(SP=101%GCD=1%ISR=10D%TI=Z%CI=Z%TS=A)OPS(O1=M508ST11NW6%O2=M508ST11NW6%O
OS:3=M508NNT11NW6%O4=M508ST11NW6%O5=M508ST11NW6%O6=M508ST11)WIN(W1=F4B3%W2=
OS:F4B3%W3=F4B3%W4=F4B3%W5=F4B3%W6=F4B3)ECN(R=Y%DF=Y%T=40%W=F507%O=M508NNSN
OS:W6%CC=Y%Q=)T1(R=Y%DF=Y%T=40%S=O%A=S+%F=AS%RD=0%Q=)T2(R=N)T3(R=N)T4(R=Y%D
OS:F=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T5(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O
OS:=%RD=0%Q=)T6(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T7(R=Y%DF=Y%T=40%W
OS:=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)U1(R=Y%DF=N%T=40%IPL=164%UN=0%RIPL=G%RID=G%R
OS:IPCK=G%RUCK=G%RUD=G)IE(R=Y%DFI=N%T=40%CD=S)

Network Distance: 2 hops
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE (using port 80/tcp)
HOP RTT       ADDRESS
1   262.51 ms 10.9.0.1
2   262.56 ms 10.10.63.65

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 54.16 seconds

```
## Enumeration

ftp access

![ftp_access](/assets/images/thm-brooklyn99/02.png)

note_to_jake.txt

![note_to_jake](/assets/images/thm-brooklyn99/03.png)


hydra brute-force jake password

![jake_password](/assets/images/thm-brooklyn99/04.png)

`username: jake` and `password: 987654321`

next, let's login via ssh using credential account.

![ssh_login](/assets/images/thm-brooklyn99/05.png)

user.txt

![user_txt](/assets/images/thm-brooklyn99/06.png)

## Privilege Escalation
follow the intructions about privilege escalation via [less](https://gtfobins.github.io/gtfobins/less/)

root.txt

![rootg](/assets/images/thm-brooklyn99/07.png)