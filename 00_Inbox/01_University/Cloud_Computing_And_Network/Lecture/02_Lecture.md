
# OSI Top Layers: Application, Presentation & Session

**Lecture Series:** Week 2 **Focus:** Layers 5, 6, and 7

![[Pasted image 20260207154741.png]]

## 🏗️ Context: The "Upper" Layers

While Layers 1–4 handle the "moving" of data (the plumbing), Layers 5–7 are responsible for the **user experience** and **data interpretation**. In the TCP/IP model, these three layers are often collapsed into a single **Application Layer**.

|OSI Layer|Name|Primary Responsibility|
|---|---|---|
|**7**|**Application**|Network services for user applications (HTTP, DNS)|
|**6**|**Presentation**|Data formatting, encryption, and compression|
|**5**|**Session**|Session management, authentication, and sync|

## 🌐 Layer 7: The Application Layer

The Application Layer is the window through which users and application processes access network services.

![[Pasted image 20260207154908.png]]


### 1. The Client-Server Model

Modern networking relies on the request-response paradigm between two distinct roles:

- **Client (User-Agent):** The entity requesting service (e.g., your web browser).
    
- **Server:** The entity storing data and responding to requests.
    
- **The Internet:** The medium that connects the two ends of the chain.

### 2. Hypertext Transfer Protocol (HTTP)

HTTP is the foundation of the World Wide Web.

- **Paradigm:** Request/Response.
    
- **Statelessness:** HTTP has no "memory." Each transaction (request-response) is independent. Explicit state (like staying logged in) must be managed separately (often using cookies or at the Session Layer).
    
- **Anatomy of a Message:**
    
    1. **Start Line:** (e.g., `GET /index.html HTTP/1.1` or `HTTP/1.1 200 OK`)
        
    2. **Headers:** Key-value pairs providing metadata (Host, User-Agent, Content-Type).
        
    3. **Empty Line:** Required separator.
        
    4. **Body:** The actual data (HTML, JSON, Images).
        

> [!TIP] HTTP Methods
> 
> - **GET**: Retrieve data.
>     
> - **POST**: Create or update data.
>     
> - **PUT**: Replace existing data.
>     
> - **DELETE**: Remove data.
>     

### 3. Domain Name System (DNS)

Humans use URLs (https://www.google.com/search?q=google.com); computers use IP addresses ($142.250.190.46$). DNS is the "Phonebook of the Internet."

1. Client asks **DNS Resolver**.
    
2. Resolver queries **Root Name Servers** $\rightarrow$ **TLD Servers (.com)** $\rightarrow$ **Authoritative Name Servers**.
    
3. IP is returned to the client, which then starts an HTTP session.

![[Pasted image 20260207154250.png]]

### 4. Other Key L7 Protocols

- **FTP**: File Transfer.
    
- **SMTP/POP**: Email delivery and receipt.
    
- **Telnet**: Virtual terminal emulation.
    

## 🎨 Layer 6: The Presentation Layer

This layer is the "Translator." It ensures that data sent from Layer 7 of one system can be read by Layer 7 of another.

### Core Functions:

1. **Formatting (Translation):** Resolves differences in data representation (e.g., converting UTF-8 to UTF-16).
    
2. **Compression:** Reducing bit-size for efficiency.
    
    - _Lossless:_ No data lost (PNG, ZIP).
        
    - _Lossy:_ Data discarded for smaller size (JPEG, MP3).
        
3. **Security (Encryption/Decryption):** Modern encryption like **SSL/TLS** (used in HTTPS) operates here to ensure data is unreadable to interceptors but readable to the application.
    

## 🤝 Layer 5: The Session Layer

The Session Layer acts as the "Meeting Host." It manages the dialogue between applications.

### Core Functions:

- **Communication Management:** Starts, stops, and restarts dialogues (e.g., NetBIOS).
    
- **Authentication:** "Who are you?" (Verifying credentials).
    
- **Authorization:** "What are you allowed to do?" (Checking permissions).
    
- **Synchronization:** In video conferencing, it ensures audio and video streams remain in sync to prevent "laggy" experiences.
    
- **Checkpointing:** In large file transfers, it creates markers so that if a connection fails, the download can resume from the last marker rather than starting over.
    

### Real-World Integration

```
graph LR
    A[Video Stream] --> B{Session Layer}
    C[Audio Stream] --> B
    B --> D[Synced Output]
    style B fill:#f96,stroke:#333,stroke-width:2px
```