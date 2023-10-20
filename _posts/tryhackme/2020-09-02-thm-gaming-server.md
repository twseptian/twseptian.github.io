---
title: "TryHackMe - Gaming Server"
classes: wide
search: false
categories: 
  - TryHackMe
tags:
  - lxd
last_modified_at: 2020-09-02T15:58:26+08:00
toc: true
toc_label: "Contents"
---

`Gaming Server` is an easy Boot2Root box for beginners. In this box/machine, we need know how about decrypting ssh private key and escalate the privilege via lxs

## Summary
- fuzzing webpath
- find ssh private key and decrypt using john2ripper or hashcat
- lxd privilege escalation

![GamingServer](/assets/images/thm-gaming-server/01.png)

## Network Scanning

find open ports using nmap

![197ffd53d5d6c4ef67ffd8ed8346c3a6.png](/assets/images/thm-gaming-server/197ffd53d5d6c4ef67ffd8ed8346c3a6.png)


```shell
┌──(twseptian㉿lab)-[~/lab/TryHackMe/rooms/gamingserverr]
└─$ sudo nmap -sC -sV -T4 -A -oA nmap/gamingserver.nmap 10.10.39.135
Starting Nmap 7.80 ( https://nmap.org ) at 2020-09-01 21:22 WIB
Nmap scan report for 10.10.39.135
Host is up (0.31s latency).
Not shown: 998 closed ports
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 34:0e:fe:06:12:67:3e:a4:eb:ab:7a:c4:81:6d:fe:a9 (RSA)
|   256 49:61:1e:f4:52:6e:7b:29:98:db:30:2d:16:ed:f4:8b (ECDSA)
|_  256 b8:60:c4:5b:b7:b2:d0:23:a0:c7:56:59:5c:63:1e:c4 (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: House of danak
Aggressive OS guesses: Linux 3.1 (95%), Linux 3.2 (95%), AXIS 210A or 211 Network Camera (Linux 2.6.17) (94%), ASUS RT-N56U WAP (Linux 3.4) (93%), Linux 3.16 (93%), Linux 2.6.32 (92%), Linux 2.6.39 - 3.2 (92%), Linux 3.1 - 3.2 (92%), Linux 3.2 - 4.9 (92%), Linux 3.7 - 3.10 (92%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 2 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE (using port 8080/tcp)
HOP RTT       ADDRESS
1   222.27 ms 10.9.0.1
2   282.27 ms 10.10.39.135

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 90.62 seconds
```
 
## Enumeration

web source page

![f82c71e90785203d9aad073b3f21e4a8.png](/assets/images/thm-gaming-server/f82c71e90785203d9aad073b3f21e4a8.png)

we found a user as `john`

gobuster scanning result

![1a751571ba08b92bc6bba575f6d6f4c5.png](/assets/images/thm-gaming-server/1a751571ba08b92bc6bba575f6d6f4c5.png)


![5674fb62dea59d0e181bdfb758d3aea6.png](/assets/images/thm-gaming-server/5674fb62dea59d0e181bdfb758d3aea6.png)



uploads directory

![d2fea3b756e4e078a2fc2cae6da73b58.png](/assets/images/thm-gaming-server/d2fea3b756e4e078a2fc2cae6da73b58.png)

secret directory

![7259d366d6b4bb9b8960ebd0721a5c40.png](/assets/images/thm-gaming-server/7259d366d6b4bb9b8960ebd0721a5c40.png)

ssh key

```yaml
-----BEGIN RSA PRIVATE KEY-----
Proc-Type: 4,ENCRYPTED
DEK-Info: AES-128-CBC,82823EE792E75948EE2DE731AF1A0547

T7+F+3ilm5FcFZx24mnrugMY455vI461ziMb4NYk9YJV5uwcrx4QflP2Q2Vk8phx
H4P+PLb79nCc0SrBOPBlB0V3pjLJbf2hKbZazFLtq4FjZq66aLLIr2dRw74MzHSM
FznFI7jsxYFwPUqZtkz5sTcX1afch+IU5/Id4zTTsCO8qqs6qv5QkMXVGs77F2kS
Lafx0mJdcuu/5aR3NjNVtluKZyiXInskXiC01+Ynhkqjl4Iy7fEzn2qZnKKPVPv8
9zlECjERSysbUKYccnFknB1DwuJExD/erGRiLBYOGuMatc+EoagKkGpSZm4FtcIO
IrwxeyChI32vJs9W93PUqHMgCJGXEpY7/INMUQahDf3wnlVhBC10UWH9piIOupNN
SkjSbrIxOgWJhIcpE9BLVUE4ndAMi3t05MY1U0ko7/vvhzndeZcWhVJ3SdcIAx4g
/5D/YqcLtt/tKbLyuyggk23NzuspnbUwZWoo5fvg+jEgRud90s4dDWMEURGdB2Wt
w7uYJFhjijw8tw8WwaPHHQeYtHgrtwhmC/gLj1gxAq532QAgmXGoazXd3IeFRtGB
6+HLDl8VRDz1/4iZhafDC2gihKeWOjmLh83QqKwa4s1XIB6BKPZS/OgyM4RMnN3u
Zmv1rDPL+0yzt6A5BHENXfkNfFWRWQxvKtiGlSLmywPP5OHnv0mzb16QG0Es1FPl
xhVyHt/WKlaVZfTdrJneTn8Uu3vZ82MFf+evbdMPZMx9Xc3Ix7/hFeIxCdoMN4i6
8BoZFQBcoJaOufnLkTC0hHxN7T/t/QvcaIsWSFWdgwwnYFaJncHeEj7d1hnmsAii
b79Dfy384/lnjZMtX1NXIEghzQj5ga8TFnHe8umDNx5Cq5GpYN1BUtfWFYqtkGcn
vzLSJM07RAgqA+SPAY8lCnXe8gN+Nv/9+/+/uiefeFtOmrpDU2kRfr9JhZYx9TkL
wTqOP0XWjqufWNEIXXIpwXFctpZaEQcC40LpbBGTDiVWTQyx8AuI6YOfIt+k64fG
rtfjWPVv3yGOJmiqQOa8/pDGgtNPgnJmFFrBy2d37KzSoNpTlXmeT/drkeTaP6YW
RTz8Ieg+fmVtsgQelZQ44mhy0vE48o92Kxj3uAB6jZp8jxgACpcNBt3isg7H/dq6
oYiTtCJrL3IctTrEuBW8gE37UbSRqTuj9Foy+ynGmNPx5HQeC5aO/GoeSH0FelTk
cQKiDDxHq7mLMJZJO0oqdJfs6Jt/JO4gzdBh3Jt0gBoKnXMVY7P5u8da/4sV+kJE
99x7Dh8YXnj1As2gY+MMQHVuvCpnwRR7XLmK8Fj3TZU+WHK5P6W5fLK7u3MVt1eq
Ezf26lghbnEUn17KKu+VQ6EdIPL150HSks5V+2fC8JTQ1fl3rI9vowPPuC8aNj+Q
Qu5m65A5Urmr8Y01/Wjqn2wC7upxzt6hNBIMbcNrndZkg80feKZ8RD7wE7Exll2h
v3SBMMCT5ZrBFq54ia0ohThQ8hklPqYhdSebkQtU5HPYh+EL/vU1L9PfGv0zipst
gbLFOSPp+GmklnRpihaXaGYXsoKfXvAxGCVIhbaWLAp5AybIiXHyBWsbhbSRMK+P
-----END RSA PRIVATE KEY-----
```

convert using ssh2john

![992ebdef060f4218c5a3403d8234a2f6.png](/assets/images/thm-gaming-server/992ebdef060f4218c5a3403d8234a2f6.png)

we found the password

![b4e9109dd3446bb1106fe750d47eefbb.png](/assets/images/thm-gaming-server/b4e9109dd3446bb1106fe750d47eefbb.png)

password: `letmein`

let's try to login using `john` as username and `letmein` as password

![4fc8fb8f27efc1d8dee40551fa699bb0.png](/assets/images/thm-gaming-server/4fc8fb8f27efc1d8dee40551fa699bb0.png)

## Privilege Escalation

find suid

![58568be32e00d4f3403f2966d7d8a008.png](/assets/images/thm-gaming-server/58568be32e00d4f3403f2966d7d8a008.png)

we found lxd,and we can follow the intructions from [Hackingarticle.in](https://www.hackingarticles.in/lxd-privilege-escalation/) about lxd privilege escalation.

download lxd alpine builder [https://github.com/saghul/lxd-alpine-builder.git](https://github.com/saghul/lxd-alpine-builder.git) and ./build as root,and then upload to target machine

![0a2199cf48c580baedffb295c9ba6a39.png](/assets/images/thm-gaming-server/0a2199cf48c580baedffb295c9ba6a39.png)


lxc import image

![c9c231386d5a6f6893cfed71af45d03a.png](/assets/images/thm-gaming-server/c9c231386d5a6f6893cfed71af45d03a.png)

![8816e7ad4a02abbcd7f849c1befbe294.png](/assets/images/thm-gaming-server/8816e7ad4a02abbcd7f849c1befbe294.png)

find root.txt

![173c35d263f239a85e2a349b8fd0a297.png](/assets/images/thm-gaming-server/173c35d263f239a85e2a349b8fd0a297.png)

## Reference
- Lxd privilege Escalation - https://www.hackingarticles.in/lxd-privilege-escalation/
