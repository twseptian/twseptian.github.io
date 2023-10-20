---
title: "TryHackMe - Easy Steganography"
classes: wide
search: false
categories: 
  - TryHackMe
last_modified_at: 2020-07-03T15:58:26+08:00
toc: true
toc_label: "Contents"
---

An easy steganography challenge. No hint, just solve it. This is a free room, which means anyone can deploy virtual machines in the room (without being subscribed)!. 
This room created by [DesKel](https://tryhackme.com/p/DesKel)

## [Task 1] Steganography challenge

This room has beneficial to knows about steganography and how to analyze images using a steagano tool. From the beginning, let's download the .zip file; it contains some of the images to analyze. After zip files extracted, some of the pictures look similar, but we need to examine each other.

### flag1.jpeg

For the flag1.jpeg, we tried to use `xxd` combine with `grep` and `St`keyword.

```shell
┌─[twseptian@twsterlab] - [~/lab/THM/rooms/easy-steganography/easystego] - [Thu Jul 02, 00:57]
└─[$]> xxd flag1.jpeg | grep St
0000a0d0: 8156 f444 6053 7489 4684 de73 a4ec a6e6  .V.D`St.F..s....
00011680: a815 7067 3ce7 5374 3367 346e 30e2 9390  ..pg<.St3g4n0...
┌─[twseptian@twsterlab] - [~/lab/THM/rooms/easy-steganography/easystego] - [Thu Jul 02, 00:58]
└─[$]> 

```
> ANS: St3g4n0

### flag2.jpeg

On flag2.jpeg, we need to check using `binwalk`, so flag2.jpeg contains some of the files, and we need to extract all data from flag2.jpeg.

**Binwalk Check**
```shell
┌─[twseptian@twsterlab] - [~/lab/THM/rooms/easy-steganography/easystego] - [Thu Jul 02, 00:32]
└─[$]> binwalk -D=".*" flag2.jpeg 

DECIMAL       HEXADECIMAL     DESCRIPTION
--------------------------------------------------------------------------------
0             0x0             JPEG image data, EXIF standard
12            0xC             TIFF image data, little-endian offset of first image directory: 8
78447         0x1326F         JPEG image data, JFIF standard 1.01
```

**Binwalk Extract**
```shell
┌─[twseptian@twsterlab] - [~/lab/THM/rooms/easy-steganography/easystego] - [Thu Jul 02, 00:33]
└─[$]> ls -l
total 372
-rw-r--r-- 1 twseptian twseptian  78445 Oct 17  2019 flag1.jpeg
-rw-r--r-- 1 twseptian twseptian 116386 Oct 17  2019 flag2.jpeg
drwxr-xr-x 2 twseptian twseptian   4096 Jul  2 00:33 _flag2.jpeg.extracted
-rw-r--r-- 1 twseptian twseptian  78491 Oct 17  2019 flag3.jpeg
-rw-r--r-- 1 twseptian twseptian  93174 Oct 17  2019 flag4.jpeg
┌─[twseptian@twsterlab] - [~/lab/THM/rooms/easy-steganography/easystego] - [Thu Jul 02, 00:33]
└─[$]> cd _flag2.jpeg.extracted 
┌─[twseptian@twsterlab] - [~/lab/THM/rooms/easy-steganography/easystego/_flag2.jpeg.extracted] - [Thu Jul 02, 00:33]
└─[$]> ls -l
total 272
-rw-r--r-- 1 twseptian twseptian 116386 Jul  2 00:33 0
-rw-r--r-- 1 twseptian twseptian  37939 Jul  2 00:33 1326F
-rw-r--r-- 1 twseptian twseptian 116374 Jul  2 00:33 C
┌─[twseptian@twsterlab] - [~/lab/THM/rooms/easy-steganography/easystego/_flag2.jpeg.extracted] - [Thu Jul 02, 00:33]
└─[$]> 
```
After flag2-jpeg has been extracted, we got some of the files. Let's open `0` in the extracted directory so that we can look the answer for the flag2.jpeg

![flag2-jpeg](/assets/images/thm-easy-steganography/flag2-jpeg.png)

> ANS: algorithm

### flag3.jpeg

For flag3.jpeg is using `strings.` we can look `Math` as an answer 

```shell
┌─[twseptian@twsterlab] - [~/lab/THM/rooms/easy-steganography/easystego] - [Thu Jul 02, 00:26]
└─[$]> strings flag3.jpeg 
Exif
Ducky
http://ns.adobe.com/xap/1.0/
<?xpacket begin="
" id="W5M0MpCehiHzreSzNTczkc9d"?> <x:xmpmeta xmlns:x="adobe:ns:meta/" x:xmptk="Adobe XMP Core 5.3-c011 66.145661, 2012/02/06-14:56:27        "> <rdf:RDF xmlns:rdf="http://www.w3.org/1999/02/22-rdf-syntax-ns#"> <rdf:Description rdf:about="" xmlns:xmpMM="http://ns.adobe.com/xap/1.0/mm/" xmlns:stRef="http://ns.adobe.com/xap/1.0/sType/ResourceRef#" xmlns:dc="http://purl.org/dc/elements/1.1/" xmlns:xmp="http://ns.adobe.com/xap/1.0/" xmpMM:DocumentID="xmp.did:64F9CFDDC66511E3B8DFD7C492300532" xmpMM:InstanceID="xmp.iid:64F9CFDCC66511E3B8DFD7C492300532" xmp:CreatorTool="Adobe Photoshop CS6 Windows"> <xmpMM:DerivedFrom stRef:instanceID="63AE842734FCAF58DD2027CD97F2F89A" stRef:documentID="63AE842734FCAF58DD2027CD97F2F89A"/> <dc:title> <rdf:Alt> <rdf:li xml:lang="x-default">168351254</rdf:li> </rdf:Alt> </dc:title> </rdf:Description> </rdf:RDF> </x:xmpmeta> <?xpacket end="r"?>
HPhotoshop 3.0
8BIM
8BIM
x/4b4
&Adobe
*The passphrase to this challenge is Math
```
> ANS: Math

### flag4.jpeg

For flag4.jpeg, we could try using the steps from How to find a flag on the flag2.jpeg. First, we need to check using binwalk, next we need to extract and read the file on `0` directory.
```shell
┌─[twseptian@twsterlab] - [~/lab/THM/rooms/easy-steganography/easystego] - [Thu Jul 02, 00:56]
└─[$]> binwalk flag4.jpeg 

DECIMAL       HEXADECIMAL     DESCRIPTION
--------------------------------------------------------------------------------
0             0x0             JPEG image data, EXIF standard
12            0xC             TIFF image data, little-endian offset of first image directory: 8
78447         0x1326F         XML document, version: "1.0"

┌─[twseptian@twsterlab] - [~/lab/THM/rooms/easy-steganography/easystego] - [Thu Jul 02, 00:56]
└─[$]> 
```

binwalk extract
```shell
┌─[twseptian@twsterlab] - [~/lab/THM/rooms/easy-steganography/easystego] - [Thu Jul 02, 00:56]
└─[$]> binwalk -D=".*" flag4.jpeg     
```

got to `_flag4.jpeg.extracted` and type `strings` on file 0
```shell
┌─[twseptian@twsterlab] - [~/lab/THM/rooms/easy-steganography/easystego/_flag4.jpeg.extracted] - [Thu Jul 02, 00:55]
└─[$]> strings 0 | tail     
="false" uof:locID="t0195" uof:attrList="
"/></
 uof:locID="t0085"><
 uof:locID="t0086" uof:attrList="
="P1"/><
 uof:locID="t0109" uof:attrList="
">TryHardered</
 uof:locID="t0050"/></
></uof:
></uof:UOF>
┌─[twseptian@twsterlab] - [~/lab/THM/rooms/easy-steganography/easystego/_flag4.jpeg.extracted] - [Thu Jul 02, 00:55]
└─[$]>
```
> ANS: TryHardered