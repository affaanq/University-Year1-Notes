
# Cloud Computing, Grid Computing & Virtualization — Complete Study Notes

## 1) Cloud Computing: Definition

Cloud computing is a model that provides **convenient, on-demand network access** to a shared pool of configurable resources such as:

- Networks  
- Servers  
- Storage  
- Applications  
- Services  

These resources can be **rapidly provisioned and released** with minimal management effort or direct interaction with the service provider.

### Simple analogy
Think of cloud computing like **electricity from the power grid**:
- You don’t build your own power plant.
- You just use electricity when needed.
- You pay for what you consume.

Cloud works similarly: you don’t buy and manage all infrastructure; you consume computing as a utility.

---

## 2) Essential Characteristics of Cloud Computing (5)

## 2.1 On-Demand Self-Service
Users can provision resources (VMs, storage, app environments) themselves whenever needed, without waiting for manual provider setup.

- Example: Creating an AWS EC2 VM in minutes.
- Analogy: Like using an ATM anytime without bank staff assistance.

---

## 2.2 Broad Network Access
Cloud services are accessible over the network from many client platforms:
- PCs
- Laptops
- Tablets
- Mobile phones

- Example: Accessing Google Docs from browser/mobile app.

---

## 2.3 Resource Pooling / Multi-Tenancy
Provider resources are pooled to serve multiple customers (tenants). Physical and virtual resources are dynamically assigned/reassigned according to demand.

- Example: Multiple companies using the same cloud data center hardware securely.
- Analogy: Like apartments in one building — shared structure, private living spaces.

---

## 2.4 Rapid Elasticity
Resources scale up/down quickly according to workload.

- Example: E-commerce site scaling during Black Friday traffic spikes.
- Analogy: Like adding/removing lanes on a highway during rush hours.

---

## 2.5 Measured Service
Usage is metered and billed by consumption metrics (compute hours, storage GB, bandwidth).

- Example: Pay per GB in S3, pay per VM runtime in EC2.
- Analogy: Like water/electricity billing based on units consumed.

---

## 3) Deployment Models of Cloud (4)

(You referenced 4 deployment models; including complete standard set:)

1. **Public Cloud**  
   - Owned/operated by third-party providers (AWS, Azure, GCP).  
   - Shared infrastructure; pay-as-you-go.

2. **Private Cloud**  
   - Dedicated to one organization (on-prem or hosted).  
   - More control/security, higher management responsibility.

3. **Hybrid Cloud**  
   - Combines private + public cloud with orchestration between both.  
   - Useful for sensitive workloads + scalable overflow.

4. **Community Cloud**  
   - Shared by organizations with common requirements (e.g., regulatory, mission-based).

---

## 4) Service Models of Cloud (3)

---

## 4.1 SaaS — Software as a Service

### What it is
Software is hosted remotely and delivered over internet; users don’t install/manage backend infrastructure.

### User manages
- Only usage-level settings, basic configuration.

### Provider manages
- App, runtime, middleware, OS, servers, storage, networking.

### Pricing
- Subscription or pay-as-you-go; sometimes free tier.

### Examples
- Google Docs
- Microsoft 365 Online
- Dropbox
- Netflix
- Slack
- Zoom
- Spotify
- Salesforce

### Analogy
Like watching movies in a cinema: you enjoy the service; you don’t manage projector or building.

---

## 4.2 PaaS — Platform as a Service

### What it is
A managed platform to develop, deploy, and run applications without managing hardware/server infrastructure.

### You do
- Write code
- Push/deploy app

### Platform does
- Provisioning
- Environment setup
- Load balancing
- Scaling
- Monitoring
- Often CI/CD integration

### Examples
- AWS Elastic Beanstalk
- Heroku
- Azure App Services / Azure platform services
- OpenShift
- Force.com

### Real scenarios
- University building student management system
- Company building attendance/payroll application

### “Magic Bridge” workflow (from your notes)
1. Developer pushes code to GitHub.
2. Platform detects changes.
3. Platform runs tests/build/deploy.
4. Old app version replaced with new one.
5. User refreshes browser and sees update.

### Analogy
PaaS is like a **fully equipped kitchen rental**:
- You bring recipe (code).
- Kitchen/tools/cleaning/maintenance handled by provider.

---

## 4.3 IaaS — Infrastructure as a Service

### What it is
You rent virtualized infrastructure resources:
- Virtual servers
- Storage
- Networking
- IP addresses

### You control
- OS installation/configuration
- Middleware
- Runtime
- Applications
- Data

### Provider controls
- Physical data center
- Servers
- Storage hardware
- Network backbone
- Virtualization layer

### Examples
- AWS EC2
- Google Compute Engine (GCE)
- Rackspace
- DigitalOcean

### Analogy
Like renting an unfurnished apartment:
- Building/water/electricity are provided.
- Interior setup and management are your responsibility.

---

## 5) Cloud vs Grid Computing

## Grid Computing
Grid computing combines resources from multiple geographically dispersed systems to solve large, complex computational tasks — like a virtual supercomputer.

- Goal: High computational power for specific heavy jobs.
- Ownership/control can be distributed among multiple organizations.

## Cloud Computing
Service-oriented model delivering on-demand IT resources via centralized provider management.

- Goal: Flexible service delivery and scalability.
- Typically centrally managed by infrastructure provider.

### Quick comparison table

| Feature | Grid Computing | Cloud Computing |
|---|---|---|
| Main purpose | Solve compute-intensive large tasks | Deliver IT services on demand |
| Management | Often distributed | Usually centralized |
| Business model | Resource collaboration | Service/subscription/pay-as-you-go |
| Elasticity | Limited/depends on grid setup | Built-in rapid elasticity |
| Typical use | Scientific simulations | Hosting apps, storage, business systems |

---

## 6) Virtualization (Key Enabler of Cloud)

## Definition
Virtualization is the process of creating virtual versions of computing resources by partitioning a physical system into multiple logical environments.

Example:
- One physical server can run multiple virtual machines (VMs).
- Each VM behaves like an independent physical computer with its own OS and apps.

A **hypervisor** is software that creates and manages VMs.

### PC example (from your notes)
You run Windows on your laptop, and inside VMware/VirtualBox you run Linux as a VM.

---

## 7) Benefits of Virtualization

## 7.1 Partitioning
One physical machine can host multiple VMs and workloads.

## 7.2 Isolation
Each VM is isolated. Crash/security issue in one VM does not directly break others.

## 7.3 Encapsulation
A VM is packaged as a manageable unit, making cloning, backup, migration easier.

### Analogy
Like multiple separate houses inside a gated community:
- Same land (hardware), independent houses (VMs), private boundaries (isolation).

---

## 8) Types of Virtualization Allocation Models

## 8.1 Static Virtualization (Static Allocation)
- Pre-allocated fixed VM resources.
- Same VM sizing every time.
- Can lead to under-utilization and storage wastage.

## 8.2 Dynamic Virtualization (Dynamic Allocation)
- Resources allocated/reallocated on demand.
- More flexible and efficient.
- Better for variable workloads.

---

## 9) What Virtualization Can Virtualize

## 9.1 Network Virtualization
Combines/splits network resources (bandwidth/channels) and assigns dynamically in real time.

- Example concept: VPN-like abstraction and segmented virtual networks.

## 9.2 Storage Virtualization
Pools physical storage devices into a unified logical storage resource.

- Example: SAN-based pooled storage.
- User sees one storage layer, not fragmented hardware devices.

## 9.3 Server Virtualization
Abstracts server resources (CPU, RAM, OS instances) into multiple virtual servers.

- Use cases:
  - Email hosting
  - Website hosting
  - Cloud notebook environments (e.g., Colab-like services)

---

## 10) Responsibility Split (Very Important Exam Concept)

| Layer | On-Prem | IaaS | PaaS | SaaS |
|---|---|---|---|---|
| Networking | You | Provider (physical), you (logical config) | Provider | Provider |
| Storage | You | Provider | Provider | Provider |
| Servers | You | Provider | Provider | Provider |
| Virtualization | You | Provider | Provider | Provider |
| OS | You | You | Provider | Provider |
| Middleware/Runtime | You | You | Provider | Provider |
| Application | You | You | You | Provider |
| Data/Usage | You | You | You | You (mostly usage/config) |

---

## 11) Visual Mind Map (Text Version for Obsidian)

```text
Cloud Computing
├── Characteristics
│   ├── On-demand self-service
│   ├── Broad network access
│   ├── Resource pooling (multi-tenancy)
│   ├── Rapid elasticity
│   └── Measured service
├── Deployment Models
│   ├── Public
│   ├── Private
│   ├── Hybrid
│   └── Community
├── Service Models
│   ├── SaaS
│   ├── PaaS
│   └── IaaS
└── Enabler
    └── Virtualization
        ├── Hypervisor
        ├── Partitioning
        ├── Isolation
        ├── Encapsulation
        ├── Static
        └── Dynamic
```
