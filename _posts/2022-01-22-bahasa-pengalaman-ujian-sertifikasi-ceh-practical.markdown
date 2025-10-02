---
layout: post
title:  "[Bahasa] Pengalaman Ujian Sertifikasi CEH Practical"
date:   2022-01-22 15:09:19 +0700
author: twseptian
pin: true
categories: [Certification]
tags: [certification, infosec]
---

## Pendahuluan
Ini adalah cerita pengalaman saya mengikuti ujian CEH Practical yang ingin saya bagikan kepada orang - orang yang sedang atau ingin mengambil sertifikasi CEH Practical.

Kesempatan yang saya dapatkan untuk mengambil untuk mengikuti ujian sertifikasi CEH Practical, ketika membaca informasi mengenai CEH Practical Scholarship pada tahun 2020, yang memberikan harga sekitar $99 dari harga normal yaitu sekitar $550, hal tersebut membuat saya tidak menyiakan kesempatan, dengan mengikuti intruksi - intruksi dan akhirnya mendapatkan kesempatan untuk exam (scholarship) pada akhir tahun 2020 dengan diberikan code dashboard Aspen.

Saya tidak mengambil exam tersebut secara langsung setelah mendapatkan code Aspen, dikarenakan sedang fokus ke sertifikasi OSCP, jadi code tersebut saya simpan kemudian saya aktifkan sekitar bulan juni 2021, dan berkesempatan mengambil ujian dipenghujung akhir masa kadaluarsa untuk ujian CEH Practical tersebut.

## CEH Practical
Ujian CEH Practical merupakan ujian yang di-proctor atau diawasi oleh pihak penyedia seperti Aspen. Dengan jumlah soal terdiri dari 20 soal real-life scenarios, dan waktu pengerjaan selama 6 jam.

Berikut spesifikasi untuk CEH Practical:

Exam Title: Certified Ethical Hacker (Practical)
- Number of Practical Challenges: 20
- Duration: 6 hours
- Availability: Aspen – iLabs
- Test Format: iLabs Cyber Range
- Passing Score: 70%
- Open Book: Yes

CEH Practical sangat berbeda dengan CEH yang sering kita ketahui pada umumnya, atau yang sering disebut dengan CEH ANSI yang memberikan pengalaman untuk ujian sertifikasi tersebut dengan menjawab pertanyaan multiple-choice/pilihan ganda dengan waktu/durasi penyelasaian selama 4 jam. Hal tersebut yang menjadi pembanding dengan CEH Practical, yaitu untuk CEH Practical menggunakan Aspen- iLabs yang akan memberikan kita dua mesin sistem operasi yaitu Parrot OS dan Windows 10 untuk mencari jawaban selama ujian.

Topik - topik yang termasuk dalam CEH Practical:

- Network scanning untuk mengidentifikasikan host,port dan service yang aktif pada sebuah network/jaringan.
- Dapat mengidentifikasikan sistem operasi (OS),serta enumeration pada host/mesin yang aktif
- Melakukan proses system hacking, steganography,crypthograpy
- Packet Sniffing, dan mengerti mengenai deep packet inpection pada sebuah file .pcap/.pcapng
- Web application security, seperti SQL injection
- Cracking hash dan password

beberapa tools yang saya gunakan sesuai dengan pengalaman saya, antara lain:

- Scanning & Reconnaissance: nmap
- Sniffing: wireshark,tcpdump
- Web application attacks: burp suite, owasp zap tools, sqlmap
- Brute-force attack dan password cracking: hydra, john the ripper
- Crypthography: HashCalc, md5sum, veracrypt, cryptoforge, BCTextEncoder Utility, SONAR.exe, Cryptool
- Steganography: open stego

dan beberapa tool pendukung seperti untuk remote desktop yang ada pada linux, antara lain: remmina, xfreerdp

## Persiapan Ujian
Beberapa hal yang harus dipersiapkan untuk ujian, antara lain:

- Pastikan koneksi internet anda stabil dan baik, sehingga tidak ada kendala selama mengikuti ujian, dan pengalaman saya mesin Parrot OS dan Windows 10 yang diakses dari Aspen - iLabs sangat slows.
- Baca dan ta’ati intruksi & aturan yang diberikan oleh proctor.
- Persiapkan tools protocor. Pada saat saya ujian, saya menggunakan gotomeeting yang terinstall di laptop saya,kemudian proctor akan memantau kegiatan anda, melalui webcam, sharing screen, dan microphone.
- Menggunakan browser, seperti firefox,chrome, dikarenakan untuk mengakses iLabs wajib menggunakan browser.
- Silahkan persiapkan website pendukung anda, baik berupa cheatsheet yang anda sukai atau googling saja, karena hal tersebut tidak dilarang.
- Tidak dianjurkan berdiskusi dengan orang lain, handphone, dan juga pastikan dalam ruangan anda tidak boleh ada orang lain selain anda.
- Anda diberikan waktu 15 menit untuk istirahat atau ke toilet.
Selama mengikuti ujian pastikan menaati aturan - aturan yang diberikan, dan pastikan butuh kesabaran yang besar ketika mengakses mesin Aspen - iLabs dan fokus pada setiap task/soal - soal.

Setelah selesai mengerjakan pastikan berkomunikasi terlebih dahulu dengan proctor sebelum meng-click “Submit”. Setelah proses submitting jawaban kita, hasil kelulusan akan langsung diumumkan di akun Aspen kita, dan akan diberikan hasil transcript ujian,sertifikasi pdf, badge digital, beserta logo CEH Practical.

## Catatan & Pernyataan Penolakan
- Postingan ini mencerminkan **pengalaman pribadi saya** dan urutan waktu.  
- Alat dan topik yang tercantum hanyalah **contoh**, bukan kurikulum resmi.  
- Selalu **patuhi hukum dan kebijakan ujian**.  
- Konten dibagikan untuk **tujuan pendidikan saja**.  

<a href="https://aspen.eccouncil.org/VerifyBadge?type=certification&a=RjLpHlv94HwrHByLKZw02VkNYlg0sDNqTcb0UnyQ760=">Verifikasi</a>