
# Cloud Architecture

## Definition

> [!info] What is Cloud Architecture?
> Cloud architecture describes **how cloud resources, services, and components interact** to deliver:
> - Computing power
> - Storage
> - Applications  
> over the internet.

---

## Types of Cloud Architecture

## 1) N-tier Architecture (Multi-tier)
A structured architecture that separates an application into layers:

- **Presentation Layer** (Front-end / View)
- **Business Logic Layer** (Server-side controller / rules)
- **Data Layer** (Databases like MySQL, MongoDB)

> [!success] Strengths
> - Clear separation of concerns
> - Easier maintenance for small-to-medium systems
> - Simple to understand and implement

> [!warning] Weaknesses
> - Can have higher **single point of failure risk** if not designed with redundancy
> - Scaling specific layers independently can be harder than microservices

---

## 2) Microservices Architecture
Breaks an application into smaller, independent services.  
Each service handles one business capability and communicates via APIs.

> [!success] Strengths
> - High scalability
> - High flexibility
> - Independent deployment per service

> [!warning] Weaknesses
> - More operational complexity
> - Requires higher engineering maturity and tooling


![[Pasted image 20260411140055.png]]


---

## 3) Event-Driven Architecture
Services communicate by emitting/consuming events in real time.

**Example flow:**  
Order placed → inventory update + payment processing + notification trigger

> [!tip] Best for
> - Real-time systems
> - Asynchronous workflows
> - Loosely coupled integrations

---

## 4) Big Data Architecture
Designed for analytics and AI/ML workloads over large datasets.

**Example uses:**
- User behavior analytics
- Personalized recommendations from browsing + purchase history

> [!tip] Common stack ideas
> Data lake + ETL/ELT + warehouse + BI/ML pipeline

---

## 5) Big Compute Architecture
Designed for high-performance workloads requiring massive processing.

**Example uses:**
- Dynamic pricing engines
- Fraud detection
- Large-scale simulations / model training

> [!warning] Tradeoff
> High computational power often means high cost if not optimized.

---

## Compute in Cloud: Containers

Compute = processing power used to run apps and workloads.  
Containers are a lightweight virtualization method to improve compute efficiency.

> [!info] Container Basics
> A container packages:
> - Application executable
> - Runtime
> - Dependencies  
> So it runs consistently across laptop, server, VM, or cloud.

### Container Benefits
- Standalone unit
- All-in-one package
- Portable across environments
- Scalable
- Isolated/individualized
![[Pasted image 20260411135947.png]]

---

## Docker vs Kubernetes

## Docker
Open-source platform mainly for building and running containers.

- Create
- Deploy
- Run containers
- Often manual scaling/configuration in basic setups

## Kubernetes
Open-source orchestration platform for managing many containers.

- Deployment orchestration
- Auto scaling
- Service discovery
- Built-in load balancing
- Self-healing (restart/reschedule failed containers)

> [!tip] Rule of thumb
> - **Docker**: container runtime/build workflow  
> - **Kubernetes**: container orchestration at scale

---

## Cloud Storage Types

## 1) Object Storage (Best for static/unstructured data)
Store any amount/type of data for any duration.

Examples:
- Media files
- Backups
- Logs
- Data lake objects

## 2) Block Storage (Best for fast low-latency access)
Data split into fixed-size blocks; ideal for high-performance disks.

Examples:
- VM disks
- Transactional databases

## 3) Cloud File Storage (Shared file access)
Hierarchical file system with multi-user/shared access.

Examples:
- Shared team directories
- Content management systems
-
![[Pasted image 20260411140141.png]]
---

## Cost Models in Cloud Architecture

> [!info] Cost Strategy
> Use a mix of:
> - **Pay-as-you-go** (flexibility)
> - **Reserved capacity** (lower predictable cost)
> - **Hybrid model** (balance cost + elasticity)

Goal: Ensure pricing/revenue covers operational cloud cost and maintains profitability.

---

## Advantages of Cloud-Based AI/ML Training

> [!success] Key Benefits
> - **Elastic computing**: Use high-end GPU/TPU only when needed
> - **Consistent environments**: Prebuilt containers + versioning
> - **Collaboration**: Shared notebooks/environments
> - **Easy deployment**: Model-as-a-service APIs quickly
> - **Cost-effective**: Pay only for usage

---

## Quick Architecture Selection Guide

> [!question] Which architecture should I choose?
> - Use **N-tier** for simpler, structured business apps.
> - Use **Microservices** for large, fast-evolving systems needing independent scaling.
> - Use **Event-driven** for real-time and asynchronous workflows.
> - Use **Big Data** for analytics/ML over huge datasets.
> - Use **Big Compute** for intensive HPC-like computation.

---

## Useful References
- [AWS Architecture Center](https://aws.amazon.com/architecture/)
- [Microsoft Azure Architecture Center](https://learn.microsoft.com/azure/architecture/)
- [Google Cloud Architecture Framework](https://cloud.google.com/architecture/framework)
- [Docker Documentation](https://docs.docker.com/)
- [Kubernetes Documentation](https://kubernetes.io/docs/)
- [NIST Cloud Computing Definition](https://csrc.nist.gov/publications/detail/sp/800-145/final)

---

## Tags
#cloud-computing #cloud-architecture #microservices #event-driven #containers #docker #kubernetes #storage #big-data #big-compute #obsidian

## Cloud Patterns:

