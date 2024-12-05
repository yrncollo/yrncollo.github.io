---
title: "CyberTalents Bootcamp 2022"
date: 2022-08-08
categories: [Bootcamp, Cybertalent]
tags: [wget, wireshark, strings, base64, curl, rot13, Kali linux, forensics, steganography, exiftool, steghide, stegcracker, cyberchef, audacity]     # TAG names should always be lowercase
---
This is [CyberTalents](https://cybertalents.com/) Bootcamp(Online)2022 challenges.
# Introduction to CTF
## Competition
**Description**: Special kind of cybersecurity competition designed to challenge its participants to solve computer security problems.

**solution**: `ctf`

# Cybersecurity Overview
## fisher
**Description:** What is the job match the below definition?

Individuals who know the nuts and bolts of cybersecurity and are well versed in finding flaws and vulnerabilities. There are various bug bounty platforms that allow them to be paid to find vulnerabilities in applications and software.

Flag format: flag{xxx xxxxxx xxxxxx}

**Solution:** This challenge doesn't need alot of explanation. You can do other research on `bug bounty hunter`

`flag{bug bounty hunter}`

# Attacks & Vulnerabilities

## Mailer

**Description:** we got the evidence for the phishing Email but we need to know the name of malware file

flag format flag{}

**Solution:**

We shall use `wget` to download the file we are given.
```shell
wget https://hubchallenges.s3-eu-west-1.amazonaws.com/Forensics/mail_bak.7z
```
Once downloaded we shall unzip the `mail_bak.7z` by use of `7z`
```shell
└─$ 7z x mail_bak.7z

7-Zip [64] 16.02 : Copyright (c) 1999-2016 Igor Pavlov : 2016-05-21
p7zip Version 16.02 (locale=en_US.UTF-8,Utf16=on,HugeFiles=on,64 bits,4 CPUs Intel(R) Core(TM) i5-4210U CPU @ 1.70GHz (40651),ASM,AES-NI)

Scanning the drive for archives:
1 file, 235694 bytes (231 KiB)

Extracting archive: mail_bak.7z
--
Path = mail_bak.7z
Type = 7z
Physical Size = 235694
Headers Size = 177
Method = LZMA2:3m
Solid = +
Blocks = 1

Everything is Ok

Files: 2
Size:       2714896
Compressed: 235694
```
We have sent and inbox files. We need to know the name of malware file which was sent so we shall cat the contents of sent and grep `.exe` file extension.
```shell
└─$ cat Sent | grep .exe
> ctbank.com/Mal_strike8941934890753353453.exe
> <http://ctbank.com/Mal_strike8941934890753353453.exe>
>>     www.ctbank.com/Mal_strike8941934890753353453.exe
>>     <http://www.ctbank.com/Mal_strike8941934890753353453.exe>
            href="http://ctbank.com/Mal_strike8941934890753353453.exe">ctbank.com/Mal_strike8941934890753353453.exe</a><br>
                          href="http://www.ctbank.com/Mal_strike8941934890753353453.exe"
> Account director Evan Russack becomes the No. 2 digital exec
> appointment of Evan Russack to executive director of digital. Russack,
                                                digital exec</span>
                                      executive director of digital.
Subject: Re: BREAKING: Nasdaq drops another 1.3% Friday, other major indexes
                              <td>How C-suite execs have shifted their
grep: (standard input): binary file matches
```
Awesome, we have the name of the file which is `Mal_strike8941934890753353453.exe` and that's our flag.
`flag{Mal_strike8941934890753353453.exe}`

# Network Security Fundamentals
## Mask
**Description:** What is the subnet mask for this host?
![subnet](/assets/img/Posts/Cybertalent/download.png)

**Solution** We can clearly see the subnet mask is `255.255.255.0` and that's our flag.

# Wireshark
## cl34r
**Description:** Can You see the password clearly

**Solution:**

We shall use `wget` to download the file.
```bash
wget https://hubchallenges.s3.eu-west-1.amazonaws.com/Forensics/cl34r.pcapng
```
Once opened on `wireshark` we can clearly see some movement of packets.

![wireshark1](/assets/img/Posts/Cybertalent/wireshark1.png)

Let's follow the `GET` request on `/login.php` packet
There we can see the flag but it is url encoded.

![follow](/assets/img/Posts/Cybertalent/follow.png)

We shall use [`cyberchef`](https://gchq.github.io/CyberChef/) to decode the `url encoded` string.

![cyberchef](/assets/img/Posts/Cybertalent/cyberchef.png)

Awesome we have our flag `flag{Th1s_15_t0_cl34r}`

## Anonymous
**Description:** Can you trace the anonymous guy?

**Solution:**

We shall use `wget` to download the file.
```shell
└─$ wget https://hubchallenges.s3-eu-west-1.amazonaws.com/Forensics/anonymous.pcap
```
Let's use the `strings` to see if we can get something out of it

```shell
└─$ strings -n 10 anonymous.pcap
220 pyftpdlib 1.5.4 ready.
USER anonymoys
331 Username ok, send password.
PASS anonymous
530 Authentication failed.
215 UNIX Type: L8
PORT 192,168,0,164,179,157
530 Log in with USER and PASS first.
530 Log in with USER and PASS first.
PORT 192,168,0,164,179,158
530 Log in with USER and PASS first.
220 pyftpdlib 1.5.4 ready.
 CKAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
 CKAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
USER anonymous
331 Username ok, send password.
PASS anonymous
230 Login successful.
215 UNIX Type: L8
PORT 192,168,0,164,181,46
200 Active data connection established.
125 Data connection already open. Transfer starting.
drwxrwxrwt   2 root     root         4096 Mar 11 18:29 .ICE-unix
drwxrwxrwt   2 root     root         4096 Mar 11 18:29 .Test-unix
drwxrwxrwt   2 root     root         4096 Mar 11 18:29 .X11-unix
drwxrwxrwt   2 root     root         4096 Mar 11 18:29 .XIM-unix
drwxrwxrwt   2 root     root         4096 Mar 11 18:29 .font-unix
-rw-r--r--   1 root     root           37 Mar 11 18:35 flag.txt
drwx------   3 root     root         4096 Mar 11 18:29 systemd-private-554f9bdfbd734f3b8cac1440e18753dc-apache2.service-Rsquyf
drwx------   3 root     root         4096 Mar 11 18:29 systemd-private-554f9bdfbd734f3b8cac1440e18753dc-systemd-logind.service-nXcWeh
drwx------   3 root     root         4096 Mar 11 18:29 systemd-private-554f9bdfbd734f3b8cac1440e18753dc-systemd-resolved.service-8ajXRf
226 Transfer complete.
 CKAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
 CKAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
 CKAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
 CKAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
200 Type set to: Binary.
PORT 192,168,0,164,182,186
200 Active data connection established.
RETR flag.txt
125 Data connection already open. Transfer starting.
ZmxhZ3thbm9ueW1vdXNfdDBfdGgzX2VuZH0=
226 Transfer complete.
```
Well as we can see we have a `base64` string their and we have to decode it.
```shell
└─$ echo "ZmxhZ3thbm9ueW1vdXNfdDBfdGgzX2VuZH0=" | base64 -d
flag{anonymous_t0_th3_end}
```
Awesome we have our flag: `flag{anonymous_t0_th3_end}`

# OSI Model
## http
**Description:** start the server and you will be able to reach the flag on ctweb.com?

username: ctf

password: ctf

**Solution:**
The first thing is to start the challenge and you are given a web shell. We shall use `curl` to see what's on `ctweb.com`.
```shell
ctf@wlem4m2366qcg3011y3e1e0sxzm61y3eq89pbx3m-web-5949f4679f-srh8v:~$ curl ctweb.com                                                                               6 ⨯
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml">
  -->
  <head>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
    <title>access /fl4g.html directory to get your flag</title>
  </head>
  </html>ctf@wlem4m2366qcg3011y3e1e0sxzm61y3eq89pbx3m-web-5949f4679f-srh8v:~$
  ```
We shall navigate to `/fl4g.html` directory so as we get the flag and we shall use `curl` again.

```shell
ctf@wlem4m2366qcg3011y3e1e0sxzm61y3eq89pbx3m-web-5949f4679f-srh8v:~$ curl ctweb.com/fl4g.html
flag{htt9_pR0toc01}
ctf@wlem4m2366qcg3011y3e1e0sxzm61y3eq89pbx3m-web-5949f4679f-srh8v:~$
```
Awesome we have our flag `flag{htt9_pR0toc01}`

## address
**Description:** (What is the source and destination IP/MAC for packet number 258 Mac address in small letters.)

Flag format: flag{Source IP/Source MAC/Destination IP/Destination MAC}

**Solution:** This was fairly simple challenge and all you were required to do is load the `pcapng` file to `wireshark` and check packet number 258.

Use `wget` to download the file.
```shell
└─$ wget https://hubchallenges.s3.eu-west-1.amazonaws.com/foren/Wireshark101.pcapng
```
![address](/assets/img/Posts/Cybertalent/address.png)

We have all the information. We shall get all the information required by the challenge _i.e IP/source MAC/Destination IP/Destination_
flag: `flag{40.77.226.250/38:54:9b:31:db:b8/192.168.1.4/08:00:27:ba:8c:38}`

# Network Protocol
## bflag

**Description**
All of us started from the bottom. Now it's your turn.

**Solution:**

For this challenge we have to analyze the http traffic packets by right clicking > follow > tcp stream.
```
GET /f14g/analyze_packet_for_fun HTTP/1.1
Host: www.aldabaknocking.com
User-Agent: Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:67.0) Gecko/20100101 Firefox/67.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Connection: keep-alive
Cookie: _ga=GA1.2.1907807868.1562271214; _gid=GA1.2.418247249.1562271214; _gat=1
Upgrade-Insecure-Requests: 1

HTTP/1.1 404 Not Found
Date: Thu, 04 Jul 2019 20:14:16 GMT
Server: Apache
Content-Length: 344
Keep-Alive: timeout=2, max=100
Connection: Keep-Alive
Content-Type: text/html; charset=iso-8859-1

<!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
<html><head>
<title>404 Not Found</title>
</head><body>
<h1>Not Found</h1>
<p>The requested URL /f14g/analyze_packet_for_fun was not found on this server.</p>
<p>Additionally, a 404 Not Found
error was encountered while trying to use an ErrorDocument to handle the request.</p>
</body></html>
```

By following stream 86 you'll find `analyze_packet_for_fun was not found on this server` and trying `analyze_packet_for_fun` it was the correct flag.


## ADAMIN
**Description:** Admin of FTP server is spoofed fetching employers data, `pcap` attached of victim activity

**Solution:**
We shall start by downloading the files
```shell
└─$ wget https://hubchallenges.s3.eu-west-1.amazonaws.com/Forensics/ADAMIN.pcap
```
We shall filter by `ftp-data`
![wireshark](/assets/img/Posts/Cybertalent/wiresharkadmin.png)
We have two packets and one has salaries.zip content. We shall follow `tcp stream` and save the raw content
![wireshark](/assets/img/Posts/Cybertalent/wiresharksave.png)
Let's unzip the `salaries.zip` file which we saved earlier.
It seems it is password protected. Let's search for the password on the `pcap` file. We shall filter by `ftp` and we can see clear password which is `oneforallpass`.
Let's unzip again the file supplying the password.
We have two files `.ADAM.txt` and `Salaries.csv.tar.xz`. Well if we open the ADAM.txt file we have the flag.

Flag: `flag{0ne_4_aLL_pa$$}`

## MAK
**Description:**
Given wireshark capturing file. What is the MAC address of the gateway router?

flag in format : flag{MAC address}

**Solution:**

We shall download the file which we are given.
```shell
└─$ wget https://hubchallenges.s3.eu-west-1.amazonaws.com/networkessentials/MAK.pcap.zip
```
After that `unzip` the file which we have downloaded
```shell
└─$ unzip MAK.pcap.zip
Archive:  MAK.pcap.zip
  inflating: MAK.pcap
```
We shall then open the `pcap` file in `wireshark` and navigate to statistics > endpoints > IPv4.10

With that we can see `10.0.0.1` is the first IP address meaning its the default gate way.
![statistics](/assets/img/Posts/Cybertalent/statistics.png)

```
839	19.478289	Netgear_67:ea:76		ARP	62	Who has 10.0.0.21? Tell 10.0.0.1
```
We are required to get the MAC address.
```
Linux cooked capture v1
    Packet type: Unicast to us (0)
    Link-layer address type: Ethernet (1)
    Link-layer address length: 6
    Source: Netgear_67:ea:76 (a4:2b:8c:67:ea:76)
    Unused: 9a9e
    Protocol: ARP (0x0806)
    Padding: 00000000000000000000000000000000
    Trailer: 0000
```
Well we have the MAC address...

**flag:** `flag{a4:2b:8c:67:ea:76}`

# Data Encoding

## keep ASCIIng
**Description:**
One form of string representations is readable but the other is not. Can you read the flag. 102108097103123067084095049115116069097115121083051099114051116051080064115115112104114052053051125 Flag format is FLAG{XXXXXXXXXX}

**Solution:**
This challenge is so simple. Find a `asci` to text converter and you can use [this site](https://www.unit-conversion.info/texttools/ascii/). If you have time you can use `asci table` and convert one by one till you form the flag.

**flag:** `flag{CT_1stEasyS3cr3t3P@ssphr453}`

# Data Encryption
## Hide Data

**Description:**
I used to hide my data with a classic cypher, can you get the flag hidden inside? gur synt vf 2w68lsudym Vg vf cerggl rnfl gb frr gur synt ohg pna lbh frr vg v gbbx arneyl 1 zvahgr gb rapbqr guvf jvgu EBG13 tbbq yhpx va fbyivat gung

**Solution:** Well I used [`Cyberchef`](https://gchq.github.io/CyberChef/) to and used `ROT13` and after that we find: `the flag is 2j68yfhqlz It is pretty easy to see the flag but can you see it i took nearly 1 minute to encode this with ROT13 good luck in solving that`

Awesome we found the flag and it's `2j68yfhqlz`

# Introduction to Kali Linux
## remove
**Description:** I need to remove a file called secret in my home directory. which command should i use

**Solution:** `rm ~/secret`

## Distribution
**Description:** Debian-based distribution with a collection of security and forensics tools. It features timely security updates, support for the ARM architecture

The flag without spaces

**Solution:** `Kalilinux`

## Linuxcmd 101
**Description:** This Challenge will help you understand essential commands in Linux OS

Each point is linked to another point, connect the link and win the Flag!

**Solution:** Hey don't be a script kiddie atleast give a try to this challenge.

# Hash Cracking
## Guess The Password
**Description:**
A hacker leaked the below hash online.Can you crack it to know the password of the CEO? the flag is the password Hash: 06f8aa28b9237866e3e289f18ade19e1736d809d

**Solution:**
You can use hash identifier to know which hash we are cracking. You'll find that the hash is `sha1` and if you crack it you'll get Password. You can use any tool to decode the `sha1` hash and you will find hash is `jrahyn+`

# Introduction to Digital Forensics
## Crime scene
**Description:** Process of analyzing and investing computer devices, on suspecting that such devices may have been used in a cybercrime

Flag Format: XXXXXXXXX
**Solution:**

`Forensics`

# Steganography
## Greeks
**Description:** The art of hiding messages or information inside other image / text or data

**Solution:** `Steganography`

## Images3c
**Description:** Securing an important image requires good encryption. so we added extra security layer for your photo and now is unbreakable!

**Solution:**

We shall use couple of tools to solve this challenge. I first used `exiftool` to check if the `metadata`
```shell
└─$ exiftool cyber.jpg
ExifTool Version Number         : 12.41
File Name                       : cyber.jpg
Directory                       : .
File Size                       : 212 KiB
File Modification Date/Time     : 2020:03:27 15:03:20+03:00
File Access Date/Time           : 2022:08:08 16:36:07+03:00
File Inode Change Date/Time     : 2022:08:08 16:35:49+03:00
File Permissions                : -rw-r--r--
File Type                       : JPEG
File Type Extension             : jpg
MIME Type                       : image/jpeg
JFIF Version                    : 1.01
Resolution Unit                 : inches
X Resolution                    : 300
Y Resolution                    : 300
Image Width                     : 720
Image Height                    : 720
Encoding Process                : Baseline DCT, Huffman coding
Bits Per Sample                 : 8
Color Components                : 3
Y Cb Cr Sub Sampling            : YCbCr4:2:0 (2 2)
Image Size                      : 720x720
Megapixels                      : 0.518
```
Then I used `steghide` to extract if there is any file hidden on the image.
```shell
└─$ steghide extract -sf cyber.jpg                                                                                                                                    1 ⨯
Enter passphrase:
steghide: could not extract any data with that passphrase!
```
Well we have password. We have to brute force the password with `stegcracker`
```shell
└─$ stegcracker cyber.jpg
StegCracker 2.1.0 - (https://github.com/Paradoxis/StegCracker)
Copyright (c) 2022 - Luke Paris (Paradoxis)

StegCracker has been retired following the release of StegSeek, which
will blast through the rockyou.txt wordlist within 1.9 second as opposed
to StegCracker which takes ~5 hours.

StegSeek can be found at: https://github.com/RickdeJager/stegseek

No wordlist was specified, using default rockyou.txt wordlist.
Counting lines in wordlist..
Attacking file 'cyber.jpg' with wordlist '/usr/share/wordlists/rockyou.txt'..
Successfully cracked file with password: 1234
Tried 1973 passwords
Your file has been written to: cyber.jpg.out
1234
```
We have the password as `1234` and the content hidden on the file was extracted. Now all we have to do is read the `cyber.jpg.out`.
Awesome we have the flag.
```shell
└─$ cat cyber.jpg.out
flag{cyb3rs3cisaw3s0me}
```
<!--
## JTAG Dump
**Description:** This is a JTAG mobile Extraction for a handset in a criminal case. Catch the Flag

**Solution:**
We shall use `wget` to download the file we are given.
```shell
└─$ wget https://s3-eu-west-1.amazonaws.com/hubchallenges/Forensics/Jtag-dump.bin
```
 I started by using `file` to know the file format of the file we downloaded.
 ```shell
 └─$ file Jtag-dump.bin
Jtag-dump.bin: data
```
We shall use `binwalk` to extract the files.
```shell
└─$ binwalk -M --dd=".*" Jtag-dump.bin
```
Once that is done we shall have a directory containing extracted files called `_Jtag-dump.bin.extracted`.The files in there seemed strange and by checking it, it has alot of images files.

-->




## I love music
**Description:** listen and focus , you will listen another thing

Flag format : XXX_XXX_XXXXXXXXXXX

**Solution:**

This challenge was some how tricky at first but after a some tries I managed to solve it.
we shall download the audio file
```shell
└─$ wget https://s3-eu-west-1.amazonaws.com/hubchallenges/Forensics/love-music.wav
```
The next task is to download audacity and if you are on linux it's fairly easy.
```shell
└─$ sudo apt install audacity
```
Once you have downloaded `audacity`, import the downloaded file `love-music.wav` to audacity.
Change audacity settings to spectrogram and you will see the flag there.
![audacity](/assets/img/Posts/Cybertalent/audacity.png)

Awesome!! flag: `you_are_victorious!`
