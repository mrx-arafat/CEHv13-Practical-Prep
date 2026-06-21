---
layout: default
title: Quick Start Reference
nav_order: 6
---

# QUICK-START: During-Exam Reference Guide

**Use this guide on exam day for quick answers to common questions.**

---

## 6-Hour Time Allocation Strategy

```
HOUR 1 (0:00-1:00) - RECONNAISSANCE
├─ Start NMAP comprehensive scan: nmap -A -T4 -p- TARGET
├─ Identify live hosts
├─ Look for open ports: 22, 80, 443, 3306, 3389, 445
└─ Continue scanning while working other tasks

HOUR 2 (1:00-2:00) - ENUMERATION + QUICK WINS
├─ Enumerate SMB: enum4linux -a TARGET
├─ Enumerate SNMP if port 161 open
├─ Try default credentials on discovered services
├─ Extract easy files/data
└─ Scans should be complete by now

HOUR 3 (2:00-3:00) - CREDENTIAL CRACKING (START)
├─ Start Hydra for RDP/SSH if needed
├─ Start Hashcat for hash cracking
├─ Begin web vulnerability testing
├─ Run SQLMAP on identified targets
└─ BREAK around 2:30

HOUR 4 (3:00-4:00) - EXPLOITATION + CRYPTO
├─ Finish web testing (SQL injection, parameter tampering)
├─ Hash cracking should complete
├─ Steganography extraction
├─ PE file analysis with DIE
└─ Directory enumeration (Nikto/Robuster)

HOUR 5 (4:00-5:00) - ADVANCED ANALYSIS
├─ Android ADB extraction if challenge present
├─ WiFi cracking if needed
├─ PCAP analysis (Wireshark/tshark)
├─ RAT detection in traffic
└─ TTL fingerprinting analysis

HOUR 6 (5:00-6:00) - FINAL REVIEW + CLEANUP
├─ Review all answers
├─ Fix format issues
├─ Verify exact answer format
├─ Submit any last-minute answers
└─ Submit exam
```

---

## Most Critical Commands (Memorize These)

### Network Scanning
```bash
# Comprehensive scan
nmap -A -T4 -p- 192.168.1.5

# Service detection
nmap -sV 192.168.1.5

# SMB enumeration
enum4linux -a 192.168.1.5
nmap --script smb-os-discovery.nse -p445 192.168.1.5

# Find open ports quickly
nmap --open 192.168.1.5
```

### Credential Cracking
```bash
# RDP brute force
hydra -l Admin -P rockyou.txt rdp://192.168.1.50

# SSH cracking
hydra -l root -P rockyou.txt ssh://192.168.1.5

# Hash cracking (MD5)
hashcat -m 0 -a 0 hashfile rockyou.txt

# Hash type identification
hashid hash_value
```

### Web Exploitation
```bash
# SQL injection
sqlmap -u "http://192.168.1.5/page.php?id=1" --dbs

# Directory enumeration
nikto -h http://192.168.1.5
robuster dir -u http://192.168.1.5 -w /usr/share/wordlists/dirb/common.txt

# WordPress scanning
wpscan --url http://target.com --enumerate u
```

### Packet Analysis
```bash
# Filter IP in Wireshark
ip.src == 192.168.1.5

# Find DDoS sources
ip.dst == 172.22.10.10

# TTL analysis (Windows)
ip.ttl == 128

# Extract credentials (FTP)
ftp.request.command == 'USER' or ftp.request.command == 'PASS'

# MQTT topics
mqtt.msgtype == 3
```

### Cryptography
```bash
# Steganography extraction
steghide extract -sf image.jpg -p ""

# PE file analysis (DIE)
DIE filename.exe

# VeraCrypt mounting
veracrypt --mount /dev/sdb1 /mnt/encrypted --password=PASSWORD
```

### Android/Mobile
```bash
# Connect ADB
adb connect 192.168.1.100:5555

# List files
adb shell ls /sdcard/DCIM/Camera/

# Pull files
adb pull /sdcard/DCIM/Camera/image.jpg ./

# Gain root
adb root

# Extract database
adb pull /data/data/com.example.app/databases/app.db ./
```

### WiFi Cracking
```bash
# Find BSSID
aircrack-ng Credmapwifi.cap

# Crack password
aircrack-ng -w /usr/share/wordlists/rockyou.txt -b AA:BB:CC:DD:EE:FF Credmapwifi.cap
```

---

## Answer Format - CRITICAL!

**This is the #1 reason people fail. Format MUST match exactly.**

### Format Types You'll See

```
Format: AAAA              → Answer: "PQRS"
Format: NN.NN.NN.NN      → Answer: "192.168.1.5" (not 192.168.1.5:3306)
Format: NNNN:NN:NN       → Answer: "1234:AB:CD" (include colons)
Format: aaaaaaaaaaaaaa    → Answer: "abcdefghij" (exact case, no capitals)
Format: AaaAaa*aaaa       → Answer: "MyPass123" (capitals at start, asterisk wildcards)
Format: /aaa/aaaaa        → Answer: "/home/admin" (include forward slashes)
```

### Case Sensitivity

```
Q: "Find the password"
Format: AaaAaa*aaaa

CORRECT: "MyPassword123"
WRONG:   "mypassword123"
WRONG:   "MYPASSWORD123"
```

### Padding with Zeros

```
Format: NNNNN (5 digits)
Answer: 3306

CORRECT: "03306" (padded)
WRONG:   "3306" (only 4 digits)
```

### No Extra Spaces or Symbols

```
CORRECT: "admin"
WRONG:   " admin"
WRONG:   "admin "
WRONG:   "admin."
```

---

## When You're Stuck (Decision Tree)

```
I can't find the answer
│
├─ Is it a network/IP question?
│  └─ Check Wireshark filters (ip.src, ip.dst, ip.ttl)
│  └─ Use tshark for quick extraction
│
├─ Is it a credential?
│  └─ Check Hydra status
│  └─ Check rockyou.txt presence
│  └─ Try default credentials (admin/admin, root/root)
│
├─ Is it a hash?
│  └─ Use hashid to identify type
│  └─ Run Hashcat with correct mode (-m flag)
│  └─ Check John the Ripper if Hashcat slow
│
├─ Is it a file/data question?
│  └─ Check if steganography (use steghide)
│  └─ Check if encrypted (VeraCrypt, CryptoForge)
│  └─ Check for metadata (exiftool)
│
├─ Is it from a PCAP?
│  └─ Open in Wireshark
│  └─ Apply protocol filter (http, ftp, mqtt, ssh)
│  └─ Use tcpdump filter: "tcp port 80"
│
├─ Is it from Android?
│  └─ adb connect first
│  └─ adb root to access protected dirs
│  └─ Pull files then analyze
│
└─ Can't figure it out?
   └─ Move to next challenge (mark for return)
   └─ Come back in last 30 minutes
   └─ Make educated guess based on format
```

---

## Common Answer Formats by Domain

### Domain 1 (Network Scanning)
- IP addresses: `192.168.1.5`
- Computer names: `WORKSTATION1` or `domain-controller`
- Port numbers: `3306` or `445`
- Service versions: `5.7.17` or `7.4`

### Domain 2 (System Hacking)
- Passwords: `MyPassword123`
- Usernames: `Administrator` or `admin`
- Registry paths: `HKLM\Software\Microsoft\Windows\Run`
- TTL values: `128` (Windows) or `64` (Linux)

### Domain 3 (Web Hacking)
- URLs: `/admin/dashboard` or `http://target.com/login`
- SQL data: Table names, column values
- Directories: `/uploads`, `/admin`, `/config`
- Form parameters: `user`, `password`, `id`

### Domain 4 (Cryptography)
- Hashes: 32-char (MD5), 40-char (SHA1), 64-char (SHA256)
- Passwords from cracking: `SecurePassword99`
- File versions: `2.3.4.5`
- Compiler names: `Microsoft Visual C++`

### Domain 5 (Mobile & IoT)
- File paths: `/sdcard/DCIM/Camera/IMG_xxx.jpg`
- Database data: SQL query results
- Hidden messages: Steganography extracted text
- Device model: `SM-G920F` or similar

### Domain 6 (Traffic Analysis)
- Attacker IPs: `203.0.113.45`
- Victim IPs: `192.168.1.50`
- Ports: `4444`, `31337`
- Topics: `home/temperature`, `sensor/humidity`
- Domains: `malware-c2.com`

### Domain 7 (Wireless)
- WiFi passwords: `MyWiFiPass123`
- Last 4 chars: `Pass1` (from "MyWiFiPass1")
- BSSID: `AA:BB:CC:DD:EE:FF`

---

## Tools Cheat Sheet

| Tool | When to Use | Quick Command |
|------|-------------|---------------|
| **nmap** | Port scanning | `nmap -A -T4 -p- IP` |
| **enum4linux** | SMB enumeration | `enum4linux -a IP` |
| **hydra** | Credential cracking | `hydra -l user -P pass.txt rdp://IP` |
| **hashcat** | Hash cracking | `hashcat -m 0 -a 0 hash rockyou.txt` |
| **sqlmap** | SQL injection | `sqlmap -u URL --dbs` |
| **nikto** | Web scanning | `nikto -h http://IP` |
| **steghide** | Steganography extract | `steghide extract -sf image.jpg -p ""` |
| **Wireshark** | Packet analysis (GUI) | Open PCAP file, filter as needed |
| **tshark** | CLI packet analysis | `tshark -r capture.pcapng -Y filter` |
| **tcpdump** | Packet capture | `tcpdump -i eth0 -w capture.pcap` |
| **DIE** | PE file analysis | Load executable in GUI |
| **adb** | Android access | `adb connect IP:5555` |
| **aircrack-ng** | WiFi cracking | `aircrack-ng -w wordlist.txt capture.cap` |

---

## Last-Minute Tips

### If Running Out of Time (< 30 min left)

1. **Complete easy challenges first** - quick points
2. **Skip multi-part challenges** - too time-consuming
3. **Make educated guesses** on remaining
4. **Check answer formats** - common mistakes:
   - Case sensitivity
   - Padding with zeros
   - Including/excluding symbols

### If Tool Fails

1. **Hydra stuck?** → Try manual RDP connection
2. **Hashcat slow?** → Try John the Ripper
3. **SQLMAP not finding injection?** → Try manual testing in Burp
4. **ADB not connecting?** → Check USB debugging enabled
5. **Wireshark slow?** → Use tshark CLI instead

### If You Can't Find Something

1. **Network issue?** → Use nmap with -p- for all ports
2. **Credential?** → Check rockyou.txt is in right location
3. **File?** → Use find command: `find / -name "*.db" 2>/dev/null`
4. **In PCAP?** → Apply basic filter (http, ftp, mqtt)

---

## Exam Day Checklist (First 5 Minutes)

- [ ] Log in successfully
- [ ] Test Ctrl+F (search functionality)
- [ ] Verify all tools are available (nmap, hydra, hashcat, etc.)
- [ ] Check rockyou.txt exists: `/usr/share/wordlists/rockyou.txt`
- [ ] Check internet access (may need for online tools/APIs)
- [ ] Note down network settings (DHCP IP, gateway)
- [ ] Verify Wireshark, ADB, DIE installed if needed
- [ ] Read ALL 20 challenge descriptions first (5 min)
- [ ] Identify quick wins vs. time-consuming challenges

---

## Common Format Mistakes (Learn From These)

```
WRONG:  "192.168.1.5:3306"  CORRECT: "192.168.1.5"
WRONG:  "admin"             CORRECT: "Admin" (case matters!)
WRONG:  "3306"              CORRECT: "03306" (if format is NNNNN)
WRONG:  "MyPassword"        CORRECT: "MyPassword1" (include all characters)
WRONG:  "/admin "           CORRECT: "/admin" (no trailing spaces)
WRONG:  "C:\Windows"        CORRECT: "c:\windows" (if format is lowercase)
```

---

**Print this page or bookmark it for quick reference during exam.**

**Remember: You have internet access - use Ctrl+F to search this guide by keyword!**
