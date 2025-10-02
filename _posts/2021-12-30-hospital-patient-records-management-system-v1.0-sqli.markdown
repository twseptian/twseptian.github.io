---
layout: post
title:  "Hospital's Patient Records Management System v1.0 - 'id' SQL Injection (Authenticated)"
date:   2021-12-30 20:00:19 +0700
author: twseptian
pin: true
categories: [Infosec, CVE, Research]
tags: [cve, infosec]
---

```
- Exploit Title: Hospital's Patient Records Management System v1.0 - 'id' SQL Injection (Authenticated)
- Date: 2021-12-30
- Exploit Author: twseptian
- Vendor Homepage: https://www.sourcecodester.com/php/15116/hospitals-patient-records-management-system-php-free-source-code.html
- Software Link: https://www.sourcecodester.com/sites/default/files/download/oretnom23/hprms_0.zip
- Version: v1.0
- Tested on: Kali Linux 2021.4
- Exploit-DB link: https://www.exploit-db.com/exploits/50630
```

## Introduction

During a focused review of HPRMS v1.0, I set out to evaluate the integrity of its record-view workflow inside the admin area—specifically how user-controlled identifiers are validated before being passed to the database. CRUD pages that accept object IDs via query strings are frequent culprits for SQL injection when input handling is inconsistent. My goal was to trace the patient-view code path, determine whether the id parameter was parameterized, and validate any issues in a safe, non-destructive way.

---

## Summary of the Finding

HPRMS v1.0 is vulnerable to a time-based blind SQL injection on the patient detail page. An authenticated user can supply crafted input in the id query parameter to trigger database delays, demonstrating that the application concatenates user input into a SQL expression without using parameterized queries. In practice, this allows an attacker to infer or extract data over time by encoding questions into time delays, even when no error messages or data are directly returned.

---


## Reproduction steps (tested on local instance)
> Performed only on a local test instance for verification and learning.

- From the admin dashboard, I navigated to Patient List → Action → View Records, which loads a patient detail page using a query-string identifier:

```text
http://localhost/hprms/admin/?page=patients/view_patient&id=1
```

- This confirmed that the object ID (id) is taken from the URL and likely used to select a specific patient record. 
twseptian's website

- To test whether the id is safely handled, I issued a standard time-based blind payload that asks the database to sleep before responding. Example (single-quote close + boolean + delay):

```text
page=patients/view_patient&id=1' AND (SELECT 2664 FROM (SELECT(SLEEP(5)))ixec) AND 'XcAY'='XcAY
```

The server’s response consistently delayed by roughly five seconds, confirming that user input flowed into a SQL context without parameterization. The behavior required an authenticated session to reach the patient view.
