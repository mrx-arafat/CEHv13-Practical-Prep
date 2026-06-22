---
layout: default
title: Domain 7 - Wireless Network Cracking
parent: Domains
nav_order: 7
description: "WPA/WPA2 password cracking and wireless network penetration testing with Aircrack-ng."
has_children: false
---

# Domain 7: Wireless Network Cracking

## Overview

WiFi networks protect data with encryption (scrambling so outsiders can't read it). To test a network's security, you capture the moment a device connects, then try to guess the password offline. Learning this is essential for penetration testing.

> **In plain English:** You record a device joining the WiFi, then run a wordlist against that recording until the password pops out.

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

### What It Does

WPA/WPA2 is the standard WiFi security. You can't read the password from the air directly, but if you have a `.cap` file (a recorded capture of network traffic), you can run guesses against it until one matches.

### Finding Networks in Capture

First, peek inside the capture file to see which networks it contains.

```bash
# List every network found inside the capture file
aircrack-ng Credmapwifi.cap

# Shows all networks with their BSSID (the router's unique hardware address, like a MAC)
```

### Cracking Password

Point a wordlist at the capture. `aircrack-ng` tries each word as the password until one fits, like trying every key on a keyring until the lock opens.

```bash
# -w = the wordlist; -b targets one specific network by BSSID
aircrack-ng -w /usr/share/wordlists/rockyou.txt -b AA:BB:CC:DD:EE:FF Credmapwifi.cap

# No -b given, so it tries every network in the file
aircrack-ng -w wordlist.txt Credmapwifi.cap
```

> **Exam tip:** `rockyou.txt` is the go-to wordlist on Kali, found at `/usr/share/wordlists/`.

---

## 7.2 Wireless Attack Workflow (5 Steps)

### What It Does

This is the full live attack: switch your WiFi card into listening mode, watch the air for nearby networks, record a device joining your target, then crack the recording. Steps 1-3 capture, step 4 cracks, step 5 cleans up.

### Step 1: Find Target

Put your wireless card into monitor mode (a special mode that listens to all WiFi traffic around you, not just your own), then scan to see what's nearby.

```bash
sudo airmon-ng start wlan0
sudo airodump-ng wlan0mon
```

### Step 2: Create Monitor Interface

Step 1 already did this for you: your card `wlan0` now appears as `wlan0mon`, the listening version you'll use from here on.

```
# wlan0 is now in monitor mode (wlan0mon)
```

### Step 3: Capture Handshake

Lock onto one network's channel and BSSID and record. You're waiting to catch the handshake (the brief exchange when a device joins WiFi, which contains the encrypted password).

```bash
sudo airodump-ng -c [CHANNEL] --bssid [BSSID] -w capture wlan0mon
# Wait for the "WPA handshake" message in the upper right — that means you caught it
```

### Step 4: Crack Password

Now run the wordlist against your fresh recording, same idea as 7.1.

```bash
aircrack-ng -w rockyou.txt capture.cap
# Success looks like: KEY FOUND! [ PasswordHere ]
```

### Step 5: Cleanup

Put the card back to normal so regular WiFi works again.

```bash
airmon-ng stop wlan0mon
```

---

## See Also

- **[Challenges](../challenges/all-challenges.html)**
- **[QUICK-START.md](../QUICK-START.html)** - Time management

---

**Next Step:** Practice [challenges](../challenges/all-challenges.html)
