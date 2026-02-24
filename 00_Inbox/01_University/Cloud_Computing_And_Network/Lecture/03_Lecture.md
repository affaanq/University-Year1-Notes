# 🌐 FNCC Lecture 3 — OSI Middle Layers: Transport & Network Layers)

## 🔁 Recap — OSI & TCP/IP Models

```
┌─────────────────────────────────────────────────────────────────┐
│           OSI Model              │         TCP/IP Model         │
├──────────┬───────────────────────┼──────────────────────────────┤
│ Layer 7  │ Application           │                              │
│ Layer 6  │ Presentation          │  Application                 │
│ Layer 5  │ Session               │  (HTTP, FTP, DNS)            │
├──────────┼───────────────────────┼──────────────────────────────┤
│ Layer 4  │ Transport  ◄────────  │  Transport (TCP, UDP)        │
├──────────┼───────────────────────┼──────────────────────────────┤
│ Layer 3  │ Network    ◄────────  │  Internet (IPv4, IPv6)       │
├──────────┼───────────────────────┼──────────────────────────────┤
│ Layer 2  │ Data Link             │                              │
│ Layer 1  │ Physical              │  Network Access (Ethernet)   │
└──────────┴───────────────────────┴──────────────────────────────┘
```

> **This lecture focuses on Layer 4 (Transport) and Layer 3 (Network) — the "middle layers" of the OSI model.**

---

## 🚚 Transport Layer

### TCP vs UDP

```
┌──────────────────────────┬──────────────────────────────────────┐
│         TCP              │              UDP                     │
│  (Transmission Control   │  (User Datagram Protocol)            │
│   Protocol)              │                                      │ 
├──────────────────────────┼───────────────────────────────────── ┤
│ ✅ Reliable delivery     │ ❌ No reliability guarantees         │
│ ✅ Acknowledged delivery │ ❌ No acknowledgments                │
│ ✅ Error recovery        │ ✅ Minimal overhead                  │
│ ❌ More overhead         │ ✅ Faster transmission               │
│ ❌ Slower (more checks)  │ ❌ Packets may be lost               │
├──────────────────────────┼──────────────────────────────────────┤
│ 🔹 Use Cases:            │ 🔹 Use Cases:                       │
│  • Website browsing      │  • YouTube / Video streaming         │
│  • Online banking        │  • Live gaming                       │
│  • Email (SMTP)          │  • VoIP calls                        │
│  • File transfers (FTP)  │  • DNS lookups                       │
└──────────────────────────┴──────────────────────────────────────┘
```

> [!important]
TCP provides a **connection-oriented data delivery**.

> [!tip] **Memory Aid**
> **TCP** = "Trust, Check, Perfect" — reliable but slow
> **UDP** = "Unreliable, Dashing, Packetloss-tolerant" — fast but risky

---

### Three Basic Processes

The Transport Layer performs **three fundamental operations**:

```
┌─────────────────────────────────────────────────────────┐
│              Transport Layer Processes                  │
├─────────────────────┬───────────────────────────────────┤
│ 1. Message          │ Divides application data into     │
│    Segmentation     │ smaller manageable segments       │
├─────────────────────┼───────────────────────────────────┤
│ 2. Transfer &       │ Tracks individual communications  │
│    Reassemble       │ between source & destination apps │
├─────────────────────┼───────────────────────────────────┤
│ 3. Error Control    │ Detects & recovers lost/corrupted │
│                     │ packets via retransmission        │
└─────────────────────┴───────────────────────────────────┘
```

---

### Message Segmentation

**Purpose:** Break large data into smaller segments for manageability and reassemble them at the destination.

**Key Mechanisms:**
- **Sequence Numbers** → Track the order of segments
- **Acknowledgement Numbers** → Confirm which data has been received

#### Segmentation Example (TCP Sequence Flow)

```
  CLIENT                                SERVER
    │                                      │
    │──── 14 bytes (SEQ = 1001) ──────────►│
    │                                      │
    │     ⚠️ Only 1 byte arrives!         │
    │                                     │
    │◄──── ACK = 1002 ────────────────────│
    │     ("I got byte 1001,              │
    │      send me 1002 next")            │
    │                                      │
    │──── Retransmit missing data ────────►│
    │                                      │
    │◄──── ACK = 1015 ────────────────────│
    │     ("Got everything up to 1014,    │
    │      send me 1015 next")            │
    │                                      │
```

#### Segmentation: Advantages vs Disadvantages

```
┌──────────────────────────────────────────────────────────┐
│              ADVANTAGES              │  DISADVANTAGES    │
├──────────────────────────────────────┼───────────────────┤
│ ✅ Efficiency: Different comms can   │ ❌ Complexity:   │
│    be interleaved (multiplexing)     │    More complex   │
│                                      │    processing     │
│ ✅ Reliability: If one segment is    │                   │
│    lost, only that segment needs     │ ❌ Overhead:      │
│    retransmission (not whole msg)    │    Each segment   │
│                                      │    needs a header │
│                                      │    (seq#, ack#)   │
└──────────────────────────────────────┴───────────────────┘
```

---

### Port Numbers

> A **port number** is a unique identifier assigned to a network protocol (TCP/UDP) to facilitate communication between an application and a device.

**Analogy:** If the IP address is a **building address**, the port number is the **apartment/room number** inside that building.

```
┌─────────────────────────────────────────────────────────┐
│                  How Port Numbers Work                   │
│                                                         │
│   ┌──────────┐         ┌──────────────────────────┐     │
│   │ Incoming │         │     Device (Server)      │     │
│   │  Data    │────────►│                          │     │
│   │ Port: 80 │         │  Port 80  → Web Server   │     │
│   └──────────┘         │  Port 443 → HTTPS App    │     │
│                        │  Port 21  → FTP Service   │    │
│                        │  Port 22  → SSH Daemon    │     │
│                        │  Port 25  → Mail Server   │    │
│                        └──────────────────────────┘     │
└─────────────────────────────────────────────────────────┘
```

#### Common Port Numbers

| Port | Protocol | Description                        |
|------|----------|------------------------------------|
| 80   | HTTP     | Web browsing (unencrypted)         |
| 443  | HTTPS    | Secure web browsing (encrypted)    |
| 21   | FTP      | File transfer between computers    |
| 22   | SSH      | Secure remote administration       |
| 25   | SMTP     | Sending/receiving email            |

> [!note] **Try It Yourself!**
> Open Command Prompt (`Win + R` → `cmd`) and run:
> ```
> netstat -an
> ```
> This shows all active connections and their port numbers.

---

### Transfer & Reassemble

```
┌─────────────────────────────────────────────────────────────────────┐
│                    SOCKET = Complete Address                        │
│                                                                     │
│  ┌────────────────┐    ┌────────────────┐                           │
│  │  Source IP      │   │  Dest. IP      │                          │
│  │  192.168.1.10   │   │  93.184.216.34 │     ← Identifies HOST    │
│  └────────┬───────┘    └───────┬────────┘                           │
│           │                    │                                    │
│  ┌────────┴───────┐    ┌──────┴─────────┐                           │
│  │  Source Port    │    │  Dest. Port   │                          │
│  │  49152          │    │  443          │     ← Identifies APP     │
│  └────────────────┘    └────────────────┘                           │
│                                                                     │
│  Socket = 192.168.1.10:49152 ↔ 93.184.216.34:443                    │
└─────────────────────────────────────────────────────────────────────┘
```

**Encapsulation Flow:**

```
Application Data
      │
      ▼
┌──────────────────────────┐
│ SEGMENT (Layer 4)        │
│ ┌──────────┬───────────┐ │
│ │ Src Port │ Dest Port │ │
│ │  + Seq#  │  + Ack#   │ │
│ │          │  + Data   │ │
│ └──────────┴───────────┘ │
└────────────┬─────────────┘
             ▼
┌──────────────────────────┐
│ IP PACKET (Layer 3)      │
│ ┌──────────┬───────────┐ │
│ │ Src IP   │ Dest IP   │ │
│ │  + Segment (above)   │ │
│ └──────────┴───────────┘ │
└──────────────────────────┘
```

> **Key Insight:**
> - **IP address** = identifies the **device/host**
> - **Port number** = identifies the **application** running on that device

---

### Error Control

**Purpose:** Ensure reliable data delivery between hosts.

```
         Error Control Process (TCP)
         ═══════════════════════════

  SENDER                                RECEIVER
    │                                      │
    │── Packet 1 (SEQ=1) ────────────────►│
    │── Packet 2 (SEQ=2) ────────────────►│
    │── Packet 3 (SEQ=3) ───── ✖ LOST     │
    │── Packet 4 (SEQ=4) ────────────────►│
    │                                      │
    │◄──────── ACK 1 ─────────────────────│
    │◄──────── ACK 2 ─────────────────────│
    │          (No ACK 3 received!)        │
    │◄──────── ACK 4 ─────────────────────│
    │                                      │
    │  ⏰ Timeout! Retransmit Packet 3     │
    │                                      │
    │── Packet 3 (SEQ=3) [RETRANSMIT] ───►│
    │                                      │
    │◄──────── ACK 3 ─────────────────────│
    │          ✅ All packets received!     │
```

**Error Control Techniques:**

| Technique        | Description                                          |
|------------------|------------------------------------------------------|
| Acknowledgment   | Receiver confirms receipt of data                    |
| Sequencing       | Sender assigns sequence numbers to packets           |
| Retransmission   | Sender re-sends if no ACK within timeout             |
| ARQ              | Automatic Repeat Request — detect & re-request       |

> [!important] **Layer Responsibility Note**
> Different layers handle different types of errors:
> - **Transport Layer** → handles lost/corrupted packets
> - **Data Link Layer** → handles physical layer errors (bit errors)

---

## 🌍 Network Layer

### Overview of Responsibilities

```
┌───────────────────────────────────────────────────┐
│            Network Layer Functions                │
├───────────────────────────────────────────────────┤
│                                                   │
│   ┌───────────┐                                   │
│   │  Routing  │ ← Select path & direct packets    │
│   └─────┬─────┘                                   │
│         │                                         │
│   ���─────▼──────────────┐                        │
│   │ Path Determination │ ← OSPF / Dijkstra        │
│   └─────┬──────────────┘                          │
│         │                                         │
│   ┌─────▼──────────┐                              │
│   │  IP Addressing │ ← Unique device ID           │
│   └─────┬──────────┘                              │
│         │                                         │
│   ┌─────▼──────────┐                              │
│   │   IP Types     │ ← Public/Private,            │
│   │                │   Static/Dynamic,            │
│   │                │   IPv4/IPv6                  │
│   └────────────────┘                              │
└───────────────────────────────────────────────────┘
```

---

### Routing

> The Network Layer directs packets to a destination host on **another network** via **routers**.

```
   Source Host                                    Destination Host
  ┌──────────┐     ┌────────┐     ┌────────┐     ┌──────────┐
  │  PC A    │────►│Router 1│────►│Router 2│────►│  PC B    │
  │192.168   │     │        │     │        │     │10.0.0    │
  │.1.10     │     └────────┘     └────────┘     │.5        │
  └──────────┘        Hop 1          Hop 2       └──────────┘

  ◄─── Each router-to-router step = 1 HOP ───►
```

**Key Concepts:**
- **Router** → selects paths and directs packets toward destination
- **Routing** → the process of selecting and directing packets
- **Hop** → each segment of the route a packet takes

---

### Path Determination (OSPF)

> **OSPF** = Open Shortest Path First — a **link-state** routing protocol

```
┌─────────────────────────────────────────────────────────┐
│                  OSPF Process Flow                      │
│                                                         │
│  1. Each router maintains a database of                 │
│     neighbouring routers & link states                  │
│                 │                                       │
│                 ▼                                       │
│  2. Routers exchange link information                   │
│                 │                                       │
│                 ▼                                       │
│  3. Construct a full network topology map               │
│                 │                                       │
│                 ▼                                       │
│  4. Run Dijkstra's algorithm to compute                 │
│     Shortest Path Tree (SPT)                            │
│                 │                                       │
│                 ▼                                       │
│  5. Route packets along the shortest path               │
└─────────────────────────────────────────────────────────┘
```

**OSPF Dijkstra Example:**

```
        [Router A]
       /    |     \
     5/    2|      \7
     /      |       \
 [R-B]   [R-C]    [R-D]
    \      |       /
    3\    1|     /4
      \    |   /
      [Router E]

 Shortest path A → E:
   A → C (cost 2) → E (cost 1) = Total cost 3 ✅
   A → B (cost 5) → E (cost 3) = Total cost 8 ❌
   A → D (cost 7) → E (cost 4) = Total cost 11 ❌
```

**OSPF Benefits:**


| Feature         | Description                              |
|-----------------|------------------------------------------|
| Scalability     | Works well in large networks             |
| Load Balancing  | Distributes traffic across equal paths   |
| Fast Convergence| Quickly adapts to network changes        |
| Widely Used     | Standard in enterprise & internet routing|

---

### IP Address Structure

> An IP address has **two parts**: the **Network Prefix** and the **Host Part**.

```
         IP Address: 192.168.1.100
         ═══════════════════════════

    ┌──────────��───────────┬───────────┐
    │   Network Prefix     │ Host Part │
    │   192.168.1          │   .100    │
    └──────────┬───────────┴─────┬─────┘
               │                 │
               ▼                 ▼
      Used by routers      Used by last
      to forward packet    router to deliver
      to correct NETWORK   to correct DEVICE
```

---

### Subnet Mask & Routing

**Example: Requesting Facebook from 192.168.3.1**

```
  Your PC: 192.168.3.1
  Subnet Mask: 255.255.255.0

  ┌─────────┬─────────┬─────────┬─────────┐
  │   192   │   168   │    3    │    1    │
  ├─────────┼─────────┼─────────┼─────────┤
  │   255   │   255   │   255   │    0    │
  ├─────────┼─────────┼─────────┼─────────┤
  │◄── Network Address ──────►│◄─ Host ─►│
  │   (identifies network)    │(identifies│
  │                            │ device)  │
  └────────────────────────────┴──────────┘

  The mask 255.255.255.0 tells:
  • First 3 octets = Network (192.168.3.x)
  • Last octet = Host (.1 = your specific device)
```

---

### IP Address Types

```
┌──────────────────────────────────────────────────────────────────┐
│                     IP Address Classification                    │
├──────────────────────────┬───────────────────────────────────────┤
│       BY SCOPE           │       BY ASSIGNMENT                   │
├──────────────────────────┼───────────────────────────────────────┤
│                          │                                       │
│  ┌────────────┐          │  ┌──────────────┐                    │
│  │  PUBLIC IP │          │  │  STATIC IP   │                    │
│  │            │          │  │              │                    │
│  │ Assigned by│          │  │ Fixed, never │                    │
│  │ ISP, used  │          │  │ changes      │                    │
│  │ on internet│          │  │              │                    │
│  │            │          │  │ Use: web     │                    │
│  │ Globally   │          │  │ servers,     │                    │
│  │ unique     │          │  │ remote access│                    │
│  └────────────┘          │  └──────────────┘                    │
│                          │                                       │
│  ┌────────────┐          │  ┌──────────────┐                    │
│  │ PRIVATE IP │          │  │ DYNAMIC IP   │                    │
│  │            │          │  │              │                    │
│  │ Used within│          │  │ Assigned by  │                    │
│  │ local      │          │  │ DHCP, can    │                    │
│  │ network    │          │  │ change on    │                    │
│  │            │          │  │ reconnect    │                    │
│  │ e.g.       │          │  │              │                    │
│  │ 192.168.1.1│          │  │ Use: home    │                    │
│  │ 10.0.0.x   │          │  │ internet     │                    │
│  └────────────┘          │  └──────────────┘                    │
└──────────────────────────┴───────────────────────────────────────┘
```

**Comparison Table:**

| Type    | Scope    | Persistence | Use Case                    |
| ------- | -------- | ----------- | --------------------------- |
| Public  | Internet | Varies      | Communicate across internet |
| Private | LAN only | Varies      | Internal network devices    |
| Static  | Either   | Fixed       | Servers, remote access      |
| Dynamic | Either   | Changes     | Home connections, DHCP      |

---

### IPv4

> **IPv4** = Internet Protocol version 4 — the most widely used version

**IPv4 Address Format:** `X.X.X.X` where each X = 0–255 (8 bits × 4 = **32 bits**)

```
  Example: 192.168.1.100

  Binary: 11000000.10101000.00000001.01100100

  Total addresses: 2³² = ~4.3 billion
```

**IPv4 Packet Structure:**

```
┌─────────────────────────────────────────���──────────┐
│                 IPv4 PACKET                        │
├────────────────────────────────────────────────────┤
│                                                    │
│  ┌──────────────────────────────────────────────┐  │
│  │              IP HEADER                       │  │
│  │  • Source IP:  192.168.1.100                 │  │
│  │  • Dest IP:    193.60.251.42 (GCU server)    │  │
│  │  • TTL, Protocol, Version, etc.              │  │
│  └──────────────────────────────────────────────┘  │
│                                                    │
│  ┌──────────────────────────────────────────────┐  │
│  │              IP DATA (Payload)               │  │
│  │  Contains Layer 4 segment:                   │  │
│  │  • Port number                               │  │
│  │  • Sequence number                           │  │
│  │  • Acknowledgement number                    │  │
│  │  • Actual application data                   │  │
│  └──────────────────────────────────────────────┘  │
└────────────────────────────────────────────────────┘
```

---

### IPv6

> **IPv6** = Internet Protocol version 6 — the next generation

**IPv6 Address Format:** `X:X:X:X:X:X:X:X` (8 groups of 4 hex digits = **128 bits**)

```
  Example: 2001:0db8:85a3:0000:0000:8a2e:0370:7334

  Total addresses: 2¹²⁸ = ~340 undecillion (3.4 × 10³⁸)
```

**IPv4 vs IPv6 Comparison:**

```
┌──────────────────────┬──────────────────────┬──────────────────────┐
│     Feature          │       IPv4           │       IPv6           │
├──────────────────────┼──────────────────────┼──────────────────────┤
│ Address Size         │ 32-bit               │ 128-bit              │
│ Address Format       │ Dotted decimal       │ Hexadecimal colon    │
│                      │ 192.168.1.1          │ 2001:db8::1          │
│ Total Addresses      │ ~4.3 billion         │ ~340 undecillion     │
│ Header               │ Variable length      │ Fixed 40 bytes       │
│ NAT Required?        │ Yes (address scarcity)│ No (enough addresses)│
│ Security             │ Optional (IPSec)     │ Built-in (IPSec)     │
│ Packet Handling      │ Standard             │ Improved/Simplified  │
└──────────────────────┴──────────────────────┴──────────────────────┘
```

**IPv6 Key Advantages:**
- ✅ **Increased address space** — virtually unlimited addresses
- ✅ **Improved packet handling** — simplified, fixed-size header
- ✅ **Eliminates NAT** — every device gets a unique global address
- ✅ **Integrated security** — IPSec is mandatory, not optional

---

## 📝 Summary

### Complete Lecture Mind Map

```
              FNCC Lecture 3
              ═════════════
                    │
        ┌───────────┴───────────┐
        │                       │
   TRANSPORT                NETWORK
   LAYER (L4)               LAYER (L3)
        │                       │
   ┌────┴────┐             ┌────┴────────────┐
   │         │             │                  │
  TCP      UDP          Routing          IP Addressing
   │         │             │                  │
   │    (fast,         ┌───┴───┐         ┌────┴────┐
   │   unreliable)     │       │         │         │
   │                 Path    Hops     Structure  Types
   │              Determine            │           │
   │                │              ┌───┴──┐     ┌──┴──────┐
   │              OSPF             │      │     │         │
   │            (Dijkstra)     Network  Host   Public  Private
   │                           Prefix   Part   Static  Dynamic
   │
   ├── Segmentation
   │   (Seq#, Ack#)
   │
   ├── Transfer & Reassemble
   │   (Sockets = IP + Port)
   │
   └── Error Control
       (ACK, Retransmit, ARQ)
```

### Key Takeaways Checklist

- [ ] Understand TCP vs UDP trade-offs
- [ ] Know the three transport layer processes (Segmentation, Transfer, Error Control)
- [ ] Memorize common port numbers (80, 443, 21, 22, 25)
- [ ] Understand what a socket is (IP + Port)
- [ ] Know how OSPF uses Dijkstra for path determination
- [ ] Distinguish Network Prefix vs Host Part in IP addresses
- [ ] Differentiate Public/Private and Static/Dynamic IPs
- [ ] Compare IPv4 (32-bit) vs IPv6 (128-bit)


---

> **Tags:** #FNCC #Networking #OSI #TransportLayer #NetworkLayer #TCP #UDP #IPv4 #IPv6 #OSPF #Routing