---
layout: post
title:  "Online Railway Reservation System 1.0 - 'id' SQL Injection (Unauthenticated)"
date:   2022-01-08 20:00:19 +0700
author: twseptian
pin: true
categories: [Infosec, CVE, Research]
tags: [cve, infosec]
---

```
- Exploit Title: Online Railway Reservation System 1.0 - 'id' SQL Injection (Unauthenticated)
- Date: 07/01/2022
- Exploit Author: twseptian
- Vendor Homepage: https://www.sourcecodester.com/php/15121/online-railway-reservation-system-phpoop-project-free-source-code.html
- Software Link: https://www.sourcecodester.com/sites/default/files/download/oretnom23/orrs.zip
- Version: v1.0
- Tested on: Kali Linux 2021.4,PHP 7.4.26
- Exploit-DB: https://www.exploit-db.com/exploits/50646
```

## Introduction

As part of my routine web-application security research, I reviewed the Online Railway Reservation System (ORRS) v1.0 from SourceCodester. My objective was straightforward: understand the data flow behind its reservation feature and assess whether user-controlled inputs are safely handled before reaching the database. This write-up outlines what I tested, what I found, and how I suggest fixing it—without fluff and with enough detail for developers and security teams to act.

## Summary of the Finding

I identified a time-based blind SQL injection affecting the reservation flow via the sid (or id) parameter processed by the page ?page=reserve. By inducing measurable response delays with crafted input, it was possible to confirm that SQL execution depends on unsafely concatenated user input.

## My Process & Reproduction Steps

While navigating Schedule → Book (Reservation Form), I noticed the page accepted a schedule identifier in the query string:

```text
/orrs/?page=reserve&sid=1
```

To probe whether sid was directly interpolated into SQL, I used a standard time-delay approach. I sent a payload designed to pause execution on the database side and watched for server response delays:

```text
/orrs/?page=reserve&sid=1') AND (SELECT 6842 FROM (SELECT(SLEEP(5)))UsWr) AND ('WBCm'='WBCm
```

Observed result: The server consistently delayed its response by ~5 seconds, a strong indicator that user input flowed into a SQL expression without proper parameterization.
Scope & restraint: All testing was performed in a local lab environment with non-destructive, observation-only techniques.
