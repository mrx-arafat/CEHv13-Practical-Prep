---
layout: default
title: Domain 6 - Traffic Analysis & Sniffing
parent: Domains
nav_order: 6
description: "Wireshark packet analysis, Tcpdump capture, protocol inspection, and DDoS detection."
has_children: false
---

# Domain 6: Traffic Analysis & Sniffing

## Overview

Network traffic contains everything: passwords, commands, data exfiltration, attacker communications. Analyzing this traffic reveals the full attack picture.

> **In plain English:** Every conversation between computers travels as packets (small chunks of data). This domain is about reading a saved recording of those packets — a PCAP (a captured file of network traffic) — to find what an attacker did, like reviewing security-camera footage frame by frame.

**Key Topics:**
- Wireshark packet analysis and filtering
- Tcpdump command-line capture
- Tshark CLI analysis
- DDoS attack identification
- OS fingerprinting via TTL analysis
- MQTT IoT protocol analysis
- RAT detection in traffic

**Duration:** 15-30 minutes per task  
**Difficulty:** Medium to Hard  
**Tools:** Wireshark, tcpdump, tshark

---

## 6.1 Wireshark Basics

### What It Does

Wireshark is a tool that opens a packet capture (a saved `.pcap`/`.pcapng` recording of network traffic) and lets you filter, follow, and read every byte that crossed the network. In the CEH practical you're usually handed a capture file and asked a question: *what's the attacker IP, what password was sent, what file was stolen.* The whole game is **the right display filter (a search to hide everything except packets you care about) + Follow Stream (reassembling one conversation so you can read it)**.

> **Exam tip:** Don't scroll thousands of packets by hand. Filter to narrow the haystack, then right-click → **Follow → TCP Stream** to read the full conversation as plain text. 90% of answers fall out of this.

### Opening & Filtering

```
File → Open → Select .pcap or .pcapng file
```

> **Display filter vs capture filter:** the top bar (green = valid) is a *display* filter — `tcp.port == 80`. Capture filters (BPF, set before capture) use different syntax — `port 80`. Exam files are pre-captured, so you use display filters.

### Common Filters

```
http                    # HTTP traffic only
https or tls            # HTTPS traffic
ip.src == 192.168.1.5   # Source IP
ip.dst == 10.0.0.1      # Destination IP
ip.addr == 192.168.1.5  # Either src OR dst (most useful)
tcp.port == 80          # TCP port
udp.port == 53          # UDP port
tcp.flags.syn == 1 && tcp.flags.ack == 0   # SYN-only (scan/flood)
tcp contains "admin"    # Any TCP packet with this string
frame contains "password"   # Search whole frame for a string
mqtt                    # MQTT protocol
ftp                     # FTP traffic
dns                     # DNS queries
icmp                    # Ping / ICMP
```

> **Combine filters:** `&&` (and), `||` (or), `!` (not). Example: `http && ip.addr==10.0.0.5 && http.request.method=="POST"`.

### Finding Credentials — Workflow

Many old protocols send usernames and passwords as plain readable text (no encryption), so if you can find the login packet, you can simply read the password. These filters jump straight to those login moments.

```
http.request.method == "POST"          # Login attempts (creds in body)
http.authbasic                         # HTTP Basic Auth (base64 user:pass)
frame contains "password"              # Any protocol leaking the word
ftp.request.command == "USER" || ftp.request.command == "PASS"   # FTP login (cleartext)
telnet                                 # Telnet = fully cleartext
tcp.port == 3306                       # MySQL traffic
```

**Steps to pull a cleartext credential:**
1. Filter to the protocol (`http`, `ftp`, `telnet`).
2. Find the login packet (POST / `USER` / `PASS`).
3. Right-click → **Follow → TCP Stream** → read user + password in the conversation.
4. HTTP Basic Auth? The header is `Authorization: Basic <base64>` — decode it: `echo dXNlcjpwYXNz | base64 -d`.

### Follow Stream & Export

A single conversation is split across many packets. "Follow Stream" stitches them back together so you read it like one chat transcript, and "Export Objects" pulls out whole files (images, documents) that were transferred.

```
Right-click packet → Follow → TCP/UDP/HTTP Stream    # Read full conversation
File → Export Objects → HTTP                          # Pull files/images transferred over HTTP
Statistics → Protocol Hierarchy                       # What protocols are in this capture?
Statistics → Conversations                            # Who talked to who, byte/packet counts
```

> **Exam tip:** "What file did the attacker download/exfiltrate?" → **File → Export Objects → HTTP** lists and saves every transferred file.

---

## 6.2 Tcpdump - Command-Line Capture

### Basic Capture

```bash
tcpdump -i eth0                        # Capture on interface
tcpdump -i eth0 -w capture.pcap        # Save to file
tcpdump -i eth0 -c 100 -w output.pcap  # Capture 100 packets
```

### Filtering

```bash
# TCP only
tcpdump -i eth0 tcp

# Specific IP
tcpdump -i eth0 src 192.168.1.5
tcpdump -i eth0 dst 10.0.0.0/8

# Specific port
tcpdump -i eth0 port 80
tcpdump -i eth0 dst port 443

# Port range
tcpdump -i eth0 portrange 1000-2000

# Complex filter
tcpdump -i eth0 "tcp port 80 or tcp port 443"
```

### Display Options

```bash
# Hex and ASCII output (find passwords!)
tcpdump -i eth0 -X "tcp port 21"

# ASCII only
tcpdump -i eth0 -A "tcp port 80"

# Verbose
tcpdump -v -i eth0 tcp
```

### Reading PCAP Files

```bash
# Read capture
tcpdump -r capture.pcap

# Apply filter to file
tcpdump -r capture.pcap "tcp port 80"

# Extract to new file
tcpdump -r capture.pcap -w filtered.pcap "tcp and port 443"
```

---

## 6.3 Tshark - CLI Wireshark

### Basic Usage

```bash
tshark -r capture.pcapng                # Read PCAP
tshark -r capture.pcapng -Y "http"      # Apply filter
tshark -r capture.pcapng -V              # Verbose
```

### Extract Fields

```bash
# IP addresses
tshark -r capture.pcapng -T fields -e ip.src -e ip.dst

# HTTP requests
tshark -r capture.pcapng -Y "http.request" -T fields -e http.method -e http.host -e http.request.uri

# FTP credentials
tshark -r capture.pcapng -Y "ftp.request.command == 'USER' or ftp.request.command == 'PASS'" -T fields -e ftp.request.command -e ftp.request.arg

# DNS queries
tshark -r capture.pcapng -Y "dns.qry.name" -T fields -e dns.qry.name

# MQTT topics
tshark -r capture.pcapng -Y "mqtt.msgtype == 3" -T fields -e mqtt.topic -e mqtt.msg
```

### Output Formats

```bash
tshark -r capture.pcapng -T json > output.json      # JSON
tshark -r capture.pcapng -T csv > output.csv        # CSV
tshark -r capture.pcapng -T fields -e field1 -e field2  # Custom
```

---

## 6.4 DDoS / Flood Attack Analysis

### What It Does

A flood attack buries a target under a massive number of packets, sent from one machine (DoS) or many machines (DDoS). In the capture it looks like **one destination getting hammered by a huge volume of near-identical packets** — like thousands of people calling one phone line at once so no real caller can get through. Your job: name the attacker IP(s) and the attack type.

### Finding the Attacker IP

Wireshark's Statistics menu counts traffic for you, so instead of reading packets you just look for the IP that sent far more than everyone else — that's almost always the attacker.

```
Statistics → Conversations → sort by Packets (descending)   # Top talker = attacker
Statistics → Endpoints → sort by Packets                    # Single IP dominates = source
Filter: ip.dst == 172.22.10.10                              # Lock to the victim, see who floods it
```

> **DDoS vs DoS:** many source IPs all hitting one victim = **DDoS**. One source IP = **DoS**. Endpoints/Conversations counts tell you which.

### Identifying the Attack Type (Signatures)

Each flood type leaves a recognizable fingerprint (a "signature") in the packets. Match what you see against this table to name the attack.

| Attack | Filter / Signature | Tell |
|--------|--------------------|------|
| **SYN flood** | `tcp.flags.syn==1 && tcp.flags.ack==0` | Flood of SYNs, no completed handshakes |
| **ACK flood** | `tcp.flags.ack==1 && tcp.flags.syn==0` | Mass ACKs to a port with no session |
| **ICMP/Ping flood** | `icmp` | Huge volume of echo requests |
| **UDP flood** | `udp` | Many UDP packets to random/closed ports |
| **DNS amplification** | `dns && udp.length > 300` | Big DNS responses to a spoofed victim |
| **HTTP flood** | `http.request` | Flood of GET/POST from many IPs (L7) |

### Confirming Volume

```
Statistics → I/O Graph        # Spike in packets/sec = the attack window
Statistics → Protocol Hierarchy   # One protocol abnormally dominant (e.g. 99% TCP SYN)
```

---

## 6.5 OS Fingerprinting via TTL

### What It Does

Every operating system stamps a default starting **TTL** (Time To Live — a counter that limits how far a packet can travel) on its outgoing packets, and each router the packet passes through (a "hop") subtracts 1. So if you read the TTL and round UP to the nearest standard default, you learn the sender's OS without ever scanning them. It's like guessing a parcel's origin from how much postage has been used up along the way.

### TTL Values by OS

| OS | Default TTL |
|----|------------|
| Windows | 128 |
| Linux/Unix | 64 |
| macOS | 64 |
| Cisco | 255 |

### TTL Analysis

```
Observed TTL: 128 → Windows (no hops)
Observed TTL: 126 → Windows (2 hops: 128-2)
Observed TTL: 64  → Linux (no hops)
Observed TTL: 62  → Linux (2 hops: 64-2)
```

### In Wireshark

```
Filter: ip.ttl == 128          # All Windows packets
Filter: ip.ttl == 64           # All Linux packets
Filter: ip.src == 203.0.113.45 && ip.ttl  # Check specific attacker
```

---

## 6.6 MQTT IoT Protocol

### What It Does

MQTT is a lightweight messaging protocol that smart/IoT devices use. It works like a noticeboard: a sensor **PUBLISHES** a value to a labeled **topic** (e.g. `home/livingroom/temp`), and anything that **SUBSCRIBED** to that topic receives it. On port 1883 the traffic is **unencrypted**, so topics, values, and often the login credentials are all readable in cleartext. The exam asks: what topic, what value, what credentials.

### Common Ports

- 1883 = MQTT (unencrypted)
- 8883 = MQTT over TLS
- 9001 = MQTT over WebSockets

### MQTT Message Types

- 1 = CONNECT
- 3 = PUBLISH (actual data)
- 8 = SUBSCRIBE
- 10 = UNSUBSCRIBE

### Analyzing MQTT

```
Filter: mqtt                          # All MQTT traffic
Filter: mqtt.msgtype == 3             # PUBLISH messages only
Filter: mqtt.msgtype == 1             # CONNECT messages
Filter: mqtt.topic contains "temp"    # Topic search
```

### Extracting Topics

```bash
tshark -r capture.pcapng -Y "mqtt.msgtype == 3" -T fields -e mqtt.topic
```

> **Exam tip:** MQTT creds ride in the **CONNECT** packet (`mqtt.msgtype == 1`) — fields `mqtt.username` and `mqtt.passwd` in cleartext on port 1883.

---

## 6.7 RAT / C2 Detection in Traffic

### What It Does

A RAT (Remote Access Trojan — malware that gives an attacker remote control of a machine) makes the infected host **call out** to the attacker's Command & Control (C2) server and then receives commands back. In a capture it shows up as an internal host "beaconing" (checking in on a steady timer, like a spy phoning home every few seconds) to a suspicious external IP on an odd port.

### What to Look For

| Signal | How to Spot It |
|--------|----------------|
| **Beaconing** | Same internal→external pair, regular interval (e.g. every 30s). `Statistics → Conversations`, look for steady periodic small packets |
| **Odd port** | C2 on non-standard ports (4444 Metasploit, 1604, high random). `tcp.port == 4444` |
| **Reverse shell** | Outbound connection from server to attacker, then interactive text. Follow TCP Stream shows commands/output |
| **Suspicious external IP** | Internal host talking to an unknown public IP. `ip.dst == <external>` |
| **DNS tunneling** | Long, random-looking subdomains in DNS queries = data exfil. `dns.qry.name` with abnormal length |

### Filters

```
tcp.port == 4444                       # Common Metasploit/reverse-shell port
ip.dst == 203.0.113.66                 # Suspected C2 IP — see all beacons
tcp.flags.push == 1 && tcp.len > 0     # Interactive shell data (commands/output)
dns                                    # Then scan for long random subdomains (tunneling)
```

**Workflow:** `Statistics → Conversations` → spot the internal host beaconing to one external IP → filter that pair → **Follow TCP Stream** → read the C2 commands.

---

## See Also

- **[Challenges](../challenges/all-challenges.html)**
- **[Domain 2: System Hacking](../domain-2-system-hacking/)** - RAT detection
- **[QUICK-START.md](../QUICK-START.html)** - Common filters

---

**Next Step:** Practice [challenges](../challenges/all-challenges.html)
