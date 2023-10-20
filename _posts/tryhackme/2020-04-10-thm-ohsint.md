---
title: "TryHackMe - OhSINT"
classes: wide
search: false
categories: 
  - TryHackMe
toc: true
toc_label: "Contents"
last_modified_at: 2020-04-10T15:58:26+08:00
---

> Are you able to use open-source intelligence to solve this challenge?

OhSINT is a free challenge on TryHackMe. You can try it without subscribing. This challenge has a purpose in using resources on the internet to find the target or collect the information.

### What information can you possible get starting with just one photo?

First, we could download an image from the 1st task.

![windowsxp-wallpapers](/assets/images/thm-ohsint/WindowsXP.jpg)

### #1 What is this users avatar of?

Hints
```
exiftool is your friend. Who is the author of the image? Do they have any social media accounts?
```
from the hints, we need to use ExifTool to find the information from image `WindowsXP.jpg`

```
╭─twseptian@twsterlab ~/lab/THM/rooms/OhSINT 
╰─$ exiftool WindowsXP.jpg 
ExifTool Version Number         : 11.99
File Name                       : WindowsXP.jpg
Directory                       : .
File Size                       : 229 kB
File Modification Date/Time     : 2020:06:13 17:08:28+07:00
File Access Date/Time           : 2020:06:13 17:08:50+07:00
File Inode Change Date/Time     : 2020:06:13 17:08:46+07:00
File Permissions                : rw-r--r--
File Type                       : JPEG
File Type Extension             : jpg
MIME Type                       : image/jpeg
XMP Toolkit                     : Image::ExifTool 11.27
GPS Latitude                    : 54 deg 17' 41.27" N
GPS Longitude                   : 2 deg 15' 1.33" W
Copyright                       : OWoodflint
Image Width                     : 1920
Image Height                    : 1080
Encoding Process                : Baseline DCT, Huffman coding
Bits Per Sample                 : 8
Color Components                : 3
Y Cb Cr Sub Sampling            : YCbCr4:2:0 (2 2)
Image Size                      : 1920x1080
Megapixels                      : 2.1
GPS Latitude Ref                : North
GPS Longitude Ref               : West
GPS Position                    : 54 deg 17' 41.27" N, 2 deg 15' 1.33" W
```

we got the owner's name from image `OWoodflint` and let's use google search to find the owner's name

![google-search](/assets/images/thm-ohsint/google-search.png)

The owner has three social media account:
1. Twitter Account - https://twitter.com/owoodflint

![twitter-account](/assets/images/thm-ohsint/twitter-account.png)

2. GitHub - https://github.com/OWoodfl1nt/people_finder

![github-account](/assets/images/thm-ohsint/github-account.png)

3. Wordpress - https://oliverwoodflint.wordpress.com/author/owoodflint/

![wordpress-account](/assets/images/thm-ohsint/wp-account.png)

come back to the question, the user's avatar is `cat`

ANS: `cat`

### #2 What city is this person in?

he was mention on the owner's GitHub about where he came from `Hi all, I am from London, I like taking photos and open source projects.`

Hint
```
BSSID + Wigle.net
```
ANS: `London`
  
### #3 Whats the SSID of the WAP he connected to?

on twitter, the owner was posted about `bssid` account is `B4:5D:50:AA:86:41`, and he was coming from `London`

![twitter-post](/assets/images/thm-ohsint/twitter-bssid.png)

let's see a hint on question number two `BSSID + Wigle.net`

![wiggle-post](/assets/images/thm-ohsint/wiggle-bssid.png)

ANS: `UnileverWifi`

### #4 What is his personal email address?

go to his Github repository, and we got information about his email

```
Hi all, I am from London, I like taking photos and open source projects.

Follow me on twitter: @OWoodflint

This project is a new social network for taking photos in your home town.

Project starting soon! Email me if you want to help out: OWoodflint@gmail.com
```

ANS: `OWoodflint@gmail.com`

### #5 What site did you find his email address on?

ANS: `github`

### #6 Where has he gone on holiday?

let's see on his WordPress

![wp-post](/assets/images/thm-ohsint/wp-post.png)

ANS:`New York`

### #7 What is this persons password?

use `view page source` on his wordpress blog
![wp-post](/assets/images/thm-ohsint/wp-view-source.png)

ANS: `pennYDr0pper.!`


