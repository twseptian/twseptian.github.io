---
layout: post
title:  "Online Veterinary Appointment System v1.0 — Multiple SQL Injection via id in Appointment Details)"
date:   2022-01-05 20:00:19 +0700
author: twseptian
pin: true
categories: [Infosec, CVE, Research]
tags: [cve, infosec]
---

```
- Exploit Title: Online Veterinary Appointment System 1.0 - 'Multiple' SQL Injection
- Date: 05/01/20222
- Exploit Author: twseptian
- Vendor Homepage: https://www.sourcecodester.com/php/15119/online-veterinary-appointment-system-using-phpoop-free-source-code.html
- Software Link: https://www.sourcecodester.com/sites/default/files/download/oretnom23/ovas.zip
- Version: v1.0
- Tested on: Kali Linux 2021.4
- Exploit-DB: https://www.exploit-db.com/exploits/50644
```

## Introduction

I examined OVAS v1.0 to evaluate how its admin dashboard handled user-controlled identifiers that flow into database queries. Appointment management looked like a likely entry point: it exposes a detail view driven by a numeric id that is passed through the query string. My aim was to confirm whether the application enforced proper server-side input handling and to determine the impact if user input reached SQL without parameterization. The review was done in a controlled local environment using non-destructive techniques, focusing on deterministic timing behavior to avoid altering data

## Summary of the Finding

OVAS v1.0 is vulnerable to multiple SQL injection conditions in the appointment details path where the id parameter is used to fetch a record. By injecting a time-delay expression into id, the server’s response becomes predictably slower, indicating that untrusted input is being concatenated into a SQL statement. In practice, this opens a path to enumerate database contents or stage more invasive actions, depending on permissions. The issue stems from missing parameterization and insufficient server-side validation on the id field within the appointment view workflow.

## My Process & Reproduction Steps

- From an authenticated admin session, I navigated to Dashboard → Appointment Requests:

```text
http://localhost/ovas/admin/?page=appointments
```

Selecting Action → View loads the appointment detail view and passes a record identifier via query string. 

- The detail page requests data using an id parameter (e.g., ?`page=appointments/view_details&id=1)`. This parameter is the primary candidate for injection testing because it directly influences which record is returned. 

- I replaced the numeric identifier with a delay construct to test whether input reached SQL unescaped. The following request consistently induced a ~5-second delay:

```text
/ovas/admin/?page=appointments/view_details&id=1' AND (SELECT 2197 FROM (SELECT(SLEEP(5)))DZwi) AND 'mQQq'='mQQq
```

- The repeatable delay confirmed a time-based blind SQL injection in the id parameter. 

- Given the confirmed injection point, an attacker could pivot to enumeration or data extraction using boolean/time-based strategies or automate with tools like sqlmap. My validation stopped at timing confirmation to avoid modifying data. The root cause remains lack of parameterized queries and missing server-side validation for the id input.
