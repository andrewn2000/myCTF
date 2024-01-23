---
layout: post
title:  "THM: Web Enumeration"
date:   2024-01-22 13:50:01 -0600
categories: TryHackMe
image: /assets/img/web-enumeration.jpeg
---
Learn the methodology of enumerating websites by using tools such as Gobuster, Nikto and WPScan

I really enjoyed this room since it has 3 enumerating tasks that I was able to complete by myself

# GOBUSTER
As the name implies, Gobuster is written in Go. Go is an open-source, low-level language (much like C or Rust) developed by a team at Google and other contributors. If you'd like to learn more about Go, visit the website linked above.
```
sudo apt install gobuster
```
It would be interesting to see if I can install gobuster on an ubuntu container and then use it to scan a website in another container.

I didn't know that you could pipe directly to the /etc/hosts file
```
echo "MACHINEIP webenum.thm" >> /etc/hosts
echo "MACHINEIP mysubdomain.webenum.thm" >> /etc/hosts
```
The gobuster commands, probably the most used one
```
gobuster dir -u http://MACHINEIP -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
```
If you want to search for .html, .php and .js files only
```
gobuster dir -u http://MACHINEIP/myfolder -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x.html,.css,.js
```

|Flag|Long Flag                 |Description
|----|--------------------------|------------------------------------------------------------|
|-c  |	--cookies               |	Cookies to use for requests
|-x  |	--extensions            |	File extension(s) to search for
|-H  |	--headers               |	Specify HTTP headers, -H 'Header1: val1' -H 'Header2: val2'
|-k  |	--no-tls-validation     |	Skip TLS certificate verification
|-n  |	--no-status             |	Don't print status codes
|-P  |	--password              |	Password for Basic Auth
|-s  |	--status-codes          |	Positive status codes
|-b  |	--status-codes-blacklist|	Negative status codes
|-U  |	--username              |	Username for Basic Auth

## The -k Flag
The -k flag is special because it has an important use during penetration tests and captures the flag events. In a capture the flag room on TryHackMe for example, if HTTPS is enabled, you will most likely encounter an invalid cert error like the one below

In instances like this, if you try to run Gobuster against this without the-k flag, it won't return anything and will most likely error out with something gross and will leave you sad. Don't worry though, easy fix! Just add the -k flag to your scan and it will bypass this invalid certification and continue scanning and deliver the goods! 

Note: This flag can be used with "dir" mode and "vhost" modes

# WPSCAN
First released in June 2011, WPScan has survived the tests of time and stood out as a tool that every pentester should have in their toolkits.

The WPScan framework is capable of enumerating & researching a few security vulnerability categories present in WordPress sites
```
sudo apt update && sudo apt install wpscan 
```
Basic scan for wordpress sites
```
wpscan --url http://MACHINEIP --enumerate t 
```
discover plugins
```
wpscan --url http://MACHINEIP --enumerate p 
```
enumerate users
```
wpscan --url http://MACHINEIP --enumerate u 

```
performing a password attack
```
wpscan –-url http://MACHINEIP –-passwords rockyou.txt –-usernames specific-user-name
```

# NIKTO
Initially released in 2001, Nikto has made leaps and bounds over the years and has proven to be a very popular vulnerability scanner due to being both open-source nature and feature-rich. Nikto is capable of performing an assessment on all types of webservers (and isn't application-specific such as WPScan.). Nikto can be used to discover possible vulnerabilities including:
```
sudo apt update && sudo apt install nikto
```
Basic scanning
```
nikto -h MACHINEIP

# nmap pipe
nmap -p80 172.16.0.0/24 -oG - | nikto -h -

#specific ports
nikto -h 10.10.10.1 -p 80,8000,8080

#plugins
nikto -h 10.10.10.1 -Plugin apacheuser

#results to html file
nikto -h http://ip_address -o report.html
```

> **&#9432;** I didn'd read carefully on this room as there was a link to [Blog](https://tryhackme.com/room/blog) which I should have completed after I did this room

