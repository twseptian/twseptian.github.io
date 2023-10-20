---
title: "HTB OSINT Challenge - Easy Phish"
classes: wide
header:
  teaser: /assets/images/htb-frame.png
search: false
categories:
  - HackTheBox Challenge
toc: true
toc_label: "Contents"
last_modified_at: 2020-04-21T16:45:40+08:00
---

## Easy Phish - OSINT challenge

On this challenge, we can look at the description or clue:

>Customers of secure-startup.com have been receiving some very convincing phishing emails, can you figure out why?

I tried to access the URL on a browser, but it wasn't showed anything, and some of the OSINT enumeration tools: `dig,` `host,` `nslookup` very useful to see all information about DNS record of `secure-startup.com.`, SPF record, DKIM, and DMARC.[see this article 'Testing SPF, DKIM, and DMARC'](https://linuxincluded.com/testing-spf-dkim-and-dmarc/)

```
$ dig TXT secure-startup.com _dmarc.secure-startup.com
```
Output
```
; <<>> DiG 9.10.6 <<>> TXT secure-startup.com _dmarc.secure-startup.com
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 17665
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 1280
;; QUESTION SECTION:
;secure-startup.com.        IN  TXT

;; ANSWER SECTION:
secure-startup.com. 411 IN  TXT "v=spf1 a mx ?all - HTB{RIP_SPF_Always_2nd"

;; Query time: 32 msec
;; SERVER: 192.168.43.1#53(192.168.43.1)
;; WHEN: Tue Apr 21 21:58:39 WIB 2020
;; MSG SIZE  rcvd: 101

;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 16599
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 1280
;; QUESTION SECTION:
;_dmarc.secure-startup.com. IN  TXT

;; ANSWER SECTION:
_dmarc.secure-startup.com. 1778 IN  TXT "v=DMARC1;p=none;_F1ddl3_2_DMARC}"

;; Query time: 22 msec
;; SERVER: 192.168.43.1#53(192.168.43.1)
;; WHEN: Tue Apr 21 21:58:39 WIB 2020
;; MSG SIZE  rcvd: 99
```
We got some of detail informations about spf record are showed on the line `secure-startup.com.   411 IN  TXT "v=spf1 a mx ?all - HTB{RIP_SPF_Always_2nd"` and dmarc record `_dmarc.secure-startup.com. 1778 IN  TXT "v=DMARC1;p=none;_F1ddl3_2_DMARC}"`. Finally, we can look and combine our 2 flags `HTB{RIP_SPF_Always_2nd` and `_F1ddl3_2_DMARC}`.

here below the flag
```
HTB{RIP_SPF_Always_2nd_F1ddl3_2_DMARC}
```
references:
- https://linuxincluded.com/testing-spf-dkim-and-dmarc/
- http://knowledge.ondmarc.redsift.com/en/articles/1519838-querying-spf-dkim-and-dmarc-records
