---
layout: default
title: Domain 1 - Network Scanning & Enumeration
parent: Domains
nav_order: 1
description: "Host discovery, port scanning, service enumeration using NMAP, SMB, SNMP, and DNS."
has_children: true
---

# Domain 1: Network Scanning & Enumeration

## Overview

Network scanning is the foundation of every penetration test. You need to discover what's on the network, what ports are open, and what services are running. Think of it like reconnaissance: a burglar doesn't just kick down the door—they case the joint first, checking doors and windows.

**Key Topics:**
- Host discovery (finding live machines)
- Port scanning (TCP, UDP, SYN)
- Service version detection
- SMB enumeration (Windows resources)
- SNMP enumeration (device configuration)
- DNS enumeration (hostname/IP mapping)

**Duration:** ~15-20 minutes per scanning task  
**Difficulty:** Easy to Medium  
**Passing Rate:** High (most people pass scanning challenges)

---

## 1.1 Host Discovery - Finding Live Machines

### What It Does
Finds which IP addresses have a real machine answering on them. You point it at a whole range of addresses, and it tells you which ones are alive — so you don't waste time poking at empty addresses.

> **In plain English:** It's a roll call — you shout "who's there?" across the network and note who answers.

### NMAP Ping Scan
```bash
nmap -sn 192.168.1.0/24
```

**Breakdown:**
- `-sn` = Ping scan only (don't do port scanning)
- `192.168.1.0/24` = Check all 256 IPs in this range
- Shows which machines are alive
- Much faster than checking every single IP manually

**Alternative Methods:**
```bash
# ARP ping (works locally on network)
nmap -sP 10.10.55.0/24

# TCP SYN ping (useful if ICMP is blocked)
nmap -sS -P 10.10.55.0/24

# Using netdiscover tool
netdiscover -i eth0 -r 192.168.1.0/24
```

**Expected Output:**
```
Starting Nmap...
Nmap scan report for 192.168.1.1
Host is up (0.0023s latency).
Nmap scan report for 192.168.1.5
Host is up (0.0045s latency).
```

**Why This Matters:** Helps you quickly identify active targets in large networks without wasting time on dead IPs.

---

## 1.2 Port Scanning - What Services Are Running

### What It Does
A port (a numbered "door" a service listens on) is how machines offer things like web pages or file sharing. Port scanning knocks on those doors to see which are open and what's behind them.

> **In plain English:** Host discovery found which houses have people home; port scanning checks which doors and windows are unlocked.

### The Three Main Scan Types

**TCP Connect Scan (Full Connection)**
```bash
nmap -sT -v 192.168.1.5
```
- Completes the full three-way handshake (the normal SYN → SYN-ACK → ACK "hello" two machines exchange to start a TCP connection)
- Slowest but most reliable
- Leaves logs on target (the target sees a real connection)
- Use when you're not worried about stealth

**TCP SYN Scan (Half-Open / Stealthy)**
```bash
nmap -sS -v 192.168.1.5
```
- Sends SYN, waits for SYN-ACK, then sends RST to drop it before the handshake finishes — so the connection is never fully made
- Faster than full connect
- Less logging on older systems (the half-open knock often isn't recorded)
- Preferred for most scans

**UDP Scan**
```bash
nmap -sU -v 192.168.1.5
```
- Tests UDP ports (SNMP, DNS, DHCP)
- Slower and less reliable
- Use when targeting specific UDP services

### Common Port Scanning Commands

```bash
# Scan specific ports on target
nmap -p 22,80,443 192.168.1.5

# Scan ports 1-1000 on target
nmap -p 1-1000 192.168.1.5

# Scan ALL 65535 ports (thorough but slow)
nmap -p- 192.168.1.5

# Fast scan (only top 100 ports)
nmap -F 192.168.1.5

# Aggressive scan with OS detection
nmap -A -T4 192.168.1.5

# Show only open ports
nmap --open 192.168.1.5
```

### Understanding Port States

Every port nmap reports comes back in one of these states. This tells you whether there's something worth attacking.

| State | Meaning | Action |
|-------|---------|--------|
| **Open** | Service is listening and accepting connections | Exploit or enumerate |
| **Closed** | Port responded but no service is listening | Usually safe to ignore |
| **Filtered** | Firewall is blocking the port | May indicate protected service |
| **Unfiltered** | Port is accessible but unclear if service exists | Investigate further |

> **EXAM MISTAKE:** Don't skip ports because "standard" tools seem comprehensive. Always scan all 65,535 ports on key targets—MariaDB might be on 3307, not 3306.

---

## 1.3 Service Version Detection

### What It Does
A plain port scan tells you a door is open; version detection tells you *exactly* which software and version is behind it (e.g. "Apache 2.4.6", not just "a web server").

```bash
nmap -sV 192.168.1.5
```

**Breakdown:**
- `-sV` = Version detection
- Connects to each open port and fingerprints the service (compares the responses against a database to identify the exact software)
- Returns software name and version
- Takes longer than basic port scan

**Example Output:**
```
PORT    STATE SERVICE VERSION
22/tcp  open  ssh     OpenSSH 7.4 (protocol 2.0)
80/tcp  open  http    Apache httpd 2.4.6
443/tcp open  https   Apache httpd 2.4.6
3306/tcp open mysql  MySQL 5.7.17
```

**Why This Matters:** Version numbers let you know if the software has known exploits. Old versions = more vulnerabilities.

### Comprehensive Scanning

The "gold standard" for initial reconnaissance:

```bash
nmap -A -T4 -p- 192.168.1.5
```

**Breakdown:**
- `-A` = All: enables OS detection, version detection, script scanning
- `-T4` = Timing template (faster, less stealthy)
- `-p-` = All 65,535 ports
- Returns comprehensive information about the target

---

## 1.4 SMB Enumeration - Windows Shared Resources

### What It Does
SMB (Server Message Block — the protocol Windows uses to share files, printers, and folders) often hands over user names, shared folders, and domain details, sometimes without any password at all. This step squeezes that info out of it.

> **In plain English:** It's like reading the names on every mailbox and which doors are unlocked in a Windows apartment building.

#### NMAP SMB Scripts

```bash
# Discover OS, domain, workgroup
nmap --script smb-os-discovery.nse -p445 192.168.1.5
```

**Expected Output:**
```
PORT    STATE SERVICE
445/tcp open  microsoft-ds
| smb-os-discovery:
|   OS: Windows 7 (Build 7601)
|   Computer name: WORKSTATION1
|   Domain: ACME
|   Workgroup: WORKGROUP
```

```bash
# Enumerate users on Windows system
nmap --script smb-enum-users.nse -p445 192.168.1.5

# Enumerate shares available
nmap -p445 --script=smb-enum-shares.nse 192.168.1.5
```

**Expected Output:**
```
HOST|445|smb-enum-shares:
  Share        Type  Comment
  ----         ----  -------
  C$           STYPE_DISKTREE  Default share
  IPC$         STYPE_IPC       Remote IPC
  Users        STYPE_DISKTREE  User files
  Documents    STYPE_DISKTREE  Shared documents
```

#### Using SMBClient for Access

```bash
# List shares on a system
smbclient -L 192.168.1.5

# Access a share
smbclient //192.168.1.5/Documents

# Download files
smbget -R smb://192.168.1.5/Documents
```

#### Enum4Linux - Comprehensive SMB Enumeration

```bash
# Get all info (-a for all)
enum4linux -a 192.168.1.5

# Get users only (-U)
enum4linux -U 192.168.1.5

# Get shares only (-S)
enum4linux -S 192.168.1.5

# Get groups (-G)
enum4linux -G 192.168.1.5

# Get password policy (-P)
enum4linux -P 192.168.1.5

# With credentials
enum4linux -u admin -p password123 -a 192.168.1.5
```

**Why This Matters:** SMB often reveals user names, domain structure, and accessible file shares without any authentication.

---

## 1.5 SNMP Enumeration

### What It Does
SNMP (Simple Network Management Protocol — how admins remotely monitor and manage devices) can be asked to dump a device's configuration, running processes, and more.

SNMP is like a public information billboard for network devices: routers, printers, and servers. Many admins leave it with default credentials, so you can often just walk up and read it.

```bash
# Basic SNMP check
nmap -sU -p 161 --script snmp-processes.nse 192.168.1.5

# Using snmp-check tool
snmp-check 192.168.1.5

# Using onesixtyone for SNMP discovery
onesixtyone 192.168.1.0/24
```

**Common SNMP Community Strings (basically the password SNMP uses — guess these first):**
- `public` (read-only, default)
- `private` (read-write)
- `community`
- `manager`

---

## 1.6 DNS Enumeration

### What It Does
DNS (Domain Name System — the internet's phone book that turns names like `target.com` into IP addresses) can be queried to map names to IPs and uncover hidden subdomains.

```bash
# DNS lookup
nslookup target.com

# More detailed DNS lookup
dig target.com

# Zone transfer attempt — if misconfigured, dumps the server's ENTIRE list of DNS records at once
dig @ns1.target.com target.com axfr

# Reverse DNS lookup (IP to hostname)
nslookup 192.168.1.5
```

**Online DNS Tools:**
- https://www.nslookup.io/ (browser-based, useful during exam)

---

## 1.7 Active Directory - Domain Controller Discovery

### What It Does

In a Windows network, the **Domain Controller (DC)** is the crown jewel — it runs Active Directory and authenticates every user. You find it by its signature service ports. A host with **LDAP (389), Kerberos (88), and Global Catalog (3268)** open is almost certainly a DC.

### DC Service Ports

| Port | Service | Why it marks a DC |
|------|---------|-------------------|
| 88 | Kerberos | AD authentication |
| 389 | LDAP | Directory queries |
| 636 | LDAPS | LDAP over TLS |
| 3268 | Global Catalog | Forest-wide LDAP |
| 3269 | Global Catalog (TLS) | GC over TLS |
| 53 | DNS | DC usually runs AD DNS |

### Find the Domain Controller

```bash
# Scan the subnet for DC ports (NO spaces in the port list!)
nmap -p 88,389,636,3268 192.168.0.0/24

# Host that answers on all of these = the DC
```

> **⚠️ Syntax:** nmap port lists must have **no spaces** — `-p 88,389,636,3268`, not `-p 88, 389, ...`. A space ends the argument and nmap errors or ignores the rest.

### Get NetBIOS Name and FQDN

Once you've found the DC's IP, pull its hostname/domain with default scripts:

```bash
# Default scripts on top ports — reveals NetBIOS name + FQDN
nmap -sC 192.168.0.X --top-ports=20

# More targeted: AD/LDAP info scripts
nmap -p 389 --script ldap-rootdse 192.168.0.X     # Domain naming context, FQDN
nmap -p 88  --script krb5-enum-users 192.168.0.X  # Kerberos realm
nmap -sU -p 137 --script nbstat 192.168.0.X       # NetBIOS name table
```

**Where the answers show up:**
- **FQDN / domain** → `ldap-rootdse` output (`defaultNamingContext: DC=corp,DC=local`) or the SSL cert CN in `-sC`.
- **NetBIOS name** → `nbstat` script or SMB output (e.g. `DC01`).

> **Exam tip:** the question is usually "what's the domain FQDN" or "the DC's NetBIOS name." `nmap -sC` against the DC plus `--script ldap-rootdse` answers both.

---

## See Also

- **[Commands Reference](commands.html)** - All Domain 1 commands
- **[Challenges](../challenges/all-challenges.html)** - Real exam challenge examples
- **[Back to Index](../index.html)**

---

**Next Step:** Review [commands](commands.html) to practice syntax, then work through [challenges](../challenges/all-challenges.html) for real examples.
