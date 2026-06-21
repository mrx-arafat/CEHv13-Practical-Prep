---
layout: default
title: Tools Reference
nav_order: 4
description: "Complete reference guide for 85+ security tools used in CEH v13 practical exam."
has_children: true
---

# Tools Reference

Complete documentation for all 85+ security tools used in the CEH v13 practical exam.

## Tools by Category

### Network Reconnaissance (9 tools)

| Tool | Purpose | Module | Difficulty |
|------|---------|--------|------------|
| **NMAP** | Port scanning & service detection | Domain 1 | Beginner |
| **Netdiscover** | ARP host discovery | Domain 1 | Beginner |
| **Enum4linux** | SMB enumeration | Domain 1 | Beginner |
| **SNMP Walk** | SNMP enumeration | Domain 1 | Intermediate |
| **Dig** | DNS enumeration & zone transfer | Domain 1 | Intermediate |
| **Nslookup** | DNS resolution & reverse lookup | Domain 1 | Beginner |
| **SMBClient** | SMB share access | Domain 1 | Beginner |
| **RPC Client** | RPC service enumeration | Domain 1 | Intermediate |
| **Netstat** | Network connection analysis | Domain 1, 6 | Beginner |

### Credential Cracking (5 tools)

| Tool | Purpose | Module | Speed |
|------|---------|--------|-------|
| **Hydra** | Online brute-force (RDP, SSH, FTP, etc.) | Domain 2 | Slow |
| **Hashcat** | Offline hash cracking (GPU-accelerated) | Domain 4 | Fast |
| **John the Ripper** | Hash cracking (CPU-based) | Domain 4 | Medium |
| **Hashid** | Hash type identification | Domain 4 | Instant |
| **Medusa** | Online brute-force (alternative to Hydra) | Domain 2 | Slow |

### Web Exploitation (6 tools)

| Tool | Purpose | Module | Type |
|------|---------|--------|------|
| **SQLMAP** | SQL injection automated testing | Domain 3 | Automated |
| **Nikto** | Web vulnerability scanner | Domain 3 | Automated |
| **Burp Suite** | Web proxy & manual testing | Domain 3 | Interactive |
| **Robuster** | Directory enumeration | Domain 3 | Automated |
| **WPScan** | WordPress vulnerability scanner | Domain 3 | Automated |
| **Curl** | HTTP CLI requests | Domain 3 | Manual |

### Cryptography & Analysis (8 tools)

| Tool | Purpose | Module | Type |
|------|---------|--------|------|
| **Steghide** | Steganography extraction | Domain 4, 5 | CLI |
| **Stegdetect** | Steganography detection | Domain 4 | CLI |
| **DIE (Detect It Easy)** | PE file analyzer | Domain 4 | GUI |
| **Strings** | Extract text from binaries | Domain 4 | CLI |
| **Exiftool** | Image metadata extraction | Domain 4, 5 | CLI |
| **VeraCrypt** | Encrypted volume mounting | Domain 4 | GUI |
| **Binwalk** | Firmware/binary analysis | Domain 4 | CLI |
| **Xxd** | Hex dump viewer | Domain 4 | CLI |

### Packet Analysis (4 tools)

| Tool | Purpose | Module | Type |
|------|---------|--------|------|
| **Wireshark** | GUI packet analyzer | Domain 6 | GUI |
| **Tshark** | CLI packet analyzer | Domain 6 | CLI |
| **Tcpdump** | Packet capture utility | Domain 6 | CLI |
| **Tcpflow** | TCP stream extraction | Domain 6 | CLI |

### Wireless Tools (4 tools)

| Tool | Purpose | Module | Type |
|------|---------|--------|------|
| **Aircrack-ng** | WPA/WPA2 password cracker | Domain 7 | CLI |
| **Airmon-ng** | Monitor mode manager | Domain 7 | CLI |
| **Airodump-ng** | Handshake capture | Domain 7 | CLI |
| **Aireplay-ng** | Deauth/replay attacks | Domain 7 | CLI |

### Mobile & IoT Tools (5 tools)

| Tool | Purpose | Module | Type |
|------|---------|--------|------|
| **ADB (Android Debug Bridge)** | Android device access | Domain 5 | CLI |
| **SQLite3** | Database query tool | Domain 5 | CLI |
| **Exiftool** | Image metadata (also cryptography) | Domain 5 | CLI |
| **Apktool** | APK decompiler | Domain 5 | CLI |
| **Androguard** | APK analyzer | Domain 5 | Python |

### System Tools (10+ tools)

| Tool | Purpose | Used In | Type |
|------|---------|---------|------|
| **File** | File type detection | All domains | CLI |
| **Grep** | Text pattern search | All domains | CLI |
| **Find** | File search | All domains | CLI |
| **Sed** | Stream editor | All domains | CLI |
| **Awk** | Text processing | All domains | CLI |
| **Cut** | Column extraction | All domains | CLI |
| **Sort** | Line sorting | All domains | CLI |
| **Uniq** | Duplicate removal | All domains | CLI |
| **Wc** | Word/line count | All domains | CLI |
| **Head/Tail** | Output beginning/end | All domains | CLI |

### Exploitation Frameworks (2 tools)

| Tool | Purpose | Module | Type |
|------|---------|--------|------|
| **Metasploit** | Exploitation framework | Domain 2 | GUI/CLI |
| **Msfvenom** | Payload generator | Domain 2 | CLI |

## Quick Tools List by Domain

### Domain 1: Network Scanning
✓ NMAP  
✓ Enum4linux  
✓ SNMP Walk  
✓ Dig / Nslookup  
✓ SMBClient  
✓ Netdiscover  

### Domain 2: System Hacking
✓ Hydra  
✓ Metasploit  
✓ Msfvenom  
✓ File / Strings  
✓ Nmap (service version)

### Domain 3: Web Hacking
✓ SQLMAP  
✓ Nikto  
✓ Burp Suite  
✓ Robuster  
✓ WPScan  
✓ Curl

### Domain 4: Cryptography
✓ Hashcat  
✓ John the Ripper  
✓ Hashid  
✓ Steghide  
✓ DIE  
✓ Exiftool  
✓ Strings  
✓ VeraCrypt

### Domain 5: Mobile & IoT
✓ ADB  
✓ SQLite3  
✓ Exiftool  
✓ Steghide  
✓ Strings

### Domain 6: Traffic Analysis
✓ Wireshark  
✓ Tshark  
✓ Tcpdump  
✓ Tcpflow  
✓ Grep / Awk

### Domain 7: Wireless
✓ Aircrack-ng  
✓ Airmon-ng  
✓ Airodump-ng  
✓ Aireplay-ng

## Tools by Installation Status

### Pre-Installed (Usually Available)
- NMAP, Wireshark, Tcpdump, GCC
- Grep, Find, Sed, Awk, Cut, Sort
- File, Strings, Xxd, Binwalk

### Commonly Available
- SQLMap, Nikto, Hydra, Hashcat
- ADB, SQLite3, Exiftool, Burp Suite
- Enum4linux, SNMP Walk, Dig, Curl

### May Need Installation
- Steghide, DIE, Stegdetect
- Aircrack-ng suite, Apktool
- VeraCrypt, Metasploit
- Androguard, Robuster

## Verification Commands

Before exam, verify tools are installed:

```bash
# Core tools
nmap --version
hashcat --version
hydra --version
sqlmap --version

# Analysis
wireshark --version
tshark --version
adb version

# Optional
aircrack-ng --version
steghide --version
exiftool --version
```

## Quick Reference by Task

| Task | Tool | Command |
|------|------|---------|
| Find open ports | NMAP | `nmap -p- IP` |
| Enumerate SMB | Enum4linux | `enum4linux -a IP` |
| Crack password | Hydra | `hydra -l user -P wordlist rdp://IP` |
| Crack hash | Hashcat | `hashcat -m 0 -a 0 hash wordlist` |
| Test SQL injection | SQLMAP | `sqlmap -u URL --dbs` |
| Scan web app | Nikto | `nikto -h http://IP` |
| Extract steganography | Steghide | `steghide extract -sf image.jpg -p ""` |
| Analyze packet capture | Wireshark | Open .pcap file, apply filters |
| Connect to Android | ADB | `adb connect IP:5555` |
| Crack WiFi | Aircrack-ng | `aircrack-ng -w wordlist.txt cap.cap` |

## Tools Documentation

Browse detailed documentation for each tool:

- **[Network Tools](network-tools.html)** - NMAP, Enum4linux, SNMP, DNS
- **[Cracking Tools](cracking-tools.html)** - Hydra, Hashcat, John the Ripper
- **[Web Tools](web-tools.html)** - SQLMAP, Nikto, Burp Suite, WPScan
- **[Crypto Tools](crypto-tools.html)** - Steghide, DIE, Exiftool, VeraCrypt
- **[Analysis Tools](analysis-tools.html)** - Wireshark, Tshark, Tcpdump
- **[Wireless Tools](wireless-tools.html)** - Aircrack-ng, Airmon-ng, Airodump-ng
- **[Mobile Tools](mobile-tools.html)** - ADB, SQLite3, Apktool

---

**Note:** Detailed tool documentation is in the **[Complete Tools Reference](TOOLS-COMPLETE-REFERENCE.html)** page.

Start with the [Quick Start Guide](../quick-start/tools-cheat.html) or pick a domain above.
