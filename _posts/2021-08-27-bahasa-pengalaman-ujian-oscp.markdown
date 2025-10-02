---
layout: post
title:  "[Bahasa] Pengalaman Ujian Sertifikasi OSCP"
date:   2021-08-27 20:00:19 +0700
author: twseptian
pin: true
categories: [Certification]
tags: [certification, infosec, offsec]
---

> Update: Semenjak tahun 2022, Offensive Security (OffSec) melakukan update terhadap materi PEN-200 dengan menambahkan materi Active Directory dan menghapus Buffer Overflow (BoF) pada exam, sehingga beberapa saran dari posting ini menjadi kurang relevan. Terkait berita tersebut bisa dibaca pada postingan OffSec berikut: <a href="https://www.offsec.com/offsec/oscp-exam-structure/">https://www.offsec.com/offsec/oscp-exam-structure/</a>
{: .prompt-info }

## Pendahuluan
Berikut cerita pengalaman saya untuk lulus ujian OSCP yang ingin saya bagikan untuk orang yang sedang atau ingin mengambil sertifikasi OSCP. OSCP merupakan salah satu sertifikasi profesional di bidang penetration testing atau red team yang telah banyak diakui oleh para rekruter di perusahaan security, beberapa perusahaan mengingkan para pelamar memiliki sertifikasi OSCP agar dapat bekerja di perusahaan security tersebut. Tidak hanya itu OSCP dapat meningkatkan kualitas para pentester dalam menganalisis setiap pekerjaan yang mereka hadapi, seperti hal apa yang pertama dihadapi?, proses melakukan enumeration pada setiap port, services sampai ke setiap vulnerability yang ada pada service, web application, buffer overflow.

Setiap orang ingin mengambil sertifikasi ini harus sudah menguasai teknik dasar dari sistem operasi linux, terutama kali linux, karena sesuai dengan nama kursus dari OSCP yaitu <a href="https://www.offensive-security.com/pwk-oscp/">Penetration With Kali linux (PWK)</a>, kemudian dasar dari jaringan komputer,ports, service pada port tersebut,serta harus sedikit familiar dengan dengan scripting bahasa pemrograman.

## Sumber Belajar
Berikut beberapa sumber belajar yang telah saya pelajari untuk menempuh ujian sertifikasi OSCP, antara lain

- <a href="https://docs.google.com/spreadsheets/d/1dwSMIAPIam0PuRBkCiDI88pU3yzrqqHkDtBngUHNCw8/edit?fbclid=IwAR0k7G5SNt_HCtrPo4-BWjnAp56WEPsA-NaX92VT0M0JqlRAsdoQAYeqcS4#gid=1839402159">TJNull - NetSecFocus Trophy Room</a>, merupakan list mesin - mesin seperti mesin OSCP, dan dapat dijadikan untuk latihan

- <a href="https://app.hackthebox.eu/home">HackTheBox</a> - diwajibkan subcribe (mins 1 month) agar dapat mendapatkan akses mesin yang sudah retired.

- <a href="https://vulnhub.com/">VulnHub</a> - anda hanya perlu memiliki koneksi internet yang kuat, serta media penyimpanan yang besar untuk menyimpan VM dari VulHub, Anda cukup mendownload apa yang sudah ada di TJNull list tersebut.

- <a href="https://tryhackme.com/">TryHackMe</a> - Pelajari beberapa room seperti Windows dan Linux Privilege Escalation dari Tib3rius,TCM, Buffer Overflow Preparation dari Tib3rius, dan bermain juga beberapa mesin CTF yang free, maupun premium.

- <a href="https://portal.offensive-security.com/">Offensive Security Proving Grounds</a> - OffSec juga mengeluarkan sistem permainan, dan latihan dengan nama Offensive Security Proving Grounds, tersedia untuk yang free yaitu Proving Grounds Play dengan beberapa mesin yang diambil dari mesin VulnHub, kemudian Proving Ground Practice untuk mesin - mesin terbaru buatan khusus OffSec.

- <a href="https://www.youtube.com/channel/UCa6eh7gCkpPo5XXUDfygQQA">IPPSec YouTube videos</a> - berisikan video writeup pada mesin - mesin HTB, anda dapat mempelajari dari IPPSec yaitu metodologi dalam melakukan proses enumeration,how to avoid a rabbit hole, sampai ke proses privilege escalation.

- <a href="https://rana-khalil.gitbook.io/hack-the-box-oscp-preparation/">HackTheBox OSCP Preparation (Rana Khalil)</a> - merupakan blog berisikan writeup dari mesin HTB yang sudah retired, anda dapat mempelajari metode dalam melakukan enumeration, sampai privilege escalation dengan penjelasan yang mudah dimengerti.

- <a href="https://www.udemy.com/">Udemy</a> - pada udemy ada beberapa video dari tib3rius,TCM yang memberikan pengetahuan mengenai bagaimana privilege escalation di sistem operasi Windows dan Linux. Untuk ini saya mendapatkan diskon yang dapatkan dari authors masin - masing, jadi follow akun twitter atau gabung di discord channel author - author tersebut.

- <a href="https://book.hacktricks.xyz/">Hack Tricks by carlos polop</a> - berisakan catatan penting untuk seorang pentester, dalam hal ini sangat membantu saya ketika menyelesaikan OSCP, terutama methodology setiap port maupun service.

- <a href="https://github.com/swisskyrepo/PayloadsAllTheThings">Payloads All The Things (GitHub repo)</a> - berisikan catatan penting mengenai teknik untuk enumeration, web attack, sampai ke privilege escalation.

- <a href="https://gtfobins.github.io/">GTFOBins</a> - berisikan informasi mengenai dokumentasi untuk linux privilege escalation.

Selain itu dapat berdiskusi di group <a href="https://discord.com/invite/offsec">Discord Offensive Security</a>, dan <a href="https://discord.com/invite/infosecprep">Discord NetSecFocus</a>. Beberapa list yang disebutkan adalah umum yang sering di-sharing oleh PWK student yang lulus OSCP, namun bagi saya sendiri, mengikuti beberapa event CTF boot2root/jeopardy menjadi nilai tambah.

## Exam Preparation
Untuk ujian, pastikan anda sudah menguasai beberapa teknik enumeration, sampai privilege escalation (ulangi lagi jika belum terlalu menguasai), kemudian anda juga sudah tidak lagi ketergantungan dengan write-up ketika anda bermain mesin retired di HTB, dan OffSec PG Play.

Untuk aplikasi catatan selama exam saya sarankan menggunakan <a href="https://obsidian.md/">obsidian</a>, dikarenakan ringan dan mudah digunakan, jika ada opsi lain saya anjurkan menggunakan <a href="https://joplinapp.org/">joplin</a>. Pada saat mau ujian pastikan peraturan - peraturan dari <a href="https://help.offensive-security.com/hc/en-us/articles/360040165632-OSCP-Exam-Guide">OffSec OSCP Exam Guide</a> telah anda baca dan pahami, kemudian siapkan mental anda dan ambil jadwal exam, saya ambil pada waktu weekend (Sabtu), dikarenakan semua pekerjaan saya telah selesai.

Pada hari sebelum saya exam, saya mengambil istirahat total, menonton film, video di youtube untuk persiapan mental pada saat exam hari berikutnya. Satu jam sebelum exam (jam 06:00 WIB) saya sudah bangun mempersiapkan diri, snack, air minum dan kopi, kemudian 15 menit sebelum exam saya sudah login ke halaman proctors, kemudian chating dengan proctors untuk melakukan pengecekan kartu identitas (ID Card/KTP, Passport, sebagai catatan pastikan anda menyiapkan hasil scan softcopy ID Card, Passport anda), dan anda akan disuruh mengarahkan kamera anda untuk mengecek ruangan, meja kerja anda, dan bawah meja anda melalui kamera yang terhubung dengan laptop/komputer anda, kemudian akan dikirimkan akses vpn dan control panel,beserta alamat ip mesin exam ke email anda, kemudian akan disuruh untuk mengecek sistem operasi beserta koneksi internet dengan koneksi vpn yang telah diberikan.

Pastikan anda berkomunikasi dengan proctor dan minta izin untuk meng-pause webcam ketika ingin ke kamar kecil, istirahat.

Setelah itu anda akan dipersilahkan untuk memulai exam. Untuk exam pastikan bisa menyelesaikan mesin yang bagi anda mudah, seperti BoF, kemudian mesin dengan point kecil sampai ke point yang terbesar. Ketika anda sudah dihadapkan dengan mesin exam, pastikan anda lakukan network scanning terlebih dahulu, kemudian dokumentasikan setiap port dan service yang terlihat, kemudian anda lakukan enumeration pada setiap masing - masing port dan services tersebut, sampai anda menemukan indikasi vulnerability, dan jangan anda mencoba loncat ke mesin lain, pastikan anda selesai terlebih dahulu mesin yang sedang anda lakukan enumeration, kecuali jika anda sudah stuck.

Setiap vulnerability akan tersedia dokumentasi beserta exploitnya di exploit-db ataupun di internet/public dan untuk privilege escalation anda bisa mempelajari teknik - teknik pada sumber - sumber yang telah saya jabarkan di atas, dan anda ketika ujian anda diperbolehkan melihat command lines pada <a href="https://book.hacktricks.xyz/">Hack Tricks by carlos polop</a>, <a href="https://github.com/swisskyrepo/PayloadsAllTheThings">Payloads All The Things (GitHub repo)</a> ataupun sumber - sumber lain anda, maupun writeup dari IPPSec YouTube videos ataupun website lain yang menjadi referensi anda.

Saat dalam ujian pastikan setiap proses pada kali linux terminal anda di-screen-shot-kan, kemudian simpan dan terdokumentasi di aplikasi note anda, berikut juga penjelasan singkat, dikarenakan hal itu sangat penting, ketika anda ingin membuat reportnya nanti.

## Pengetikan Report
Untuk report anda dapat menggunakan yang telah disediakn oleh <a href="https://help.offensive-security.com/hc/en-us/articles/360040165632-OSCP-Exam-Guide">OffSec OSCP Exam Guide</a>,atau dari <a href="https://github.com/noraj/OSCP-Exam-Report-Template-Markdown">Offensive Security Exam Report Template in Markdown</a> **catatan: saya menggunakan report markdown tersebut**. Pastikan mulai melakukan penulisan ketika anda sudah fresh dari istirahat yang cukup, kemudian mulailah melakukan pengetikan report. Setelah itu segera upload ke link yang sediakan oleh OffSec, pastikan anda telah mengoreksi grammar, screen-shot dan source code yang jelas, serta langkah - langkah yang jelas.

Untuk selanjutnya anda tinggal menunggu hasil dari OffSec yang akan diumumkan sesuai dengan hari kerja, dan dari pengalaman saya yaitu waktu exam selesai jam 23:00 WIB dan dimulai dari 06:00WIB (hampir 18 jam), kemudian istirahat dan mulai membuat report pada jam 08:00WIB di pagi hari berikutnya. Pastikan anda jangan terlalu gugup/anxiety, resah, tetap berpikir tenang, ambil istirahat & coba lakukan hal yang membuat anda refresh/tenang, dan berdoa.

## Pengumuman Hasil
Untuk pengumuman hasil akan diumumkan melalui email dan sekitar 1-2 hari (hari kerja). Setelah menunggu sekitar 1.5 hari setelah pengiriman report, dan tepat pada rabu, 24 Agustus 2021, saya mendapatkan email dan dikabarkan lulus ujian OSCP

![Desktop View](/assets/img/posts/oscp.png)
_Pengumuman Kelulusan OSCP_

Berdasarkan informasi terbaru dari Offensive Security, untuk badge dapat diakses menggunakan accredible.

<iframe
  src="https://credentials.offsec.com/embed/6dad89ac-d662-4067-af42-1c7448dba77c"
  width="800"
  height="600"
  frameborder="0"
  allowfullscreen>
</iframe>