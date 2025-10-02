---
layout: post
title:  "[English] OSCP Certification Exam Experience"
date:   2021-08-27 20:00:19 +0700
author: twseptian
pin: true
categories: [Certification]
tags: [certification, infosec, offsec]
---

> Update: Since 2022, Offensive Security (OffSec) updated the PEN-200 materials by adding Active Directory content and removing Buffer Overflow (BoF) from the exam, so some suggestions in this post are less relevant. You can read OffSec’s announcement here: <a href="https://www.offsec.com/offsec/oscp-exam-structure/">https://www.offsec.com/offsec/oscp-exam-structure/
</a>
{: .prompt-info }

## Intruduction
Here is my story about passing the OSCP exam, which I hope helps anyone taking or planning to take the certification. OSCP is a widely recognized professional certification in penetration testing/red teaming and is often requested by security recruiters. Beyond hiring value, OSCP sharpens a pentester’s approach to analyzing each engagement—what to tackle first, how to enumerate ports and services, and how to assess vulnerabilities across services, web applications, and buffer overflow scenarios.

Anyone pursuing this certification should already be comfortable with Linux fundamentals—especially Kali Linux—because the OSCP course is <a href="https://www.offensive-security.com/pwk-oscp/">Penetration Testing with Kali Linux (PWK)</a>. A basic understanding of computer networks, ports, the services running on those ports, and some familiarity with scripting/programming is also important.

## Learning Resources
Below are the study resources I used to prepare for the OSCP exam:

- <a href="https://docs.google.com/spreadsheets/d/1dwSMIAPIam0PuRBkCiDI88pU3yzrqqHkDtBngUHNCw8/edit?fbclid=IwAR0k7G5SNt_HCtrPo4-BWjnAp56WEPsA-NaX92VT0M0JqlRAsdoQAYeqcS4#gid=1839402159">TJNull - NetSecFocus Trophy Room</a>, a list of machines similar to OSCP targets—great for practice.

- <a href="https://app.hackthebox.eu/home">HackTheBox</a> – a subscription (min. 1 month) is recommended to access retired machines.

- <a href="https://vulnhub.com/">VulnHub</a> – you only need a strong internet connection and ample storage for VMs. You can download machines from the TJNull list.

- <a href="https://tryhackme.com/">TryHackMe</a> – study rooms like Windows and Linux Privilege Escalation by Tib3rius/TCM, Buffer Overflow Preparation by Tib3rius, and try both free and premium CTF boxes.

- <a href="https://portal.offensive-security.com/">Offensive Security Proving Grounds</a> – OffSec’s own lab platform. Proving Grounds Play (free, includes some VulnHub boxes) and Proving Grounds Practice (newer, OffSec-authored machines).

- <a href="https://www.youtube.com/channel/UCa6eh7gCkpPo5XXUDfygQQA">IPPSec YouTube videos</a> – walkthroughs of HTB machines that showcase enumeration methodology, avoiding rabbit holes, and privilege escalation.

- <a href="https://rana-khalil.gitbook.io/hack-the-box-oscp-preparation/">HackTheBox OSCP Preparation (Rana Khalil)</a> – clear write-ups of retired HTB boxes with easy-to-follow enumeration and escalation methods.

- <a href="https://www.udemy.com/">Udemy</a> – helpful courses from Tib3rius/TCM on Windows and Linux privilege escalation. I used author discounts announced on Twitter/Discord—follow or join their servers to catch them.

- <a href="https://book.hacktricks.xyz/">Hack Tricks by carlos polop</a> – an essential pentester handbook; it helped me a lot during OSCP, especially for per-port/service methodology.

- <a href="https://github.com/swisskyrepo/PayloadsAllTheThings">Payloads All The Things (GitHub repo)</a> – notes on enumeration, web attacks, and privilege escalation.

- <a href="https://gtfobins.github.io/">GTFOBins</a> – documentation for Linux privilege escalation techniques.

You can also discuss and learn in the <a href="https://discord.com/invite/offsec">Offensive Security Discord</a> and <a href="https://discord.com/invite/infosecprep">NetSecFocus Discord</a>. Many of the lists above are commonly shared by PWK students who passed OSCP, but in my case, participating in boot2root/jeopardy CTFs also helped a lot.

## Exam Preparation
For the exam, make sure you are solid on enumeration techniques and privilege escalation (review until you’re confident). Also aim to reduce reliance on write-ups when working through retired HTB and OffSec PG Play boxes.

For note-taking during the exam, I recommend <a href="https://obsidian.md/">Obsidian</a> because it’s lightweight and easy to use. As an alternative, <a href="https://joplinapp.org/">Joplin</a> is also good. Before the exam, read the <a href="https://help.offensive-security.com/hc/en-us/articles/360040165632-OSCP-Exam-Guide">OffSec OSCP Exam Guide</a> thoroughly, prepare yourself mentally, and schedule the exam. I chose a weekend (Saturday) because I had cleared my workload.

The day before the exam, I rested completely—watched movies and some YouTube for mental prep. One hour before the exam (06:00 WIB), I woke up, prepared snacks, water, and coffee. Fifteen minutes before the start, I logged into the proctoring page and chatted with the proctor to verify my ID (ID Card/Passport—have scanned copies ready). You’ll be asked to show the room, your desk, and under the desk using your webcam. VPN and control panel details, along with exam machine IPs, will be emailed to you. You’ll then test your OS and internet connection with the provided VPN.

Communicate with the proctor and ask permission to pause the webcam if you need a restroom or rest break.

Once cleared, you can begin. Tackle the machines you find easiest first—often BoF back then—then lower-point boxes, and finally the hardest. When you face a target, start with network scanning, document all open ports and services, enumerate each thoroughly, and follow leads until you find a vulnerability. Avoid jumping to another box prematurely unless you are truly stuck.

Most vulnerabilities have documentation and public exploits on exploit-db or elsewhere. For privilege escalation, use the resources listed above. During the exam, you’re allowed to reference command lines from <a href="https://book.hacktricks.xyz/">Hack Tricks by carlos polop</a>, <a href="https://github.com/swisskyrepo/PayloadsAllTheThings">Payloads All The Things (GitHub repo)</a>, and your own notes, as well as IPPSec videos or other references.

While taking the exam, screenshot every important step in your Kali terminal and keep everything organized in your note-taking app with brief explanations. This is crucial when writing the report later.

## Reporting
You can use the template provided in the <a href="https://help.offensive-security.com/hc/en-us/articles/360040165632-OSCP-Exam-Guide">OffSec OSCP Exam Guide</a> or the <a href="https://github.com/noraj/OSCP-Exam-Report-Template-Markdown">Offensive Security Exam Report Template in Markdown</a> (note: I used the Markdown template). Start writing when you’re well-rested, then upload to the OffSec submission link. Make sure grammar is correct, screenshots and source code are clear, and your steps are well-documented.

After submission, wait for OffSec to announce the result on business days. In my case, the exam ended at 23:00 WIB after starting at 06:00 WIB (almost 18 hours). I rested and began writing the report at 08:00 WIB the next morning. Try not to be overly anxious—stay calm, take breaks, do something that refreshes you, and pray.

## Result Announcement
Results are sent by email, typically within 1–2 business days. About 1.5 days after submitting my report—on Wednesday, August 24, 2021—I received the email confirming that I passed the OSCP.

![Desktop View](/assets/img/posts/oscp.png)
_OSCP Pass Announcement_

According to the latest information from Offensive Security, the badge can be accessed via Accredible.

<iframe
  src="https://credentials.offsec.com/embed/6dad89ac-d662-4067-af42-1c7448dba77c"
  width="800"
  height="600"
  frameborder="0"
  allowfullscreen>
</iframe>