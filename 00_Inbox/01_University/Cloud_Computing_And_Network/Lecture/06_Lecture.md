
# Lecture 6: Cloud Introduction & Virtualisation

## 1. Core Definitions
Cloud computing eliminates the need for high-cost, on-premises server infrastructure, enabling collaborative work, remote data access, and centralized security/backups. 

> [!abstract] NIST Definition of Cloud Computing (2011)
> "A model for enabling convenient, on-demand network access to a shared pool of configurable computing resources (e.g., networks, servers, storage, applications, and services) that can be rapidly provisioned and released with minimal management effort or service provider interaction."

### The 5 Essential Characteristics
If a service does not have these five traits, it is not true cloud computing.
1. **On-demand self-service:** Users can provision computing capabilities (server time, network storage) automatically without requiring human interaction with the service provider.
2. **Broad network access:** Capabilities are available over the network and accessed through standard mechanisms (PC, laptops, mobile, cross-platform).
3. **Multi-tenancy / Resource pooling:** The provider's resources are pooled to serve multiple consumers using a multi-tenant model. Physical and virtual resources are dynamically assigned according to demand.
4. **Rapid Elasticity:** Capabilities can be elastically provisioned and released to scale rapidly outward and inward. *Example: Scaling up servers automatically to handle Black Friday web traffic.*
5. **Measured service:** Cloud systems automatically control and optimize resource use by leveraging a metering capability. Resource usage can be monitored, controlled, and reported (billing).

---

## 2. Grid Computing vs. Cloud Computing

> [!info] Key Distinction
> Grid computing is about combining computing power for a *single massive task*. Cloud computing is about providing *scalable services* to multiple users on demand.

| Feature | Grid Computing | Cloud Computing |
| :--- | :--- | :--- |
| **Focus** | Application-oriented. | Service-oriented. |
| **Operation** | Tasks are divided into sub-tasks and allocated across interconnected machines working on a large-scale task. | Provides scalable IT services on demand. |
| **Ownership** | Usually owned by an organization within a corporate network. | Owned by an external infrastructure provider (AWS, Azure). |
| **Management** | De-centralised. | Centralised. |
| **Access** | Internal networks. | Accessed via the internet. |

---

## 3. Virtualisation: The Engine of the Cloud
Virtualisation is the foundational technology that makes cloud computing possible. It allows the partitioning of a single physical server into multiple logical servers (Virtual Machines / VMs), each running its own independent operating system and applications.

> [!important] Hypervisors
> A hypervisor (or Virtual Machine Monitor) is the software that creates and runs VMs. It separates the operating system and applications from the underlying physical hardware.

### Types of Hypervisors
* **Type 1 (Native / Bare-Metal):** Sits directly on the physical hardware. Highly efficient and secure. 
	* *Use Case:* Enterprise and business (e.g., MS Hyper-V, VMware ESXi).
	* *Procedure:* Prepare hardware $\rightarrow$ Install Hypervisor directly $\rightarrow$ Create VMs and allocate storage.
* **Type 2 (Hosted):** Runs as a software application on top of an existing host operating system. Overhead from the host OS reduces performance.
	* *Use Case:* Individual testing and development (e.g., VMWare Player, VirtualBox).
	* *Procedure:* Prepare OS $\rightarrow$ Install Hypervisor software $\rightarrow$ Create VM $\rightarrow$ Run VM.

### Core Benefits of Virtualisation

* **Partitioning:** A single physical system can run multiple, distinct applications and OS environments simultaneously.
* **Isolation:** VMs are completely sandboxed from one another. If one VM crashes or is compromised, the others remain unaffected.
* **Encapsulation:** A VM is essentially saved as a set of files. This makes duplication, migration, and backups incredibly simple compared to physical hardware.

### Allocation Types

* **Static Virtualisation:** Fixed, pre-allocated VMs. Limited flexibility as resources are reserved regardless of actual use (leads to resource wastage). Focuses on traditional business models.
* **Dynamic Virtualisation:** VMs are created and destroyed dynamically on-demand. Highly flexible, adjusts based on workload, and ensures efficient resource allocation. This is the backbone of modern cloud computing (AWS, Azure).

---

## 4. Service Abstractions (What can be virtualised?)

1. **Network Virtualisation:** Splitting available network bandwidth into independent channels that can be assigned to specific servers/devices in real-time (e.g., VPNs).
2. **Storage Virtualisation:** Pooling physical storage from multiple network storage devices into what appears to be a single storage device (e.g., Storage Area Networks - SANs).
3. **Server Virtualisation:** Masking server resources (processors, RAM, OS) to increase resource sharing and reduce computational complexity for users.

---

## 5. Case Studies: Traditional vs. Cloud

### Case 1: Virtual Private Network (VPN)
* **Without VPN:** Your source IP is your local network (e.g., campus IP), connecting directly to the destination IP (Internal Server).
* **With VPN:** Your physical Source IP (Home IP) connects to a VPN server. The network is virtualised, assigning you a virtual Source IP (Campus IP) to access the destination server securely.

### Case 2: File Storage Service
* **Traditional (On-Premises):** Requires purchasing server hardware, software, and personnel training. **Major Issue:** Forces the business to accurately estimate present and future capacity needs and manually build fault tolerance.
* **Cloud Storage:** Eliminates capital outlay (OpEx instead of CapEx). Highly scalable and inherently fault-tolerant. **Major Issue:** You are outsourcing your privacy and security to a third party.

### Case 3: Web Site Hosting
* **Traditional (On-Premises):** Requires maintaining physical servers, software, and local web development teams. Faces the same capacity estimation and fault tolerance issues as traditional storage.
* **Cloud Hosting:** Uses infrastructure like AWS, Google Cloud Platform (GCP), or Azure. Alternatively, uses platform services like WordPress or Wix. Highly scalable with zero capital outlay. 

---

## 6. Major Cloud Providers
The public cloud market is dominated by a few massive hyperscalers.
1. **Amazon Web Services (AWS):** The undisputed market leader in cloud infrastructure.
2. **Microsoft Azure:** Strong integration with enterprise Microsoft environments.
3. **Google Cloud Platform (GCP):** Known for data analytics and machine learning capabilities.
4. **Alibaba Cloud:** Dominant in the Asian market.