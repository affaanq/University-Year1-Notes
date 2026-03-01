# 📘 FNCC Lecture 3 — Information Security, Cryptography, Cybercrime & Google Colab

---


```mermaid
graph LR
    A[🌐 You Browse the Internet] --> B[⚠️ Hacking Risk]
    A --> C[📧 Email Monitoring]
    A --> D[🎣 Fake Emails / Phishing]
    B --> E[💰 $10.5 Trillion Cybercrime Cost by 2025]
    C --> E
    D --> E
    style E fill:#ff4444,color:#fff,stroke:#cc0000
```

---

## 🔐 CIA Triad

The **CIA Triad** is the foundational model of information security. Every security measure maps back to one or more of these three principles.

```mermaid
graph TD
    CIA[🛡️ CIA Triad] --> C[🔒 Confidentiality]
    CIA --> I[✅ Integrity]
    CIA --> A[🌐 Availability]

    C --> C1[Only authorised users can access data]
    I --> I1[Data has not been tampered with]
    A --> A1[Data is available when needed]

    style CIA fill:#2196F3,color:#fff,stroke:#1565C0
    style C fill:#4CAF50,color:#fff
    style I fill:#FF9800,color:#fff
    style A fill:#9C27B0,color:#fff
```

### 📊 CIA Triad — Definition Summary

| Principle | Definition | Key Question |
|---|---|---|
| **Confidentiality** | Only those with correct **authorisation** can access the data | *Who can see it?* |
| **Integrity** | Data has **not been tampered** with (intentionally or unintentionally) | *Has it been changed?* |
| **Availability** | Data is **accessible when needed** | *Can I get to it?* |

> **Source:** [SecurityNotepad — Information Security Principles](https://securitynotepad.wordpress.com/2019/06/17/information-security-principles-of-success-12-principles/)

---

### 🎓 CIA Triad �� Scenario: Online Exam Marks on GCU Learn

> [!example] Scenario
> The university releases online exam marks on GCU Learn. Three problems occur:

```mermaid
graph TD
    S[📝 Exam Marks Released on GCU Learn] --> P1
    S --> P2
    S --> P3

    P1[🟡 Problem 1: Student logs in but<br/>no mark appears — page is blank] --> V1[🌐 AVAILABILITY Violation]
    P2[🔴 Problem 2: Student can see OTHER<br/>students' marks but not their own] --> V2[🔒 CONFIDENTIALITY Violation]
    P3[🟠 Problem 3: Student's mark shows 0<br/>even though they passed] --> V3[✅ INTEGRITY Violation]

    style V1 fill:#9C27B0,color:#fff
    style V2 fill:#4CAF50,color:#fff
    style V3 fill:#FF9800,color:#fff
```

| Problem | Description | CIA Violation |
|---|---|---|
| **1** | Student logs in, no mark appears, page blank | **Availability** — data not accessible |
| **2** | Student sees other students' marks, not their own | **Confidentiality** — unauthorised access |
| **3** | Mark shows 0 even though they passed | **Integrity** — data has been altered/corrupted |

---

### 🔒 How to Achieve Confidentiality

```mermaid
mindmap
  root((🔒 Confidentiality))
    Restrict Access
      Share OneDrive links with permissions
      Role-based access control
    Protect Sensitive Information
      Gmail warning messages before sending
      Email encryption
    Classify Data by Importance
      🔴 Highly Sensitive
        Military secrets
        National confidential files
      🟡 Moderately Sensitive
        Company financial data
        Encrypted via VPN
      🟢 Low Sensitivity
        Public press releases
        General public info
```

#### 📋 Data Classification Table

| Classification | Examples | Protection Measure |
|---|---|---|
| 🔴 **Highly Sensitive** | Military secrets, national confidential files | Top-level encryption, strict access control |
| 🟡 **Moderately Sensitive** | Company financial data | VPN encryption, limited access |
| 🟢 **Low Sensitivity** | Company press releases, public info | Standard access, minimal restrictions |

---

### ✅ How to Achieve Integrity

```mermaid
mindmap
  root((✅ Integrity))
    Prevent Unauthorised Modifications
      Banking system: customers cannot<br/>modify account balances directly
    Prevent Improper Modifications by Authorised Users
      Insurance company: employees modify<br/>ONLY their assigned customers
      Cannot access other departments' data
    Maintain Data Consistency
      Sender & receiver balances match<br/>in bank transfers
      Cross-system data alignment
```

| Strategy | Example | Purpose |
|---|---|---|
| Block unauthorised modifications | Banking: customers can't edit balances | Prevents external tampering |
| Restrict authorised user scope | Insurance: employees only modify assigned customers | Prevents internal over-reach |
| Ensure consistency across systems | Bank transfers: sender − amount = receiver + amount | Data remains coherent |

---

### 🌐 How to Achieve Availability

```mermaid
graph TD
    AV[🌐 Availability] --> CH1[⚔️ Challenge 1:<br/>DoS Attacks / Software Flaws]
    AV --> CH2[🌪️ Challenge 2:<br/>Natural Disasters / Human Actions]
    AV --> CH3[⚙️ Challenge 3:<br/>Equipment Failures]

    CH1 --> S1[🖥️ Solution: Separate to<br/>different servers]
    CH2 --> S2[💾 Solution: Backup systems]
    CH3 --> S3[🔄 Solution: Redundant servers]

    style AV fill:#9C27B0,color:#fff
    style S1 fill:#4CAF50,color:#fff
    style S2 fill:#4CAF50,color:#fff
    style S3 fill:#4CAF50,color:#fff
```

| Challenge | Cause | Solution |
|---|---|---|
| **DoS / Software flaws** | Intentional attacks or undiscovered implementation bugs (e.g., unexpected input crashes a program) | Distribute across **different servers** |
| **Natural disasters / Human actions** | Fires, floods, storms, earthquakes, bombs, strikes | **Backup** systems |
| **Equipment failures** | Normal hardware wear and tear | **Redundant** servers |

---

## 🔑 Cryptography

### 📖 Important Terms

> [!info] Definition
> **Cryptography** = The science of secret writing that enables you to **store and transmit data** in a form available **only to intended individuals**.

```mermaid
flowchart LR
    PT[📄 Plaintext<br/>Readable Data] -->|🔐 Encryption<br/>using Algorithm + Key| CT[🔒 Ciphertext<br/>Unreadable Data]
    CT -->|🔓 Decryption<br/>using Algorithm + Key| PT2[📄 Plaintext<br/>Readable Data]

    style PT fill:#4CAF50,color:#fff
    style CT fill:#f44336,color:#fff
    style PT2 fill:#4CAF50,color:#fff
```
![[Pasted image 20260301192052.png]]

| Term | Definition |
|---|---|
| **Cryptography** | Science of secret writing for secure data storage & transmission |
| **Algorithm** | A set of **mathematical rules** used in encryption and decryption |
| **Encryption** | Transforming data into an **unreadable** format |
| **Decryption** | Transforming data back into a **readable** format |
| **Key** | A secret **sequence of bits and instructions** governing encryption/decryption |

> **Source:** [PGP Introduction — DidiSoft](https://pgpi.didisoft.com/doc/pgpintro/)

---

### 🔐 Algorithm Types Overview

```mermaid
graph TD
    CRYPTO[🔑 Cryptography<br/>Algorithm Types] --> SKC[🔑 Secret Key Cryptography<br/>SKC / Symmetric]
    CRYPTO --> PKC[🔑🔑 Public Key Cryptography<br/>PKC / Asymmetric]

    SKC --> SKC1[Uses SINGLE key for<br/>both encryption & decryption]
    PKC --> PKC1[Uses TWO keys:<br/>Public key + Private key]

    style CRYPTO fill:#2196F3,color:#fff
    style SKC fill:#FF9800,color:#fff
    style PKC fill:#9C27B0,color:#fff
```

---

### 🔑 Secret Key Cryptography (SKC) — Symmetric

> Uses a **single secret key** (number, word, or random string) known to **both sender and recipient**.

```mermaid
sequenceDiagram
    participant Sender
    participant Receiver

    Note over Sender,Receiver: 🔑 Both share the SAME secret key

    Sender->>Sender: 📄 Plaintext → 🔐 Encrypt with Key
    Sender->>Receiver: 🔒 Ciphertext sent over network
    Receiver->>Receiver: 🔓 Decrypt with SAME Key → 📄 Plaintext
```

| Aspect | Detail |
|---|---|
| **Keys** | 1 shared secret key |
| ✅ **Pros** | Faster, identity verification, easy to execute |
| ❌ **Cons** | Limited scalability (key distribution problem) |

> **Source:** [SSL2Buy — Symmetric vs Asymmetric Encryption](https://www.ssl2buy.com/wiki/symmetric-vs-asymmetric-encryption-what-are-differences)

---

### 🔑🔑 Public Key Cryptography (PKC) — Asymmetric

> Uses **two keys**: a **public key** (shared with everyone) and a **private key** (kept secret by the owner).

```mermaid
sequenceDiagram
    participant Sender
    participant Receiver

    Note over Receiver: 🔑 Has Public Key (shared)<br/>🔐 Has Private Key (secret)

    Sender->>Sender: 📄 Plaintext → 🔐 Encrypt with<br/>Receiver's PUBLIC Key
    Sender->>Receiver: 🔒 Ciphertext sent over network
    Receiver->>Receiver: 🔓 Decrypt with own<br/>PRIVATE Key → 📄 Plaintext

    Note over Sender: ❌ Cannot decrypt — doesn't<br/>have the private key
```

| Aspect     | Detail                                                                             |
| ---------- | ---------------------------------------------------------------------------------- |
| **Keys**   | 2 keys — public (shared) + private (secret)                                        |
| ✅ **Pros** | Easy key distribution, more scalable, safer                                        |
| ❌ **Cons** | Slower in performance, Not easy to implement and manage due to its large key sizes |

---

### ⚖️ SKC vs PKC Comparison

```mermaid
graph LR
    subgraph "🔑 Symmetric (SKC)"
        S1[1 Key Shared]
        S2[⚡ Faster]
        S3[❌ Hard to Scale]
        S4[🤝 Both parties know key]
    end

    subgraph "🔑🔑 Asymmetric (PKC)"
        A1[2 Keys: Public + Private]
        A2[🐢 Slower]
        A3[✅ Scalable & Safer]
        A4[📤 Public key shared openly]
    end
```

| Feature | SKC (Symmetric) | PKC (Asymmetric) |
|---|---|---|
| **Number of Keys** | 1 (shared) | 2 (public + private) |
| **Speed** | ⚡ Faster | 🐢 Slower |
| **Scalability** | ❌ Limited | ✅ More scalable |
| **Key Distribution** | 🔴 Difficult (must share securely) | 🟢 Easy (public key is open) |
| **Security** | Moderate | Higher |
| **Use Case** | Bulk data encryption | Key exchange, digital signatures |

---

## 🦠 Cybercrime

### 📖 Definition

> [!info] Definition
> **Cybercrime** = Any **illegal behaviour** directed by means of **electronic operations** that targets the **security of computer systems** and the **data processed** by them.

```mermaid
mindmap
  root((🦠 Cybercrime))
    📧 Phishing
    ⚔️ DoS & DDoS Attacks
    💉 SQL Injection
    🐛 Malware Attacks
    🤖 Botnet Infections
    🔌 VS Code Extension Vulnerabilities
```

> **Stat:** Cybercrime costs projected to hit **$10.5 trillion by 2025**
> **Source:** [Business Standard](https://www.business-standard.com/finance/personal-finance/cybercrime-costs-to-hit-10-5-trn-by-2025-how-insurance-may-save-your-biz-124072400476_1.html)

---

### 📧 Phishing

```mermaid
graph TD
    PH[🎣 Phishing Attack] --> HW[How It Works]
    PH --> HP[How to Protect]

    HW --> H1[📧 Pretends to be a<br/>LEGITIMATE organisation]
    HW --> H2[⏰ Creates a sense<br/>of URGENCY]
    HW --> H3[🔗 Uses fake URL<br/>that looks real]

    HP --> P1[🚫 NEVER click<br/>suspicious links]
    HP --> P2[🔍 CHECK the sender's<br/>email address carefully]
    HP --> P3[🛡️ Use ANTI-PHISHING<br/>browser extensions]

    style PH fill:#f44336,color:#fff
    style HP fill:#4CAF50,color:#fff
```

| Aspect | Details |
|---|---|
| **How it works** | Impersonates legitimate org → creates urgency → uses fake URL |
| **Protection** | Never click → Check email address → Use anti-phishing browser |

> **Source:** [PentestPartners — Microsoft Phishing Emails](https://www.pentestpartners.com/security-blog/microsoft-phishing-emails-and-lessons-to-learn/)

---

### ⚔️ DoS & DDoS Attacks

```mermaid
graph TD
    subgraph "DoS Attack"
        DOS[💻 Single Attacker] -->|Massive invalid<br/>requests| SERVER1[🖥️ Target Server]
        SERVER1 --> DOWN1[❌ Server Down]
    end

    subgraph "DDoS Attack"
        DDOS[💻 Attacker] -->|Controls| BOT1[🤖 Bot 1]
        DDOS --> BOT2[🤖 Bot 2]
        DDOS --> BOT3[🤖 Bot 3]
        DDOS --> BOT4[🤖 Bot N...]
        BOT1 -->|Simultaneous<br/>attacks| SERVER2[🖥️ Target Server]
        BOT2 --> SERVER2
        BOT3 --> SERVER2
        BOT4 --> SERVER2
        SERVER2 --> DOWN2[❌ Server Down<br/>Harder to mitigate]
    end

    style DOS fill:#FF9800,color:#fff
    style DDOS fill:#f44336,color:#fff
    style DOWN1 fill:#880000,color:#fff
    style DOWN2 fill:#880000,color:#fff
```

| Type | Mechanism | Difficulty to Mitigate |
|---|---|---|
| **DoS** | Single source sends massive invalid requests | Moderate |
| **DDoS** | Multiple infected devices (**botnets**) attack simultaneously | Very Hard |

> [!example] Real-World Example: GitHub DDoS Attack (2018)
> - **Scale:** 1.35 Tbps — one of the **largest attacks in history**
> - **Impact:** GitHub went **offline for 10 minutes**

---

### 💉 SQL Injection

> **SQL Injection** = Sending **invalid or untrusted data** to a web application by providing **malicious code** so the server processes it (via forms or URL query strings).

```mermaid
flowchart TD
    USER[👤 Attacker] -->|Enters malicious input<br/>in form or URL| WEBAPP[🌐 Web Application]
    WEBAPP -->|Passes input directly<br/>to database query| DB[(🗄️ Database)]
    DB -->|Returns sensitive data<br/>or executes commands| WEBAPP
    WEBAPP -->|Shows stolen data| USER

    style USER fill:#f44336,color:#fff
    style DB fill:#ff9800,color:#fff
```

#### 🔒 SQL Injection Prevention Methods

```mermaid
graph TD
    PREV[🛡️ SQL Injection Prevention] --> PS[📝 Prepared Statements<br/>Pre-built queries, only<br/>parameters are passed]
    PREV --> IS[🧹 Input Sanitisation<br/>Filter characters using<br/>frameworks/libraries]
    PREV --> SP[🗄️ Stored Procedures<br/>Like Prepared Statements<br/>but stored on DB server]
    PREV --> WL[✅ Whitelists<br/>Only allow certain<br/>characters]
    PREV --> BEST[⭐ BEST CASE:<br/>Combine ALL above]

    style BEST fill:#4CAF50,color:#fff,stroke:#2E7D32,stroke-width:3px
```

| Prevention Method | Description |
|---|---|
| **Prepared Statements** | Pre-built SQL statements; only parameters are passed in |
| **Input Sanitisation** | Filter all characters using framework/library sanitizers |
| **Stored Procedures** | Like prepared statements but stored on the database server side |
| **Whitelists** | Only allow specific permitted characters |
| ⭐ **Best Case** | **Combine all of the above** |

---

### 🐛 Other Cybercrime Types

```mermaid
graph TD
    OTHER[🦠 Other Cybercrimes] --> MAL[🐛 Malware Attacks]
    OTHER --> BOT[🤖 Botnet Infections]
    OTHER --> VSC[🔌 VS Code Extension<br/>Vulnerabilities]

    MAL --> MAL1[Viruses, Worms, Trojans,<br/>Ransomware, Spyware]
    MAL --> MAL2[Example: WannaCry 2017<br/>200,000+ computers<br/>150+ countries]

    BOT --> BOT1[Turns devices into 'zombies']
    BOT --> BOT2[Used to spread spam<br/>& conduct cybercrimes]

    style MAL fill:#f44336,color:#fff
    style BOT fill:#FF9800,color:#fff
    style VSC fill:#9C27B0,color:#fff
```

#### 🐛 Malware Attack

| Malware Type | Description |
|---|---|
| **Virus** | Attaches to files, spreads when executed |
| **Worm** | Self-replicates across networks without user action |
| **Trojan** | Disguises as legitimate software |
| **Ransomware** | Encrypts data, demands ransom for decryption |
| **Spyware** | Secretly monitors user activity |

> [!example] WannaCry Ransomware (2017)
> - Affected **200,000+ computers** in **150+ countries**
> - Exploited a Windows vulnerability

#### 🤖 Botnet Infection

- Turns devices into **"zombies"**
- Hackers use them to **spread spam** or **conduct cybercrimes**

#### 🛡️ How to Prevent

- ✅ **Regular backups**
- ✅ **Update firewall**
- ✅ **Do NOT download/open files from unknown sources**

---

## 💻 Google Colab Introduction

### 📖 What is Google Colab?

> [!info] Definition
> **Google Colab** = A **free cloud-based Jupyter Notebook environment** provided by Google that allows users to write and execute **Python code** through a **web browser**.

```mermaid
mindmap
  root((💻 Google Colab))
    ✅ No Installation Needed
      Runs entirely in the browser
    🤝 Collaboration & Sharing
      Like Google Docs for code
    ⚡ Scalability & Performance
      Cloud resources
      Free GPU access
    📦 Pre-installed Libraries
      TensorFlow
      PyTorch
      OpenCV
      Many more
```

> **Link:** [Google Colab](https://colab.research.google.com/)

---

### ⚖️ VS Code vs Google Colab

```mermaid
graph LR
    subgraph "🖥️ VS Code"
        V1[Local installation required]
        V2[General-purpose IDE]
        V3[Manual library installation]
        V4[No free GPU]
        V5[Full project structure]
    end

    subgraph "☁️ Google Colab"
        C1[No installation — browser-based]
        C2[Python-based ML focus]
        C3[Pre-installed libraries]
        C4[Free GPU ⚡]
        C5[Easy markdown integration]
    end
```

| Feature | VS Code | Google Colab |
|---|---|---|
| **Installation** | Required locally | ❌ No installation — runs in browser |
| **Focus** | General-purpose IDE | Python-based **machine learning** |
| **GPU Access** | ❌ Not included | ✅ **Free GPU** |
| **Libraries** | Manual installation | **Pre-installed** (TensorFlow, PyTorch, OpenCV, etc.) |
| **Markdown** | Limited | ✅ **Easy markdown** support |
| **Collaboration** | Via extensions/Git | Built-in **Google Docs-style** sharing |

---

## 🗺️ Complete Lecture Mind Map

```mermaid
mindmap
  root((📘 FNCC Lecture 3))
    🔐 CIA Triad
      🔒 Confidentiality
        Access control
        Data classification
        Highly/Moderately/Low sensitive
      ✅ Integrity
        Prevent unauthorised changes
        Prevent improper authorised changes
        Data consistency
      🌐 Availability
        DoS mitigation → multiple servers
        Natural disasters → backups
        Equipment failure → redundancy
    🔑 Cryptography
      Encryption ↔ Decryption
      Algorithm + Key
      🔑 Symmetric (SKC)
        1 shared key
        Fast but limited scalability
      🔑🔑 Asymmetric (PKC)
        Public + Private keys
        Scalable but slower
    🦠 Cybercrime
      📧 Phishing
        Fake emails, urgency, fake URLs
      ⚔️ DoS/DDoS
        Single vs distributed attack
        GitHub 2018: 1.35Tbps
      💉 SQL Injection
        Malicious input in forms/URLs
        Prevention: prepared statements,<br/>sanitisation, stored procedures, whitelists
      🐛 Malware
        WannaCry 2017
      🤖 Botnets
        Zombie devices
    💻 Google Colab
      Free cloud Jupyter Notebook
      No installation
      Free GPU
      Pre-installed ML libraries
```

---

## 📝 Quick Revision Flashcards

| Question | Answer |
|---|---|
| What are the 3 pillars of the CIA Triad? | **Confidentiality, Integrity, Availability** |
| What does Confidentiality ensure? | Only **authorised users** can access data |
| What does Integrity ensure? | Data has **not been tampered** with |
| What does Availability ensure? | Data is **accessible when needed** |
| What is Cryptography? | Science of **secret writing** for secure data storage/transmission |
| What is the difference between SKC and PKC? | SKC uses **1 key** (symmetric); PKC uses **2 keys** (asymmetric) |
| What is a DoS attack? | Sending **massive invalid requests** from a single source |
| What is a DDoS attack? | Using **botnets** (multiple devices) to attack simultaneously |
| How do you prevent SQL Injection? | Prepared statements, input sanitisation, stored procedures, whitelists |
| What was the WannaCry attack? | 2017 ransomware affecting **200K+ computers** in **150+ countries** |
| What is Google Colab? | Free **cloud-based Jupyter Notebook** by Google for Python |
| What advantage does Colab have over VS Code? | **Free GPU**, no installation, pre-installed ML libraries |

---

## 🔗 References & Sources

- [SecurityNotepad — CIA Triad Principles](https://securitynotepad.wordpress.com/2019/06/17/information-security-principles-of-success-12-principles/)
- [DidiSoft — PGP Introduction (Cryptography)](https://pgpi.didisoft.com/doc/pgpintro/)
- [SSL2Buy — Symmetric vs Asymmetric Encryption](https://www.ssl2buy.com/wiki/symmetric-vs-asymmetric-encryption-what-are-differences)
- [Business Standard — Cybercrime Costs $10.5 Trillion by 2025](https://www.business-standard.com/finance/personal-finance/cybercrime-costs-to-hit-10-5-trn-by-2025-how-insurance-may-save-your-biz-124072400476_1.html)
- [PentestPartners — Microsoft Phishing Emails](https://www.pentestpartners.com/security-blog/microsoft-phishing-emails-and-lessons-to-learn/)
- [Google Colab](https://colab.research.google.com/)

---

> [!tip] Study Tip
> Use the **CIA Triad** as a mental framework for any security scenario. Ask yourself:
> 1. **Who can see it?** → Confidentiality
> 2. **Has it been changed?** → Integrity
> 3. **Can I access it?** → Availability