
# Cloud Application Design: Principles & Patterns

## 0) Design Flow (3 Steps)

When designing a cloud application, follow this order:

1. **Select Cloud Service Model** (SaaS / PaaS / IaaS)
2. **Choose Cloud Deployment Model** (Public / Private / Hybrid / Community, sometimes Multi-cloud)
3. **Apply Cloud Design Principles & Patterns** (reliability, scalability, maintainability, security)

---

## 1) Step 1 — Select Cloud Service Model

Choose based on how much control vs convenience you want.

- **SaaS**: Use ready software, minimal technical management.
- **PaaS**: Build/deploy apps without managing infrastructure.
- **IaaS**: Rent infra; you manage OS, runtime, and apps.

### Quick decision idea

- Need fastest business usage → **SaaS**
- Need developer speed → **PaaS**
- Need deep control/custom stack → **IaaS**

---

## 2) Step 2 — Choose Deployment Model

## 2.1 Public Cloud (Multi-Tenant Building)

- Shared hardware among many tenants.
- Examples: AWS, Azure, Google Cloud.
- Cheapest and highly scalable.
- Pay-as-you-use model.

**Analogy:** Apartment in a skyscraper (your unit is private, building is shared).

---

## 2.2 Private Cloud (Gated Mansion)

- Dedicated infrastructure for one organization.
- Can be on-premises or provider-hosted dedicated setup.
- Strong compliance/security control.
- More expensive and harder to operate.

**Use cases:** Banks, hospitals, defense, strict-regulated sectors.

**Analogy:** Private house with high boundary wall.

---

## 2.3 Hybrid Cloud (Best of Both Worlds)

- Combination of Public + Private cloud.
- Sensitive data in private; scalable/public-facing workloads in public cloud.

**Example:** Bank keeps PII/SSN private but hosts mobile frontend in public cloud.

**Analogy:** Keep valuables in home safe, use rented warehouse for high-volume operations.

---

## 2.4 Community Cloud (Shared Cul-de-Sac)

- Shared by organizations with common mission/compliance needs.
- Private from outsiders, shared among members.
- Cost-sharing for secure infrastructure.

**Example:** Consortium of universities/government departments.

---

## 2.5 Multi-Cloud (Common in Companies)

Many companies use **multi-cloud** (e.g., AWS + Azure + GCP) for:

- Vendor risk reduction
- Better regional/service fit
- Negotiation and cost flexibility
- Resilience (avoid single-provider dependency)

![[Screenshot 2026-04-11 123516.png]]
---

## 3) Step 3 — Apply Cloud Application Design Principles

Your notes highlight 4 challenge areas.  
These are exactly why principles/patterns are needed.

## 3.1 Why We Need Cloud Design Principles

### Reliability challenges

- Downtime
- Partial failures

### Scalability challenges

- Sudden demand spikes
- DB/server bottlenecks

### Maintainability challenges

- High system complexity
- Hard deployments and updates

### Security challenges

- Malicious/accidental attacks
- Sensitive data protection

---

## 4) Core Design Principles & Patterns

## 4.1 Reliability

### Principle A: High Availability (HA)

Design for service continuity using redundancy.

**Patterns:**

- Multi-instance deployment
- Multi-zone deployment
- Load balancing with health checks
- Active-active / active-passive failover

### Principle B: Fault Tolerance & Resilience

System should continue with degraded mode even when parts fail.

**Patterns:**

- Auto-restart/self-healing
- Replication
- Circuit breaker
- Retry with backoff
- Graceful degradation (non-critical features temporarily disabled)

![[Screenshot 2026-04-11 124249.png]]
---

## 4.2 Scalability

### Principle A: Horizontal Scaling

Add more instances instead of making one machine huge.

**Example:** Netflix adds servers during peak streaming events.

### Principle B: Partitioning

Split workload/data to reduce contention and improve throughput.

**Patterns:**

- **Horizontal partitioning (sharding)** by user key/range
    - Example: Instagram partition by username/user ID
- **Functional partitioning** by business capability
    - Example: Amazon splits modules (orders, payments, search, inventory)

---

## 4.3 Maintainability

### Principle A: Modularity

Break system into smaller independently developed/deployed modules.

**Example:** Amazon-style independent service modules.

### Principle B: Loose Coupling

Reduce hard interdependencies across components.

**Patterns:**

- Clear API contracts
- Event/message-driven communication (queues/topics)
- Bounded contexts
- Independent CI/CD pipelines

**Outcome:** Faster releases, simpler debugging, easier team ownership.

---

## 4.4 Security

### Principle A: Data Protection

- Encryption at rest + in transit
- Key management
- Data classification

### Principle B: Access Control

- Least privilege IAM
- Role-based access control (RBAC)
- MFA and strong identity policies

### Principle C: Proactive Defense

- Continuous monitoring (logs/metrics/traces)
- Threat detection alerts
- Rapid incident response playbooks

---

## 5) Architecture Graph (Text Diagram)

Text

```
                    ┌──────────────────────────────┐
                    │  Cloud Application Design    │
                    └──────────────┬───────────────┘
                                   │
                    ┌──────────────┴───────────────┐
                    │      3-Step Design Flow      │
                    └──────────────┬───────────────┘
                                   │
      ┌──────────────────���─────────┼────────────────────────────┐
      │                            │                            │
┌─────▼─────┐                ┌─────▼─────┐                ┌─────▼─────┐
│ Step 1    │                │ Step 2    │                │ Step 3    │
│ Service   │                │ Deployment│                │ Principles │
│ Model     │                │ Model     │                │ & Patterns │
└─────┬─────┘                └─────┬─────┘                └─────┬─────┘
      │                            │                            │
 SaaS/PaaS/IaaS      Public/Private/Hybrid/Community     Reliability
                                                     Scalability
                                                     Maintainability
                                                     Security
```

---

## 6) Principle-to-Pattern Mapping Table

|Quality Attribute|Principle|Typical Patterns|Example|
|---|---|---|---|
|Reliability|Redundancy + resilience|Multi-AZ, failover, retry, circuit breaker|Service remains up during node crash|
|Scalability|Scale-out + partitioning|Auto-scaling, sharding, stateless services|Netflix peak handling|
|Maintainability|Modularity + loose coupling|Microservices/modules, event bus, API contracts|Independent team deployments|
|Security|Defense-in-depth|Encryption, IAM least privilege, monitoring/SIEM|Protecting sensitive banking data|

---

## 7) Quick Design Checklist (Use in exams/interviews)

Before finalizing cloud architecture, ask:

- **Service Model:** Did we choose SaaS/PaaS/IaaS based on control needs?
- **Deployment Model:** Public/private/hybrid/community/multi-cloud justified?
- **Reliability:** What happens if one service/zone fails?
- **Scalability:** Can we handle 10x traffic spike?
- **Maintainability:** Can teams deploy independently?
- **Security:** Is data encrypted, access controlled, and monitored continuously?

---

## 8) Easy Memory Analogy (One-liner)

- **Public Cloud** = Apartment
- **Private Cloud** = Private house
- **Hybrid** = House + rented office
- **Community** = Gated society
- **Multi-cloud** = Owning units in different cities for flexibility