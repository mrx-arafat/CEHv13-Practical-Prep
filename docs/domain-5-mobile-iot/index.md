---
layout: default
title: Domain 5 - Mobile & IoT Security
parent: Domains
nav_order: 5
description: "Android device access via ADB, steganography extraction, and mobile database analysis."
has_children: true
---

# Domain 5: Mobile & IoT Security

## Overview

Mobile devices and IoT systems often store sensitive data. Learning to extract and analyze this data is crucial for penetration testing.

**Key Topics:**
- Android device access via ADB
- APK installation and analysis
- Database extraction and analysis
- Steganography in photos
- Logcat analysis
- Protected directory access
- SQLite database queries

**Duration:** 15-25 minutes per task  
**Difficulty:** Medium  
**Tools:** ADB, sqlite3, exiftool, steghide

---

## 5.1 Android Device Access via ADB

### What is ADB?

**ADB (Android Debug Bridge)** is a command-line tool to interact with Android devices for development, testing, and debugging.

### Connecting to Devices

```bash
# List devices
adb devices

# Connect via USB
adb devices  # Should list device

# Connect via Wi-Fi
adb tcpip 5555
adb connect 192.168.1.100:5555

# Verify connection
adb shell whoami
```

### Basic Commands

```bash
# System info
adb shell getprop ro.build.version.release  # Android version
adb shell getprop ro.product.model          # Device model

# List processes
adb shell ps

# List files
adb shell ls /sdcard/
adb shell ls /sdcard/DCIM/Camera/
adb shell ls -la /data/data/
```

---

## 5.2 File Operations

```bash
# Pull files to computer
adb pull /sdcard/DCIM/Camera/image.jpg ./
adb pull /sdcard/DCIM/Camera/ ./photos/

# Push files to device
adb push file.apk /sdcard/

# Download entire directory
adb pull /data/data/com.example.app/ ./app_backup/
```

---

## 5.3 Database Access

```bash
# Gain root
adb root
adb shell whoami  # Verify: root

# List app data
adb shell ls /data/data/ | grep -i app_name

# Extract database
adb pull /data/data/com.example.app/databases/app.db ./

# Query database
sqlite3 app.db
.tables
.schema users
SELECT * FROM users;
.exit
```

---

## 5.4 Steganography from Photos

```bash
# Check for hidden data
steghide info image.jpg

# Extract steganographic data
steghide extract -sf image.jpg -p ""

# Read extracted file
cat secret.txt
```

---

## 5.5 Logcat Analysis

```bash
# Real-time logs
adb logcat

# Filter by app
adb logcat | grep com.example.app

# Errors only
adb logcat *:E

# Clear logs
adb logcat -c

# Find sensitive data in logs
adb logcat | grep -iE "(password|token|secret|key|auth)"
```

---

## 5.6 Advanced Workflows

```bash
# Install APK
adb install application.apk

# Check if installed
adb shell pm list packages | grep application

# Screenshots
adb shell screencap /sdcard/screen.png
adb pull /sdcard/screen.png ./

# Simulate input
adb shell input text "password"
adb shell input tap 500 1200
adb shell input swipe 100 500 100 100

# Metadata extraction
exiftool image.jpg
exiftool image.jpg | grep -i "gps\|date"
```

---

## See Also

- **[Challenges](challenges.html)**
- **[Domain 4: Cryptography](../domain-4-cryptography/)**
- **[Domain 6: Traffic Analysis](../domain-6-traffic-analysis/)**

---

**Next Step:** Practice [challenges.md](challenges.html)
