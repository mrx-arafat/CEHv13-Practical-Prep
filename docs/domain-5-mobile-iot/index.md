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
- APK installation and static analysis (decompiling, hardcoded secrets)
- Database extraction and analysis
- Steganography in photos
- Logcat analysis
- SQLite database queries
- IoT protocols — MQTT broker interaction
- Firmware analysis (binwalk) and IoT device discovery (Shodan)

**Duration:** 15-25 minutes per task  
**Difficulty:** Medium  
**Tools:** ADB, sqlite3, exiftool, steghide, apktool, jadx, mosquitto, binwalk

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

## 5.7 APK Static Analysis

### What It Does

An APK (an Android app's install file) is just a ZIP archive of code and resources. Pulling it apart lets you read the app's permissions, configuration, and — the usual exam goal — **hardcoded secrets** the developer left inside (API keys, passwords, URLs).

> **In plain English:** the app ships with its own source baked in. Unzip it, decompile it, and read the developer's secrets straight off the page.

### Get the APK off the Device

```bash
# Find the package, then its APK path
adb shell pm list packages | grep -i target
adb shell pm path com.example.app          # prints /data/app/.../base.apk
adb pull /data/app/com.example.app-1/base.apk ./app.apk
```

### Decompile & Read

```bash
# apktool — unpacks resources + AndroidManifest into readable form
apktool d app.apk -o app_src

# Read the manifest (permissions, components, exported activities)
cat app_src/AndroidManifest.xml

# jadx — decompile DEX bytecode back to readable Java
jadx -d app_out app.apk
jadx-gui app.apk                           # GUI to browse the code
```

### Hunt for Secrets

```bash
# Grep the decompiled source for hardcoded creds/keys/URLs
grep -riE "password|api[_-]?key|secret|token|http://|https://" app_src/ app_out/

# strings.xml often holds keys and endpoints
cat app_src/res/values/strings.xml
```

> **Exam tip:** the flag is usually a hardcoded API key, password, or hidden URL in `strings.xml` or a decompiled `.java` file. `grep -ri` for `key`/`password`/`token` finds it fast.

---

## 5.8 IoT — MQTT, Firmware & Device Discovery

### What It Does

IoT (Internet of Things — smart devices like cameras, sensors, locks) devices are weakly secured embedded computers. The exam tasks: talk to their messaging broker (MQTT), pull secrets out of their firmware, or find exposed devices on the internet.

### MQTT Broker Interaction

MQTT (a lightweight publish/subscribe protocol IoT devices chat over) brokers on port **1883** are usually unauthenticated. Subscribe to read everything; publish to inject commands.

```bash
# Subscribe to ALL topics on the broker (# = wildcard) — read live device data
mosquitto_sub -h 192.168.1.50 -t "#" -v

# Subscribe to a specific topic
mosquitto_sub -h 192.168.1.50 -t "home/sensors/temp"

# Publish (send a command to a device)
mosquitto_pub -h 192.168.1.50 -t "home/lock" -m "unlock"

# With credentials, if required
mosquitto_sub -h 192.168.1.50 -u admin -P password -t "#" -v
```

> **Exam tip:** `mosquitto_sub -t "#" -v` dumps every topic + message — the flag is usually sitting in one of them. (`-v` prints the topic name alongside each message.)

### Firmware Analysis

Firmware (the software baked into a device) often hides credentials, keys, and config in its filesystem.

```bash
# Inspect what's inside a firmware image
binwalk firmware.bin

# Extract the embedded filesystem
binwalk -e firmware.bin

# Then grep the extracted files for secrets
grep -riE "password|admin|root|key" _firmware.bin.extracted/

# Pull any readable strings (passwords, URLs, versions)
strings firmware.bin | grep -iE "password|http|admin"
```

### IoT Device Discovery (Shodan)

Shodan (a search engine for internet-connected devices) finds exposed IoT gear by banner, port, and product.

```
# Shodan search filters (web or CLI)
port:1883                      # Exposed MQTT brokers
product:"MQTT"                 # MQTT by product banner
"default password" port:23     # Telnet devices advertising defaults
webcam city:"London"           # Exposed webcams in a location
```

> **Exam tip:** IoT devices frequently ship with **default credentials** (`admin:admin`, `root:root`, `admin:password`). Always try these first against any IoT login.

---

## See Also

- **[Challenges](../challenges/all-challenges.html)**
- **[Domain 4: Cryptography](../domain-4-cryptography/)**
- **[Domain 6: Traffic Analysis](../domain-6-traffic-analysis/)**

---

**Next Step:** Practice [challenges](../challenges/all-challenges.html)
