---
layout: default
title: Domain 6 - Traffic Analysis & Sniffing
parent: Domains
nav_order: 6
description: "Wireshark packet analysis, Tcpdump capture, protocol inspection, and DDoS detection."
has_children: true
---

# Domain 6: Traffic Analysis & Sniffing

## Overview

Network traffic contains everything: passwords, commands, data exfiltration, attacker communications. Analyzing this traffic reveals the full attack picture.

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

### Opening & Filtering

```
File → Open → Select .pcap or .pcapng file
```

### Common Filters

```
http                    # HTTP traffic only
https or tls            # HTTPS traffic
ip.src == 192.168.1.5   # Source IP
ip.dst == 10.0.0.1      # Destination IP
tcp.port == 80          # TCP port
udp.port == 53          # UDP port
mqtt                    # MQTT protocol
ftp                     # FTP traffic
dns                     # DNS queries
```

### Finding Credentials

```
http.request.method == "POST"          # Login attempts
data contains "password"               # Password fields
ftp.request.command == 'USER'          # FTP login
tcp.port == 3306                       # MySQL traffic
```

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

## 6.4 DDoS Attack Analysis

### Finding Attacker IP

```
Filter: ip.dst == 172.22.10.10
Look for: IP appearing most frequently as source
```

### Statistics

```
Statistics → Endpoints
Shows all source/destination pairs with packet counts
Identify IP with highest count (attacker)
```

---

## 6.5 OS Fingerprinting via TTL

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

---

## See Also

- **[Commands Reference](commands.html)**
- **[Challenges](challenges.html)**
- **[Domain 2: System Hacking](../domain-2-system-hacking/)** - RAT detection
- **[QUICK-START.md](../QUICK-START.html)** - Common filters

---

**Next Step:** Review [commands.md](commands.html) and practice [challenges.md](challenges.html)
