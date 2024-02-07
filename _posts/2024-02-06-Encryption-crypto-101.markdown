---
layout: post
title:  "THM: Encryption-Crypto 101"
date:   2024-02-06 13:50:01 -0600
categories: TryHackMe
image: /assets/img/crypto101.png
---
Blew thru most of this room but here what was interesting to take note

## Task 9 SSH Authentication
I spun up the Linux room just like suggested and tried this to refresh my memory on how to do this

```
ssh-keygen

Generating public/private rsa key pair.
Enter file in which to save the key (/root/.ssh/id_rsa): 
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Passphrases do not match.  Try again.
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /root/.ssh/id_rsa.
Your public key has been saved in /root/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:Si5zj4AjcEUPliR7TAsqQeukppbLhCXu7iXXlT3C8po root@ip-10-10-171-2
The key's randomart image is:
+---[RSA 2048]----+
|o.o.*.           |
| o.O.+           |
|oo. = .          |
|=  o  . o        |
|+oo  ..=So       |
|*+...o+.. .      |
|+*oo+.+.         |
|=.=. =oo         |
|o*   E. .        |
+----[SHA256]-----+

root@ip-10-10-171-2:~# ssh-copy-id -i /root/.ssh/id_rsa.pub tryhackme@10.10.130.213
/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/root/.ssh/id_rsa.pub"
The authenticity of host '10.10.130.213 (10.10.130.213)' can't be established.
ECDSA key fingerprint is SHA256:aOSKDL5+YDo+ad1PweP6qD2K9uTHVhendteLGblBs08.
Are you sure you want to continue connecting (yes/no)? yes
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
tryhackme@10.10.130.213's password: 

Number of key(s) added: 1

Now try logging into the machine, with:   "ssh 'tryhackme@10.10.130.213'"
and check to make sure that only the key(s) you wanted were added.

root@ip-10-10-171-2:~# ssh tryhackme@10.10.130.213
Welcome to Ubuntu 20.04.2 LTS (GNU/Linux 5.4.0-1047-aws x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Wed Feb  7 03:01:44 UTC 2024

  System load:  0.15              Processes:             110
  Usage of /:   26.8% of 7.69GB   Users logged in:       0
  Memory usage: 25%               IPv4 address for eth0: 10.10.130.213
  Swap usage:   0%





The list of available updates is more than a week old.
To check for new updates run: sudo apt update

```

for the downloaded rsa file

```
root@ip-10-10-171-2:~# python3 /opt/john/ssh2john.py idrsa.id_rsa > id_rsa_hash.txt
root@ip-10-10-171-2:~# john --wordlist=/usr/share/wordlists/rockyou.txt id_rsa_hash.txt
Note: This format may emit false positives, so it will keep trying even after finding a
possible candidate.
Warning: detected hash type "SSH", but the string is also recognized as "ssh-opencl"
Use the "--format=ssh-opencl" option to force loading these as that type instead
Using default input encoding: UTF-8
Loaded 1 password hash (SSH [RSA/DSA/EC/OPENSSH (SSH private keys) 32/64])
Cost 1 (KDF/cipher [0=MD5/AES 1=MD5/3DES 2=Bcrypt/AES]) is 0 for all loaded hashes
Cost 2 (iteration count) is 1 for all loaded hashes
Will run 2 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
{redacted}        (idrsa.id_rsa)
1g 0:00:00:07 63.29% (ETA: 03:09:43) 0.1328g/s 1209Kp/s 1209Kc/s 1209KC/s choosehope..choose123
Session aborted
root@ip-10-10-171-2:~# 

```

## Task 11 PGP, GPG and AES

```
unzip gpg.zip 
Archive:  gpg.zip
 extracting: message.gpg             
  inflating: tryhackme.key           


sudo gpg --import tryhackme.key 
gpg: /root/.gnupg/trustdb.gpg: trustdb created
gpg: key FFA4B5252BAEB2E6: public key "TryHackMe (Example Key)" imported
gpg: key FFA4B5252BAEB2E6: secret key imported
gpg: Total number processed: 1
gpg:               imported: 1
gpg:       secret keys read: 1
gpg:   secret keys imported: 1


sudo gpg message.gpg 
gpg: WARNING: no command supplied.  Trying to guess what you mean ...
gpg: encrypted with 1024-bit RSA key, ID 2A0A5FDC5081B1C5, created 2020-06-30
      "TryHackMe (Example Key)"


cat message
You decrypted the file!
The secret word is {redacted}.
```