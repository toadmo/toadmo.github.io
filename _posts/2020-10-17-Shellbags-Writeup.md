# Shellbags - 300 Points - Forensics

## MetaCTF 2020

## Solve

```
A password file is hidden in a folder maze, and we need to find it. 
Luckily, the user recently accessed the file, and we are given a UsrClass.dat file.
```
Trying to cat the files and using grep to look for keywords did not work due to the text being alphanumeric. Parsing through all of the outputted data was also impractical due to the sheer amount of text.

The hint was to use the UsrClass.dat file in looking for the correct file. Upon some searching, I found that shellbags are stored in UsrClass.dat files, which give a history of file access.

I found a tool--ShellBagsExplorer.exe--and plugged in the data file. ShellBagsExplorer only seems to work in Windows, it seems. After parsing the file, we find six accesses, the first of which leads to the correct file with the flag encoded in base64.

## Questions

I did not understand why the flag was in the first ShellBagsExplorer directory, which was the first directory path listed, and not the most recent access, given by the timestamps.

## Flag 

p3rh4ps_a_st1cky_n0te_w0u1d_h4v3_b33n_m0re_s3cure