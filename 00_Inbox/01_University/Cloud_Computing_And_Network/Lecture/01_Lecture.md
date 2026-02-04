
---

# 🌐 Fundamentals of Network and Cloud Computing

## 1. Defining Computer Networks

A computer network is a system of **interconnected computing devices** that exchange data and share resources with each other.

- **Communications Protocols:** Rules used by networked devices to transmit information over physical or wireless technologies.
    
- **Interoperability:** The goal is to provide a decentralized, interoperable network for resource sharing.
    

---

## 2. Switching Methodologies: Circuit vs. Packet

Modern networking evolved from dedicated physical lines to dynamic data units.

### Circuit Switching (The "Legacy" Model)

- **Historical Context:** Based on early telephone systems created in 1878.
    
- **Mechanism:** A dedicated circuit is established; it remains connected and uses resources even if no communication occurs until a party disconnects.
    
- **Efficiency:** Low efficiency; resources may remain idle, and it is possible to get "busy" signals when all circuits are in use.
    
- **Failure Handling:** The entire call/session fails if the circuit breaks.
    

![Image of Circuit Switching vs Packet Switching](https://encrypted-tbn0.gstatic.com/licensed-image?q=tbn:ANd9GcQdoTg_oWWQkcSNRXxVJmanq7c6hBS03tsF3eGp_7rRF_9kyUv_eLOyup0Dz5Ym7QMdAkp0jKhuyoM9eVkPND6Lhh_sNEOA-7UJDFTIHSB0IKqPbBc)

### Packet Switching (The Modern Internet Model)

- **Mechanism:** Messages are split into **packets**, each of which can take a different route over the network.
    
- **Reassembly:** Packets are reassembled into the original message upon arrival at the destination.
    
- **Efficiency:** High efficiency, making it suitable for "bursty" traffic like the Internet, email, and file sharing.
    
- **Reliability:** Packets can automatically reroute if a link fails.
    

---

## 3. Core Network Components

Network infrastructure consists of three main categories.

|**Component Category**|**Description**|**Examples**|
|---|---|---|
|**End Devices**|The interface between humans and the communication network.|Computers, Laptops, Network Printers, VoIP phones, and Mobile devices.|
|**Intermediary Devices**|Devices that interconnect end devices and form the network infrastructure.|Switches, Wireless Access Points, Routers, and Firewalls.|
|**Network Media**|The **physical channel** providing the pathway for message transmission.|Metallic wires (Ethernet), Fiber Optics (glass/plastic), and Wireless transmission.|

> **Functions of Intermediary Devices:** They filter data flow based on security settings and direct data along alternate pathways if a link fails.

---

## 4. Network Scope & Organization

### Geographical Scope

- **LAN (Local Area Network):** Spans a small area (home, school, office building). It is usually administered by a single organization and provides high-speed bandwidth.
    
- **WAN (Wide Area Network):** Spans wide geographical areas (cities, countries, continents) and interconnects LANs. Managed by Service Providers (SPs/ISPs) and typically offers slower-speed links than LANs.
    

### Organizational Models

- **Client-Server Model:** Dedicated servers (Web, Email) run specific software to provide resources, while clients use software (Browsers, Mail clients) to access them.
    
- **Peer-to-Peer (P2P) Model:** Common in small businesses or homes where a computer functions as both a server and a client simultaneously (e.g., sharing a printer or files).
    

---

## 5. Network Protocol Models (OSI vs. TCP/IP)

Protocols define the rules for data transmission between devices.

### The OSI 7-Layer Model

Used for network design, operation specifications, and troubleshooting.

|**Layer**|**Name**|**Function**|**PDU (Data Unit)**|
|---|---|---|---|
|**7**|**Application**|Services for user applications (e.g., HTTP).|Data|
|**6**|**Presentation**|Formats data (e.g., JPG) and encryption.|Data|
|**5**|**Session**|Manages data exchange and connections.|Data|
|**4**|**Transport**|Segmentation and reassembly using ports/sockets (TCP/UDP).|Segment|
|**3**|**Network**|Path determination and Logical Addressing (IP).|Packet|
|**2**|**Data Link**|Physical Addressing (MAC/LLC).|Frame|
|**1**|**Physical**|Binary transmission, signals, and media.|Bits|

### The TCP/IP Model

A suite of protocols required for the Internet. It consolidates the OSI layers into four: **Application, Transport, Internet (Network), and Network Access (Link)**.

![[Pasted image 20260204095503.png]]