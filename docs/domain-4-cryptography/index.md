---
layout: default
title: Domain 4 - Cryptography & Steganography
parent: Domains
nav_order: 4
description: "Hash cracking, steganography extraction, and PE file analysis with Hashcat and DIE."
has_children: true
---

# Domain 4: Cryptography & Steganography

## Overview

Encryption protects data. Hash cracking recovers passwords. Steganography hides data. These skills let you extract hidden information.

**Key Topics:**
- Hash identification & cracking (MD5, SHA1, SHA256, NTLM, bcrypt)
- File steganography extraction
- VeraCrypt volume cracking
- PE file analysis (DIE, CFF Explorer)
- Malware static analysis
- Registry analysis

**Duration:** 15-30 minutes per task  
**Difficulty:** Medium to Hard  
**Tools:** Hashcat, John, Steghide, DIE, CFF Explorer, VeraCrypt

---

## 4.1 Hash Cracking

### Identify Hash Type

```bash
hashid hash_value
hashid -e hash_value
```

### Hash Types Reference

| Type | Length | Mode (Hashcat) |
|------|--------|----------------|
| MD5 | 32 | 0 |
| SHA1 | 40 | 100 |
| SHA256 | 64 | 1400 |
| NTLM | 32 | 1000 |
| bcrypt | 60 | 3200 |

### Cracking Commands

```bash
# MD5
hashcat -m 0 -a 0 hashfile rockyou.txt

# SHA1
hashcat -m 100 -a 0 hashfile rockyou.txt

# SHA256
hashcat -m 1400 -a 0 hashfile rockyou.txt

# NTLM Windows
hashcat -m 1000 -a 0 hashfile rockyou.txt

# Using John
john hashes.txt --wordlist=rockyou.txt
john --show hashes.txt
```

---

## 4.2 Steganography Extraction

```bash
# Check if image contains hidden data
steghide info image.jpg

# Extract (prompts for passphrase)
steghide extract -sf image.jpg

# Extract with no passphrase
steghide extract -sf image.jpg -p ""

# Extract to specific file
steghide extract -sf image.jpg -xf output.txt -p ""

# Crack steganography
stegcracker image.jpg wordlist.txt
```

---

## 4.3 PE File Analysis

### Detect It Easy (DIE)

```bash
DIE filename.exe                        # GUI mode
detect-it-easy filename.exe             # Command line
```

**What to look for:**
- Compiler: Microsoft Visual C++, Borland Delphi, etc.
- Packer: UPX, Themida, etc. (suspicious)
- Entropy: Normal ~4.2, Packed ~7.5
- File Version: In properties tab

### CFF Explorer

GUI-based tool for:
- PE header inspection
- Import/Export tables
- Resource examination (version info)
- Hex editing

**Finding Version Info:**
1. Open CFF Explorer
2. Load executable
3. Left tree → Sections → .rsrc
4. Find "Version Information"
5. Read FILEVERSION field

### Command-Line Analysis

```bash
# Extract strings
strings Ghostware.exe | grep -i version
strings Ghostware.exe | grep -E "(http|ftp|cmd|exec)"

# Using objdump
objdump -h Ghostware.exe
objdump -x Ghostware.exe | grep -A 20 "Import"

# Using exiftool
exiftool Ghostware.exe
exiftool Ghostware.exe | grep -i version
```

---

## 4.4 VeraCrypt Volume Cracking

```bash
# Mount volume
veracrypt --mount /dev/sdb1 /mnt/encrypted --password=PASSWORD

# Or use GUI
# VeraCrypt → Select Device → Mount
```

---

## See Also

- **[Challenges](challenges.html)**
- **[Domain 2: System Hacking](../domain-2-system-hacking/)**

---

**Next Step:** Practice [challenges.md](challenges.html)
