---
layout: default
title: Domain 5 - Mobile & IoT Security
parent: Domains
nav_order: 5
description: "Android device access via ADB, steganography extraction, and mobile database analysis."
has_children: false
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

> **In plain English:** ADB is like a remote control for an Android phone from your computer — you can list files, copy them off, run apps, and read logs over USB or Wi-Fi.

### Connecting to Devices

This is how you get your computer talking to the phone. USB just works once plugged in; Wi-Fi needs the phone to "listen" on a port (5555) first, then you connect to its IP address.

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

Run these to confirm what device you're on and to look around its file system. `adb shell` runs a command directly on the phone instead of your computer.

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

### What It Does

Moves files between the phone and your computer. `pull` copies *from* the phone *to* you (think "download"); `push` copies *to* the phone (think "upload").

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

### What It Does

Apps store their data in SQLite database files (`.db`) inside `/data/data/`, a protected folder you need root (admin) access to read. You copy the `.db` file off the phone, then open it with `sqlite3` to read the tables.

> **Exam tip:** `/data/data/<package>/databases/` is where app data lives — pull the `.db` and `SELECT * FROM` the tables to find the flag.

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

### What It Does

Steganography (hiding a secret file *inside* a normal-looking image). `steghide` checks an image for hidden data and pulls it out — often the password is blank (`""`), so always try that first.

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

### What It Does

Logcat (Android's running system log) is a live stream of everything the phone and its apps print out. Careless apps sometimes log passwords or tokens — so you `grep` the stream for those words.

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

### What It Does

A grab-bag of handy ADB tricks: installing an APK (an Android app's install file), taking screenshots, faking taps/typing on the screen, and reading photo metadata with `exiftool` (a tool that shows hidden info like GPS location and timestamps baked into image files).

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

- **[Challenges](../challenges/all-challenges.html)**
- **[Domain 4: Cryptography](../domain-4-cryptography/)**
- **[Domain 6: Traffic Analysis](../domain-6-traffic-analysis/)**

---

**Next Step:** Practice [challenges](../challenges/all-challenges.html)
