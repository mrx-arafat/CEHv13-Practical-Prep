---
layout: default
title: Domain 4 - Cryptography & Steganography
parent: Domains
nav_order: 4
description: "Hash cracking, steganography extraction, and PE file analysis with Hashcat and DIE."
has_children: false
---

# Domain 4: Cryptography & Steganography

## Overview

Encryption protects data. Hash cracking recovers passwords. Steganography hides data. These skills let you extract hidden information.

**Key Topics:**
- Hash identification & cracking (MD5, SHA1, SHA256, NTLM, bcrypt)
- File steganography extraction
- VeraCrypt volume mounting & file counting
- File/text decryption (AES Tool, BCTextEncoder, CrypTool)
- PE file analysis (DIE, CFF Explorer)
- Malware static analysis
- Registry analysis

**Duration:** 15-30 minutes per task  
**Difficulty:** Medium to Hard  
**Tools:** Hashcat, John, Steghide, DIE, CFF Explorer, VeraCrypt, AES Tool, BCTextEncoder, CrypTool

---

## 4.1 Hash Cracking

### What It Does

A hash (a one-way fingerprint of data, like a password) can't be reversed, so cracking means hashing millions of guesses until one matches. Think of it like trying every key on a giant keyring until one opens the lock.

### Identify Hash Type

Before cracking, you need to know *what kind* of hash it is so you pick the right setting. `hashid` looks at the hash's shape and tells you.

```bash
hashid hash_value      # Guess the hash type
hashid -e hash_value   # Also show the matching Hashcat mode
```

### Hash Types Reference

The "Length" (number of characters) is the easiest way to spot a hash type by eye. The "Mode" is the `-m` number you feed Hashcat.

| Type | Length | Mode (Hashcat) |
|------|--------|----------------|
| MD5 | 32 | 0 |
| SHA1 | 40 | 100 |
| SHA256 | 64 | 1400 |
| NTLM | 32 | 1000 |
| bcrypt | 60 | 3200 |

### Cracking Commands

`-m` picks the hash type (see the table above), `-a 0` means "wordlist attack," and `rockyou.txt` is the famous list of real leaked passwords to try.

> **Exam tip:** wrong `-m` number = nothing cracks. Always confirm the hash type first with `hashid`.

```bash
# MD5
hashcat -m 0 -a 0 hashfile rockyou.txt

# SHA1
hashcat -m 100 -a 0 hashfile rockyou.txt

# SHA256
hashcat -m 1400 -a 0 hashfile rockyou.txt

# NTLM Windows
hashcat -m 1000 -a 0 hashfile rockyou.txt

# Using John (John the Ripper, another cracker)
john hashes.txt --wordlist=rockyou.txt   # Crack the hashes
john --show hashes.txt                    # Show cracked passwords
```

---

## 4.2 Steganography Extraction

### What It Does

Steganography (hiding secret data inside an ordinary file like a photo) means the image looks normal but carries hidden text. `steghide` pulls that hidden payload back out, usually after you give it a passphrase.

```bash
# Check if image contains hidden data
steghide info image.jpg

# Extract (prompts for passphrase)
steghide extract -sf image.jpg

# Extract with no passphrase
steghide extract -sf image.jpg -p ""

# Extract to specific file
steghide extract -sf image.jpg -xf output.txt -p ""

# Crack the passphrase if you don't know it (tries each word in the list)
stegcracker image.jpg wordlist.txt
```

---

## 4.3 PE File Analysis

### What It Does

A PE file (Portable Executable — any Windows `.exe` or `.dll`) can be inspected without running it to learn who built it, whether it's hidden/packed, and its version. It's like reading the label on a package instead of opening it.

### Detect It Easy (DIE)

DIE scans an executable and tells you which compiler made it and whether it's "packed" (compressed/obfuscated to hide its real code).

```bash
DIE filename.exe                        # GUI mode
detect-it-easy filename.exe             # Command line
```

**What to look for:**
- Compiler: Microsoft Visual C++, Borland Delphi, etc.
- Packer: UPX, Themida, etc. (a packer hides the real code — suspicious)
- Entropy (a measure of randomness): Normal ~4.2, Packed ~7.5 (high randomness usually means packed/encrypted)
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

If you don't have the GUI tools, you can pull the same clues from the terminal — readable text, headers, and metadata baked into the file.

```bash
# Extract readable text from the binary (often hides version, URLs, commands)
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

## 4.4 VeraCrypt Volume Mounting

### What It Does

VeraCrypt mounts an encrypted volume/container as a normal drive once you supply the password. Think of the container as a locked safe — once unlocked, its contents show up like any other drive letter. CEH practical typically gives you a container file + password, then asks **how many files are inside**.

```bash
# Mount volume (CLI)
veracrypt --mount /dev/sdb1 /mnt/encrypted --password=PASSWORD

# Mount a container file
veracrypt --mount volume.hc /mnt/encrypted --password=test
```

**GUI workflow (CEH lab — answer the "count files" question):**
1. Open **VeraCrypt** → **Select File...** (or Select Device) → pick the container.
2. Click a free drive slot → **Mount**.
3. Enter password (e.g. `test`) → **OK**.
4. Open the newly mounted drive in Explorer.
5. **Count the files** inside → that's the answer (e.g. `6`).
6. **Dismount** when done.

> **Exam tip:** the answer is usually a file count or a specific filename inside the volume. Sort by type/name so you don't miscount hidden or system files.

---

## 4.5 File & Text Decryption Tools (CEH Lab GUI)

These are Windows GUI tools used in CEH iLabs encryption challenges. Each gives you a file/text + a password; you produce the plaintext (the original readable content).

> **In plain English:** all three do the same thing — take scrambled data plus the right password and hand back the readable original. They just handle different formats: AES Tool for `.aes` files, BCTextEncoder for blocks of encoded text, CrypTool for picking a specific algorithm.

### AES Tool — Decrypt a `.aes` File

> **Goal:** decrypt an `.aes` file. **Password:** `qwerty`

1. Launch **AES Tool** (Advanced Encryption Package / AES Crypt).
2. Load the `.aes` file (drag in or **Browse**).
3. Select **Decrypt**.
4. Enter password `qwerty`.
5. Run → output is the decrypted original file. Open it to read the answer.

> **CLI equivalent (aescrypt):** `aescrypt -d -p qwerty secret.aes`

### BCTextEncoder — Decode a Hidden IP / Text

> **Goal:** decrypt hidden text (e.g. an IP). **Password:** `Pa$$w0rd` → **Example output:** `10.10.10.31`

1. Open **BCTextEncoder**.
2. Paste the encoded block into the **Encoded text** pane.
3. Click **Decode**.
4. Enter password `Pa$$w0rd`.
5. The **Decoded** pane shows the plaintext — e.g. the hidden IP `10.10.10.31`.

### CrypTool — Decrypt a Ransomware File

> **Goal:** decrypt `cryt-128–06encr.hex`. **Algorithm:** Twofish. **Result hidden text:** `@!ph@|tE*t`

1. Open **CrypTool** → **File → Open** the `cryt-128–06encr.hex` file.
2. Menu **Crypt/Decrypt → Symmetric (modern) → Twofish**.
3. Choose **Decrypt**, supply the key as specified in the task.
4. Run → output reveals the hidden text `@!ph@|tE*t`.

> **Exam tip:** match the **algorithm** to what the challenge states (Twofish here, but could be AES/DES/RC4). Wrong algorithm = garbage output. The filename hint (`128`) usually = key/block size.

---

## See Also

- **[Challenges](../challenges/all-challenges.html)**
- **[Domain 2: System Hacking](../domain-2-system-hacking/)**

---

**Next Step:** Practice [challenges](../challenges/all-challenges.html)
