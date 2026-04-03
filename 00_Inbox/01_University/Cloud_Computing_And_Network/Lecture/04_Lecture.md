
# 📚 Comprehensive In-Depth Notes: OSI & TCP/IP Models — Data Link Layer & Physical Layer

## 🏗️ OSI MODEL QUICK REFERENCE (Layers 1–7)

![[Pasted image 20260223111107.png]]

---

# 🔷 PART 1: DATA LINK LAYER (Layer 2)

## 1.1 Definition & Core Responsibility

The Data Link Layer is **responsible for the exchange of frames between nodes over a physical network media**. It:
- Allows **upper layers to access the media**
- **Controls how data is placed and received** on the media
- Works between **directly connected nodes** (node-to-node delivery)

> ⚠️ **KEY EXAM POINT**: The Data Link Layer deals with **frames** (its PDU), not packets or segments.

---

## 1.2 Two Sub-Layers of the Data Link Layer

```text name=Data_Link_Sublayers_Diagram.txt
┌──────────────────────────────────────────────────────────────┐
│                    DATA LINK LAYER (Layer 2)                 │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│   ┌──────────────────────────────────────────────────────┐   │
│   │         LLC (Logical Link Control)                   │   │
│   │         ─── UPPER Sublayer ───                       │   │
│   │                                                      │   │
│   │  • Defined by SOFTWARE processes                     │   │
│   │  • Provides services to Network Layer protocols      │   │
│   │  • Like a SORTING OFFICE:                            │   │
│   │    → Ensures each package has proper labels          │   │
│   │    → Routes correctly (protocols like IPv4/IPv6)     │   │
│   │  • Interfaces between Layer 3 (Network) and MAC     │    │
│   └──────────────────────────────────────────────────────┘   │
│                          ▼                                   │
│   ┌──────────────────────────────────────────────────────┐   │
│   │         MAC (Media Access Control)                   │   │
│   │         ─── LOWER Sublayer ───                       │   │
│   │                                                      │   │
│   │  • Defined by HARDWARE processes                     │   │
│   │  • Provides Data Link Layer ADDRESSING               │   │
│   │  • Delimiting of data per physical signalling        │   │
│   │  • Like a DELIVERY DRIVER:                           │   │
│   │    → Delivers packages to specific street            │   │
│   │      addresses (MAC addresses)                       │   │
│   └──────────────────────────────────────────────────────┘   │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

### 🔑 LLC (Logical Link Control) — Upper Sublayer
- **Software-based** processes
- Provides services to **Network Layer protocols**
- **Analogy**: Like a **sorting office** — ensures each package is prepared with proper labels and routed correctly
- Handles protocols like **IPv4** or **IPv6**

### 🔑 MAC (Media Access Control) — Lower Sublayer
- **Hardware-based** processes
- Provides **Data Link Layer addressing** (MAC addresses)
- **Delimiting of data** according to physical signalling requirements
- **Analogy**: Like a **delivery driver** who delivers packages to specific street addresses (MAC addresses)

> ⚠️ **KEY EXAM POINT**: LLC = Software/Upper; MAC = Hardware/Lower. Know the analogies!

---

## 1.3 MAC — The Switch

A **switch** is the key device operating at the Data Link Layer:

| Feature | Detail |
|---|---|
| **Function** | Connects end devices to a central intermediate device on most Ethernet networks |
| **Operation** | Performs **switching and filtering** based **only** on the **MAC address** |
| **Key Mechanism** | Builds a **MAC address table** that it uses to make **forwarding decisions** |
![[Pasted image 20260223111154.png]]

> ⚠️ **KEY EXAM POINT**: Switches work at **Layer 2** and use **MAC addresses** (not IP addresses) to make forwarding decisions.

---

## 1.4 MAC — CSMA (Carrier Sense Multiple Access)

### What is CSMA?
A process used to **detect if the media is carrying a signal** before transmitting:

```text name=CSMA_Process_Diagram.txt
┌──────────────────────────────────────────────────────┐
│               CSMA PROCESS FLOW                      │
│                                                      │
│   Device wants to transmit                           │
│          │                                           │
│          ▼                                           │
│   ┌─────────────────┐                                │
│   │ LISTEN to media  │ ◄── "Carrier Sense"           │
│   │ (Is it busy?)    │                               │
│   └────────┬────────┘                                │
│            │                                         │
│      ┌─────┴─────┐                                   │
│      ▼           ▼                                   │
│  ┌────────┐  ┌──────────┐                            │
│  │NO signal│  │Signal    │                           │
│  │detected │  │detected  │                           │
│  └────┬───┘  └────┬─────┘                            │
│       │           │                                  │
│       ▼           ▼                                  │
│  TRANSMIT     WAIT &                                 │
│  data         RETRY                                  │
│                                                      │
│  If TWO devices transmit simultaneously:             │
│  → COLLISION may occur                               │
│  → Use CSMA/CD or CSMA/CA to resolve                 │
└──────────────────────────────────────────────────────┘
```

### Two Methods of Resolving Contention:

| Method | Full Name | Usage | How It Works |
|---|---|---|---|
| **CSMA/CD** | Collision **Detection** | **Wired** networks (Ethernet) | Detects collision **after** it happens, stops, waits random time, retries |
| **CSMA/CA** | Collision **Avoidance** | **Wireless** networks (Wi-Fi) | Tries to **avoid** collision **before** it happens by signalling intent to transmit |

> ⚠️ **KEY EXAM POINT**: CSMA/CD = Wired (Detection); CSMA/CA = Wireless (Avoidance). Know the difference!

---

## 1.5 MAC — Basic Services

The MAC sublayer provides **two critical services**:

### 1️⃣ Error Detection (NOT Correction!)
- Performs **error detection** related to the physical layer
- **⚠️ Detection ONLY — No Correction**
- The **Transport Layer** (Layer 4) handles error **correction**

### 2️⃣ Data Encapsulation
- Accepts **Network Layer packets**
- Packages them into data units called **frames** (the PDU of Layer 2)

> ⚠️ **CRITICAL EXAM POINT**: Data Link Layer **detects** errors but does **NOT correct** them. Transport Layer corrects errors!

---

## 1.6 Data Encapsulation — The Frame Structure

The Data Link Layer prepares a packet for transport across local media by **encapsulating it with a header and a trailer** to create a **frame**.

```text name=Frame_Structure_Diagram.txt
┌────────────────────────────────────────────────────────────────────────────┐
│                        DATA LINK FRAME STRUCTURE                           │
│                                                                            │
│  ┌─────────────────────┬──────────────────────┬───────────────────────┐    │
│  │      HEADER         │       PAYLOAD        │       TRAILER        │     │
│  ├─────────────────────┼──────────────────────┼───────────────────────┤    │
│  │                     │                      │                       │    │
│  │ • Start Frame       │  Network Layer       │ • FCS (Frame Check   │     │
│  │   Delimiter         │  Packet (from        │   Sequence)           │    │
│  │                     │  Layer 3)            │                       │    │
│  │ • Type/Length       │                      │ • Stop Frame          │    │
│  │   Field             │  (This is the data   │   (optional - marks   │    │
│  │                     │   being carried)     │    end of frame when  │    │
│  │ • MAC Destination   │                      │    length not in      │    │
│  │   Address           │                      │    Type/Length field) │   │
│  │                     │                      │                       │    │
│  │ • MAC Source        │                      │                       │    │
│  │   Address           │                      │                       │    │
│  │                     │                      │                       │    │
│  └─────────────────────┴──────────────────────┴───────────────────────┘    │
│                                                                            │
│  ◄──── Control info for ────►◄── Actual data ──►◄── Error checking ──►     │
│        topology & media        being carried         & end marking         │
└────────────────────────────────────────────────────────────────────────────┘
```

### Header Contents:
- **Start Frame Delimiter** — Marks the beginning of the frame
- **Type/Length Field** — Identifies the protocol or length of data
- **MAC Destination Address** — Where the frame is going
- **MAC Source Address** — Where the frame came from
- Contains **control information** specified by the Data Link layer protocol for the specific **logical topology and media used**

### Trailer Contents:

| Field | Purpose |
|---|---|
| **FCS (Frame Check Sequence)** | Error checking — source calculates a number based on frame data, places it in FCS. Destination recalculates — if **mismatch → frame is DELETED** |
| **Stop Frame (Frame Trailer)** | **Optional** — indicates end of frame when length is NOT specified in Type/Length field |

> ⚠️ **KEY EXAM POINT**: If FCS doesn't match at destination → **frame is DISCARDED/DELETED** (not corrected!)

---

## 1.7 Full Encapsulation Process Across Layers

```text name=Full_Encapsulation_Process.txt
┌──────────────────────────────────────────────────────────────────────────────────┐
│              COMPLETE ENCAPSULATION ACROSS LAYERS                                │
│                                                                                 │
│  TRANSPORT LAYER (Layer 4 - Segment):                                           │
│  ┌──────────────────────────────────────────────────────────┐                   │
│  │ Seq Number │ Data (GET /HTTP/1.1) │ ACK Number           │                   │
│  └──────────────────────────────────────────────────────────┘                   │
│                              │                                                  │
│                              ▼                                                  │
│  NETWORK LAYER (Layer 3 - IP Packet):                                           │
│  ┌──────────────────────────────────────────────────────────────────────┐       │
│  │ IP Src Addr    │ Src Port │ Seq# │ Data    │ ACK# │ IP Dst Addr    │         │
│  │ (192.168.1.2)  │ (1517)   │      │(GET...) │      │ Dst Port       │         │
│  │                │          │      │         │      │ (443/80)       │         │
│  └──────────────────────────────────────────────────────────────────────┘       │
│                              │                                                  │
│                              ▼                                                  │
│  DATA LINK LAYER (Layer 2 - Frame):                                             │
│  ┌────────────────────────────────────────────────────────────────────────────┐ │
│  │ HEADER                    │ PAYLOAD (entire IP packet)  │ TRAILER         │  │
│  │ ┌────────────────────┐    │                             │ ┌─────────────┐ │  │
│  │ │• Start Frame       │    │ IP Src │Port│Seq#│Data│ACK# │ │• FCS        │ │  │
│  │ │• Type              │    │ IP Dst │DPort│              │ │• Stop Frame │ │  │
│  │ │• MAC Src Address   │    │                             │ └─────────────┘ │  │
│  │ │• MAC Dst Address   │    │                             │                 │  │
│  │ └────────────────────┘    │                             │                 │ │ 
│  └────────────────────────────────────────────────────────────────────────────┘ │
│                              │                                                  │
│                              ▼                                                  │
│  PHYSICAL LAYER (Layer 1):                                                      │
│  ┌──────────────────────────────────────────────────────────────────────┐       │
│  │  1 0 1 1 0 1 0 0 1 1 0 1 0 1 1 0 1 0 0 1 ... (raw bits on media) │           │
│  └──────────────────────────────────────────────────────────────────────┘       │
└──────────────────────────────────────────────────────────────────────────────────┘
```

> ⚠️ **KEY EXAM POINT**: Understand how each layer adds its own header/trailer around the data from the layer above. This is **encapsulation**. Each layer's data unit has a specific name (Segment → Packet → Frame → Bits).

---

# 🔷 PART 2: PHYSICAL LAYER (Layer 1)

## 2.1 Definition

The **1st layer** of the OSI model. It is responsible for **transmitting raw data (bits) over the different types of physical media** that may be present.

---

## 2.2 Types of Physical Media

```text name=Physical_Media_Comparison.txt
┌────────────────────────────────────────────────────────────────────────────┐
│                        PHYSICAL MEDIA TYPES                                │
│                                                                            │
│  ┌───────────────────┐  ┌───────────────────┐  ┌────────────────────────┐  │
│  │  🔌 COPPER CABLE   │  │  💡 FIBER OPTIC   │  │  📡 WIRELESS         │  │
│  │                   │  │                   │  │                        │  │
│  │ • NIC Cards       │  │ • Single-mode     │  │ • Radio waves          │  │
│  │ • UTP Cable       │  │ • Multimode       │  │ • Access Points        │  │
│  │ • RJ-45 Plugs     │  │                   │  │ • Home Router          │  │
│  │   & Sockets       │  │                   │  │                        │  │
│  │                   │  │                   │  │                        │  │
│  │ Speed:            │  │ Speed:            │  │ Speed:                 │  │
│  │ 1 - 10 Gbps      │  │ 1 - 400 Gbps       │  │ Up to 3 Gbps          │   │
│  │                   │  │                   │  │                        │  │
│  │ Use: LAN,         │  │ Use: Long-haul,   │  │ Range:                 │  │
│  │ short distances   │  │ data centres      │  │ 100 - 200 metres       │  │
│  └───────────────────┘  └───────────────────┘  └────────────────────────┘  │
└────────────────────────────────────────────────────────────────────────────┘
```

---

### 2.2.1 NIC (Network Interface Controller) Card

- **Converts digital data to signal data** (and vice versa)
- Present in every network-connected device
- Acts as the **interface between the device and the network medium**

> ⚠️ **KEY EXAM POINT**: NIC = converts **digital data ↔ signal data**

---

### 2.2.2 Copper Cable (Ethernet Cables)

| Component | Detail |
|---|---|
| **Type** | UTP — **Unshielded Twisted Pair** cable |
| **Speed** | **1 – 10 Gbps** |
| **Connector** | **RJ-45** plugs and sockets |
| **Use** | Most common in **LANs** (local area networks) |

---

### 2.2.3 Fiber Optic Cable

```text name=Fiber_Optic_Comparison.txt
┌─────────────────────────────────────────────────────────────────────┐
│                   FIBER OPTIC COMPARISON                           │
│                                                                    │
│   ┌──────────────────────────┬──────────────────────────────────┐  │
│   │    SINGLE-MODE FIBER     │      MULTIMODE FIBER             │  │
│   ├──────────────────────────┼──────────────────────────────────┤  │
│   │                          │                                  │  │
│   │ • Long distance:         │ • Short distance:                │  │
│   │   Up to HUNDREDS of km   │   Typically UNDER 500 metres     │  │
│   │                          │                                  │  │
│   │ • Speed:                 │ • Speed:                         │  │
│   │   1 Gbps - 400 Gbps     │   1 Gbps - 100 Gbps             │    │
│   │                          │                                  │  │
│   │ • Single light path      │ • Multiple light paths           │  │
│   │   (one ray of light)     │   (many rays of light)           │  │
│   │                          │                                  │  │
│   │ • More expensive         │ • Less expensive                 │  │
│   │                          │                                  │  │
│   │ • No dispersion issue    │ • Limited by DISPERSION          │  │
│   │                          │   (light signals spread out,     │  │
│   │                          │    limiting distance & speed)     │ │
│   └──────────────────────────┴──────────────────────────────────┘  │
│                                                                    │
│   KEY: Multimode is limited by DISPERSION - this is why it has     │
│   shorter range and lower max speed than single-mode               │
└────────────────────────────────────────────────────────────────────┘
```

> ⚠️ **KEY EXAM POINT**: Single-mode = **long distance (hundreds of km), up to 400 Gbps**. Multimode = **under 500m, up to 100 Gbps**, limited by **dispersion**.

---

### 2.2.4 Wireless Media

| Feature | Detail |
|---|---|
| **Medium** | Radio waves |
| **Devices** | Home Routers, Access Points |
| **Providers** | BT / EE / Sky / Virgin Media (UK ISPs) |
| **Speed** | Up to **3 Gbps** |
| **Range** | **100 – 200 metres** |

---

## 2.3 Bandwidth vs Throughput

```text name=Bandwidth_vs_Throughput.txt
┌──────────────────────────────────────────────────────────────────┐
│            BANDWIDTH vs THROUGHPUT                               │
│                                                                  │
│   BANDWIDTH                        THROUGHPUT                    │
│   ═════════                        ══════════                    │
│   • CAPACITY of a medium           • ACTUAL transfer of bits     │
│     to carry data                    across media over time      │
│                                                                  │
│   • THEORETICAL maximum            • REAL-WORLD measurement      │
│                                                                  │
│   • Measured in:                   • Always ≤ Bandwidth          │
│     - kb/s (kilobits/sec)                                        │
│     - Mb/s (megabits/sec)          • Affected by latency,        │
│                                      congestion, errors          │
│                                                                  │
│   ANALOGY:                                                       │
│   ┌──────────────────────────────────────────┐                   │
│   │  Bandwidth = Width of a HIGHWAY           │                  │
│   │  (how many lanes it has)                  │                  │
│   │                                           │                  │
│   │  Throughput = Actual TRAFFIC FLOW          │                 │
│   │  (how many cars actually pass per hour)   │                  │
│   └──────────────────────────────────────────┘                   │
│                                                                  │
│   Digital bandwidth = amount of data that can flow from one      │
│   place to another in a given amount of time                     │
└──────────────────────────────────────────────────────────────────┘
```

> ⚠️ **KEY EXAM POINT**: Bandwidth = **capacity** (theoretical max). Throughput = **actual** transfer rate. Throughput is always **≤** Bandwidth.

---

## 2.4 Latency — Factors

Latency is the **delay** in data transmission. There are **7 key factors**:

```text name=Latency_Factors_Diagram.txt
┌──────────────────────────────────────────────────────────────────────────────┐
│                          LATENCY FACTORS                                    │
│                                                                             │
│   ┌─────────────────────────────────────────────────────────────────────┐   │
│   │                                                                     │   │
│   │  1. TRANSMISSION MEDIA                                              │   │
│   │     • Fiber optic = FASTER, LOWER latency                           │   │
│   │     • Copper = Slower, higher latency                               │   │
│   │                                                                     │   │
│   │  2. DISTANCE                                                        │   │
│   │     • Greater physical distance = Higher latency                    │   │
│   │                                                                     │   │
│   │  3. NETWORK TRAFFIC CONGESTION                                      │   │
│   │     • More data than network can handle → Delays                    │   │
│   │     • Packets queue up waiting to be delivered                      │   │
│   │                                                                     │   │
│   │  4. NETWORK EQUIPMENT                                               │   │
│   │     • Quality & efficiency of routers, switches, NICs               │   │
│   │     • Poor equipment = more processing delay                        │   │
│   │                                                                     │   │
│   │  5. NETWORK STRUCTURE                                               │   │
│   │     • Number of devices (hops) between source & destination         │   │
│   │     • More hops = more latency                                      │   │
│   │                                                                     │   │
│   │  6. PROTOCOL OVERHEADS                                              │   │
│   │     • TCP/IP adds overhead: error checking, packet sequencing,      │   │
│   │       acknowledgments → increases latency                           │   │
│   │                                                                     │   │
│   │  7. APPLICATION & SERVICE TYPE                                      │   │
│   │     • Real-time (gaming, video conferencing) = noticeable delays    │   │
│   │     • Cloud/web browsing = slower page loads, data retrieval        │   │
│   │                                                                     │   │
│   └─────────────────────────────────────────────────────────────────────┘   │
└──────────────────────────────────────────────────────────────────────────────┘
```

> ⚠️ **KEY EXAM POINT**: Memorize all **7 factors**. Especially note that **protocol overheads** (TCP/IP error checking, sequencing, ACKs) contribute to latency.

---

## 2.5 Managing Latency — 4 Strategies

| Strategy | Example / Detail |
|---|---|
| **1. Optimising network infrastructure** | Direct interconnection (fewer hops) |
| **2. Using faster transmission media** | High-speed routers, fiber optic |
| **3. Implementing efficient routing protocols** | UDP (less overhead than TCP) |
| **4. Employing CDNs (Content Delivery Networks)** | Cache content **closer to end-users** to reduce distance |

```text name=Latency_Management_Diagram.txt
┌──────────────────────────────────────────────────────────────────┐
│              MANAGING LATENCY — 4 STRATEGIES                     │
│                                                                  │
│  ┌─────────────────┐     ┌──────────────────┐                    │
│  │ 1. OPTIMISE     │     │ 2. FASTER MEDIA  │                    │
│  │ INFRASTRUCTURE  │     │                  │                    │
│  │                 │     │ • High-speed     │                    │
│  │ • Direct        │     │   Routers        │                    │
│  │   interconnect  │     │ • Fiber optic    │                    │
│  │ • Fewer hops    │     │   cables         │                    │
│  └────────┬────────┘     └────────┬─────────┘                    │
│           │                       │                              │
│           └───────────┬───────────┘                              │
│                       │                                          │
│              ┌────────┴────────┐                                 │
│              │  LOWER LATENCY  │                                 │
│              └────────┬────────┘                                 │
│                       │                                          │
│           ┌───────────┴───────────┐                              │
│           │                       │                              │
│  ┌────────┴────────┐     ┌────────┴─────────┐                    │
│  │ 3. EFFICIENT    │     │ 4. CDNs          │                    │
│  │ PROTOCOLS       │     │                  │                    │
│  │                 │     │ • Cache content  │                    │
│  │ • UDP (less     │     │   closer to      │                    │
│  │   overhead)     │     │   end-users      │                    │
│  │ • Less error    │     │ • Reduce         │                    │
│  │   checking      │     │   physical       │                    │
│  └─────────────────┘     │   distance       │                    │
│                          └──────────────────┘                    │
└──────────────────────────────────────────────────────────────────┘
```

> ⚠️ **KEY EXAM POINT**: UDP is mentioned as an **efficient routing protocol** to reduce latency because it has **less overhead** than TCP (no handshaking, no ACKs).

---

# 🔷 MASTER SUMMARY DIAGRAM

```text name=Complete_Summary_Diagram.txt
╔══════════════════════════════════════════════════════════════════════════════════╗
║                    COMPLETE WEEK SUMMARY — OSI LAYERS 1 & 2                    ║
╠══════════════════════════════════════════════════════════════════════════════════╣
║                                                                                ║
║  ┌─────────────────────────────── LAYER 2 ─────────────────────────────────┐   ║
║  │                        DATA LINK LAYER                                  │   ║
║  │                                                                         │   ║
║  │  SUBLAYERS:           DEVICES:          SERVICES:                       │   ║
║  │  ┌─────────┐          ┌────────┐        ┌───────────────────┐           │   ║
║  │  │ LLC     │          │ SWITCH │        │ Error DETECTION   │           │   ║
║  │  │(software│          │(MAC    │        │ (NOT correction!) │           │   ║
║  │  │ upper)  │          │ table) │        │                   │           │   ║
║  │  ├─────────┤          └────────┘        │ Data ENCAPSULATION│           │   ║
║  │  │ MAC     │                            │ (Packets→Frames)  │           │   ║
║  │  │(hardware│   CSMA:                    └───────────────────┘           │   ║
║  │  │ lower)  │   ┌──────────┐                                             │   ║
║  │  └─────────┘   │ CSMA/CD  │ Wired     FRAME STRUCTURE:                  │   ║
║  │                │ CSMA/CA  │ Wireless   ┌────────┬─────────┬─────────┐  │   ║
║  │                └──────────┘            │ HEADER │ PAYLOAD │ TRAILER │  │   ║
║  │                                        │(Start, │(Network │(FCS,    │  │   ║
║  │                                        │ Type,  │ Layer   │ Stop    │  │   ║
║  │                                        │ MAC    │ Packet) │ Frame)  │  │   ║
║  │                                        │ addrs) │         │         │  │   ║
║  │                                        └────────┴─────────┴─────────┘  │   ║
║  └─────────────────────────────────────────────────────────────────────────┘   ║
║                                                                                ║
║  ┌─────────────────────────────── LAYER 1 ─────────────────────────────────┐   ║
║  │                        PHYSICAL LAYER                                   │   ║
║  │                                                                         │   ║
║  │  MEDIA TYPES:                                                           │   ║
║  │  ┌──────────────┬────────────────────┬───────────────────┐              │   ║
║  │  │ COPPER       │ FIBER OPTIC        │ WIRELESS          │              │   ║
║  │  │ UTP/RJ-45    │ Single: 400Gbps    │ Up to 3Gbps       │              │   ║
║  │  │ 1-10 Gbps    │   hundreds of km   │ 100-200m range    │              │   ║
║  │  │              │ Multi: 100Gbps     │                   │              │   ║
║  │  │ NIC: digital │   under 500m       │                   │              │   ║
║  │  │ ↔ signal     │   (DISPERSION)     │                   │              │   ║
║  │  └──────────────┴────────────────────┴───────────────────┘              │   ║
║  │                                                                         │   ║
║  │  BANDWIDTH = Capacity (theoretical)   THROUGHPUT = Actual transfer      │   ║
║  │                                                                         │   ║
║  │  LATENCY FACTORS (7):                MANAGING LATENCY (4):              │   ║
║  │  1. Transmission media               1. Optimise infrastructure         │   ║
║  │  2. Distance                         2. Faster media                    │   ║
║  │  3. Network congestion               3. Efficient protocols (UDP)       │   ║
║  │  4. Network equipment                4. CDNs (cache closer)             │   ║
║  │  5. Network structure (hops)                                            │   ║
║  │  6. Protocol overheads (TCP/IP)                                         │   ║
║  │  7. Application/service type                                            │   ║
║  └─────────────────────────────────────────────────────────────────────────┘   ║
╚══════════════════════════════════════════════════════════════════════════════════╝
```


