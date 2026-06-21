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
Identifies which IP addresses have active machines on them. Scans a network range and returns all responding hosts.

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

### The Three Main Scan Types

**TCP Connect Scan (Full Connection)**
```bash
nmap -sT -v 192.168.1.5
```
- Complete three-way handshake
- Slowest but most reliable
- Leaves logs on target
- Use when you're not worried about stealth

**TCP SYN Scan (Half-Open / Stealthy)**
```bash
nmap -sS -v 192.168.1.5
```
- Sends SYN, waits for SYN-ACK, sends RST (no completion)
- Faster than full connect
- Less logging on older systems
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
Identifies specific software and version numbers running on open ports.

```bash
nmap -sV 192.168.1.5
```

**Breakdown:**
- `-sV` = Version detection
- Connects to each open port and fingerprints the service
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
Discovers Windows file shares, users, and domain information.

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
Queries network devices (routers, printers, servers) for configuration info.

SNMP is like a public information billboard for network devices. Many admins leave it with default credentials.

```bash
# Basic SNMP check
nmap -sU -p 161 --script snmp-processes.nse 192.168.1.5

# Using snmp-check tool
snmp-check 192.168.1.5

# Using onesixtyone for SNMP discovery
onesixtyone 192.168.1.0/24
```

**Common SNMP Community Strings (Passwords):**
- `public` (read-only, default)
- `private` (read-write)
- `community`
- `manager`

---

## 1.6 DNS Enumeration

### What It Does
Maps domain names to IP addresses and discovers subdomains.

```bash
# DNS lookup
nslookup target.com

# More detailed DNS lookup
dig target.com

# Zone transfer attempt (finds all DNS records)
dig @ns1.target.com target.com axfr

# Reverse DNS lookup (IP to hostname)
nslookup 192.168.1.5
```

**Online DNS Tools:**
- https://www.nslookup.io/ (browser-based, useful during exam)

---

## See Also

- **[Commands Reference](commands.html)** - All Domain 1 commands
- **[Challenges](challenges.html)** - Real exam challenge examples
- **[Back to Index](../index.html)**

---

**Next Step:** Review [commands.md](commands.html) to practice syntax, then work through [challenges.md](challenges.html) for real examples.
