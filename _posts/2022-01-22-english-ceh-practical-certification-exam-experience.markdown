---
layout: post
title:  "[English] CEH Practical Certification Exam Experience"
date:   2022-01-22 15:09:19 +0700
author: twseptian
pin: true
categories: [Certification]
tags: [certification, infosec]
---

## Introduction
This is my personal experience taking the **CEH Practical** exam—shared for anyone who is preparing for or considering the certification.

I first learned about the **CEH Practical Scholarship** in 2020, which reduced the exam fee to around **$99** (from ~**$550**). I followed the instructions, received an Aspen dashboard code, and secured a scholarship slot toward the end of 2020.

I didn’t take the exam immediately because I was focused on **OSCP**. I activated the Aspen code around **June 2021** and finally sat the CEH Practical near its expiry window.

---

## CEH Practical Overview
The CEH Practical is a **proctored** exam (e.g., via Aspen) with **real-world scenarios** instead of multiple-choice questions.

**Key Specs:**
- **Exam Title:** Certified Ethical Hacker (Practical)  
- **Number of Practical Challenges:** 20  
- **Duration:** 6 hours  
- **Availability:** Aspen – iLabs  
- **Test Format:** iLabs Cyber Range  
- **Passing Score:** 70%  
- **Open Book:** Yes  

Unlike the **CEH (ANSI)** multiple-choice exam (4 hours), the Practical runs in **Aspen iLabs**, providing two machines—**Parrot OS** and **Windows 10**—for you to investigate and submit answers during the session.

**Topics commonly covered:**
- **Network scanning** to identify live hosts, ports, and services  
- **OS detection** and **enumeration** of active hosts  
- **System hacking**, **steganography**, **cryptography**  
- **Packet sniffing** and **deep packet inspection** of `.pcap/.pcapng` files  
- **Web application security** (e.g., SQL Injection)  
- **Hash and password cracking**

**Tools I used (examples):**
- **Scanning & Reconnaissance:** `nmap`  
- **Sniffing:** Wireshark, `tcpdump`  
- **Web App Attacks:** Burp Suite, OWASP ZAP, `sqlmap`  
- **Brute Force & Cracking:** `hydra`, John the Ripper  
- **Cryptography:** HashCalc, `md5sum`, VeraCrypt, CryptoForge, BCTextEncoder, `SONAR.exe`, CrypTool  
- **Steganography:** OpenStego  

**Supporting tools (Linux RDP/remote):** Remmina, `xfreerdp`

---

## Exam Preparation
Here are the practical things that helped me:

- **Stable internet is crucial.** In my case, the Parrot OS and Windows 10 VMs in Aspen iLabs felt **slow**, so patience mattered.  
- **Read and follow the proctor’s instructions** and rules carefully.  
- **Proctoring setup.** I used **GoToMeeting** installed on my laptop. The proctor monitored via **webcam**, **screen share**, and **microphone**.  
- **Use a compatible browser** (Firefox/Chrome) to access iLabs.  
- **Open book is allowed.** Prepare reference sites or your favorite **cheatsheets**. Googling for commands and docs is permitted within the rules.  
- **No discussions with others.** Keep your **phone away** and make sure you’re **alone** in the room.  
- **Break time:** You get **15 minutes** for a quick rest or restroom break.

During the exam, respect all rules, stay patient with the lab performance, and **focus** on each task.

After finishing, **talk to the proctor** before clicking **“Submit.”** Your result appears on your **Aspen** account shortly after, along with the **exam transcript**, **PDF certificate**, **digital badge**, and the **CEH Practical logo**.

---

## Notes & Disclaimer
- This post reflects **my personal experience** and timeline.  
- Tools and topics listed are **examples**, not an official syllabus.  
- Always **comply with laws and exam policies**.  
- Content is shared for **educational purposes only**.  

<a href="https://aspen.eccouncil.org/VerifyBadge?type=certification&a=RjLpHlv94HwrHByLKZw02VkNYlg0sDNqTcb0UnyQ760=">Verification</a>