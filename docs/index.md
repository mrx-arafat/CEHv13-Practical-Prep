---
layout: default
title: Home
nav_order: 1
description: "Complete open-book reference guide for CEH v13 Practical Exam with 20 challenges, 7 domains, and 85+ security tools."
has_children: false
---

# CEH v13 Practical Exam - Complete Documentation

**Version:** 2.0 - INTEGRATED WITH ENHANCEMENTS  
**Last Updated:** June 22, 2026  
**Status:** GitHub Pages Ready  
**Exam Duration:** 6 Hours  
**Passing Score:** 70% (14 out of 20 challenges)  
**Certificate Validity:** 3 Years

---

## How to Use This Documentation

This documentation is designed as an **open-book reference during your 6-hour practical exam**. It contains:

- **Domain-by-domain breakdown** of all 7 exam domains with command syntax
- **Real challenge examples** with step-by-step solutions
- **Tools reference section** for quick command lookup
- **Exam strategy** for time management and answer formatting
- **Cross-referenced navigation** to jump between related topics

**During the Exam:**
1. Keep this documentation accessible in a browser tab
2. Use Ctrl+F to search for specific tools or commands
3. Reference challenge examples for similar scenarios
4. Check "Critical Success Factors" when stuck on format

---

## Quick Navigation

### By Domain

1. **[Domain 1: Network Scanning & Enumeration](domain-1-network-scanning/)**
   - Host discovery, port scanning, service detection
   - SMB, SNMP, DNS enumeration
   - Commands, challenges, advanced topics

2. **[Domain 2: System Hacking & Exploitation](domain-2-system-hacking/)**
   - Remote Access Trojans (RAT), Metasploit, persistence
   - RDP cracking, credential harvesting
   - Commands, challenges, malware analysis

3. **[Domain 3: Web Server & Application Hacking](domain-3-web-hacking/)**
   - SQL injection, web directory enumeration
   - Parameter tampering, WordPress exploitation
   - Commands, challenges, DVWA workflows

4. **[Domain 4: Cryptography & Steganography](domain-4-cryptography/)**
   - Hash cracking (Hashcat, John), steganography
   - PE file analysis (DIE, CFF Explorer)
   - VeraCrypt volume cracking
   - Commands, challenges, analysis tools

5. **[Domain 5: Mobile & IoT Security](domain-5-mobile-iot/)**
   - Android device access via ADB
   - Steganography extraction, database analysis
   - Advanced workflows, photo extraction
   - Commands, challenges, security analysis

6. **[Domain 6: Traffic Analysis & Sniffing](domain-6-traffic-analysis/)**
   - Wireshark packet analysis, Tcpdump capture
   - Tshark CLI analysis, protocol inspection
   - DDoS detection, TTL fingerprinting, MQTT analysis
   - Commands, challenges, real-world scenarios

7. **[Domain 7: Wireless Network Cracking](domain-7-wireless/)**
   - WPA/WPA2 password cracking (Aircrack-ng)
   - Wireless attack workflow
   - Commands, challenges, monitoring mode

### Quick Reference

- **[QUICK-START.md](QUICK-START.html)** - How to use during exam (time management, answer format)
- **[TOOLS-COMPLETE-REFERENCE.md](tools-reference/TOOLS-COMPLETE-REFERENCE.html)** - All 85+ tools with commands
- **[Challenges](challenges/)** - All 20 challenge examples organized by domain

---

## Domain Overview

| Domain | Topics | Tools | Challenges |
|--------|--------|-------|------------|
| **1** | NMAP, enumeration, SMB, DNS | nmap, enum4linux, smbclient | 2 |
| **2** | RAT, RDP cracking, exploitation | Hydra, msfvenom, Metasploit | 2 |
| **3** | SQL injection, web scanning | SQLMAP, Nikto, Burp Suite | 2 |
| **4** | Hash cracking, steganography, PE analysis | Hashcat, Steghide, DIE, CFF | 3 |
| **5** | ADB, Android extraction, databases | ADB, sqlite3, exiftool | 2 |
| **6** | Wireshark, tcpdump, tshark, MQTT | Wireshark, tcpdump, tshark | 4 |
| **7** | WiFi cracking | Aircrack-ng, airmon-ng | 1 |

---

## Exam Strategy Overview

### Time Allocation (6 Hours = 360 minutes)

- **Hour 1-2 (Reconnaissance)**: Start NMAP scans, identify targets
- **Hour 2-3 (Quick Wins)**: Enumeration, default credentials, file access
- **Hour 3-4 (Credential Cracking)**: Hydra, Hashcat, web vulnerabilities
- **Hour 4-5 (Advanced)**: Web exploitation, crypto, PE analysis
- **Hour 5-6 (Finalization)**: Mobile, wireless, network analysis, cleanup

### Critical Success Factors

1. **Exact Answer Format** - Case-sensitive, padding matters
2. **Tool Mastery** - Know multiple methods for each task
3. **Time Management** - Skip long challenges, return later
4. **Documentation** - Write down findings for reference

---

## Key Competencies

✓ Network reconnaissance and mapping  
✓ Service enumeration and exploitation  
✓ Credential cracking (RDP, FTP, SSH, SMB, SQL)  
✓ Web application testing (SQL injection, parameter tampering)  
✓ Cryptography and file encryption/decryption  
✓ Mobile device access (Android via ADB)  
✓ Packet analysis and traffic inspection  
✓ Wireless network penetration testing  
✓ Remote Access Trojan (RAT) detection  
✓ Operating System fingerprinting via TTL analysis  
✓ MQTT IoT protocol analysis  
✓ PE file analysis and malware triage  

---

## Getting Started

1. **Read QUICK-START.md** for exam day strategy
2. **Review your weakest domain** - start with targeted study
3. **Practice tool commands** from each domain section
4. **Work through challenge examples** for real-world scenarios
5. **Use TOOLS-COMPLETE-REFERENCE.md** as during-exam lookup

---

## Documentation Structure

```
docs/
├── index.md (this file)
├── QUICK-START.md
├── domain-1-network-scanning/
│   ├── README.md
│   ├── commands.md
│   ├── challenges.md
│   └── advanced.md
├── domain-2-system-hacking/
│   ├── README.md
│   ├── commands.md
│   ├── challenges.md
│   └── advanced.md
├── [... similar for domains 3-7 ...]
├── tools-reference/
│   ├── TOOLS-COMPLETE-REFERENCE.md
│   ├── scanning.md
│   ├── cracking.md
│   ├── web-testing.md
│   └── analysis.md
└── challenges/
    ├── all-challenges.md
    ├── by-domain.md
    └── by-difficulty.md
```

---

## Last Updated

**Version 2.0 - Complete**  
**Date:** June 21, 2026  
**Verified:** All 7 domains, 30+ tools, 20 challenges  
**Status:** Ready for exam use

---

**Start with:** [QUICK-START.md](QUICK-START.html) or pick a domain above
