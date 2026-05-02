# 📝 Internal Exam 2026 — Full Answers
> **Institute:** Kalol Institute of Computer Studies
> **Subject Code:** DSC-C-BCA-362 | **Cloud Computing**
> **Exam Date:** 10/03/2026 | **Total Marks:** 50

---

## Q.1(a) — What are the Challenges of Cloud Computing? `[05 Marks]`

### Major Challenges:

1. **Security & Privacy:** Most important concern. Data stored on third-party servers — risk of unauthorized access, data breaches. Compliance with data protection regulations (e.g., GDPR, USA PATRIOT Act) is complex. Multi-tenant environments increase risk of data leaks between users.

2. **Vendor Lock-in:** Applications built for one cloud platform (especially PaaS) may not easily migrate to another. Different providers use proprietary APIs and data formats. Switching costs can be prohibitively high.

3. **Availability & Reliability:** Dependency on Internet connectivity. Cloud outages can disrupt all services. SLA (Service Level Agreement) guarantees vary between providers.

4. **Performance & Latency:** Network latency affects response times. Shared resources in multi-tenant environments may cause performance degradation. Not suitable for real-time, latency-sensitive applications.

5. **Data Location & Jurisdiction:** Physical location of data centers determines which laws apply. Sensitive data stored in foreign countries may be subject to foreign government access. Organizations may need data to reside in specific geographical regions.

6. **Interoperability & Standards:** Lack of universal standards across cloud providers. Difficult to integrate services from multiple providers. Moving data between clouds is complex.

---

## Q.1(b) — What is Cloud Computing? Explain Characteristics of Cloud Computing. `[05 Marks]`

### Definition:
> **Cloud Computing** is a utility-oriented and Internet-centric way of delivering IT services on demand. These services cover the entire computing stack: from hardware infrastructure packaged as virtual machines to software services such as development platforms and distributed applications.

### Five Essential Characteristics (NIST):

1. **On-Demand Self-Service:** Users can automatically provision computing resources (servers, storage) without requiring human interaction with the service provider.

2. **Broad Network Access:** Services are available over the network and accessed through standard mechanisms by diverse platforms (mobile, tablet, laptop).

3. **Resource Pooling:** Computing resources are pooled using a multi-tenant model. Physical and virtual resources dynamically assigned based on demand. Customer has no control/knowledge of exact resource location.

4. **Rapid Elasticity:** Capabilities elastically provisioned and released — scale up and down with demand. To the consumer, resources appear unlimited.

5. **Measured Service:** Automatic control and optimization of resource use through metering. Usage monitored, controlled, and reported — enabling pay-per-use billing.

---

## Q.1 OR (a) — What is Virtualization? `[05 Marks]`

### Definition:
Virtualization is a technique that provides an **abstraction layer** between the physical hardware and the user/application, creating an illusion of a different (or additional) computing environment using emulation or abstraction.

### Key Concepts:
- **Guest:** The system that interacts with the virtualization layer (e.g., a guest OS running in a VM).
- **Host:** The original environment where virtualization takes place (physical machine).
- **Virtualization Layer (VMM/Hypervisor):** Software that recreates the guest environment and manages resources.

### Types:
- **Hardware/System-Level Virtualization:** Complete hardware emulation — run entire guest OS (VMware, Xen, Hyper-V).
- **Programming Language-Level:** Execute bytecode on virtual machine (Java VM, .NET CLR).
- **OS-Level Virtualization:** Multiple isolated user-space instances on single OS kernel (Docker, FreeBSD Jails).
- **Application-Level:** Run apps in emulated environments (Wine).

### Benefits:
- Security through isolation and sandboxing
- Managed execution (sharing, aggregation, emulation, isolation)
- Portability (VM instances are files)
- Server consolidation (reduce active physical servers)

---

## Q.1 OR (b) — Give Some Examples of Web 2.0 Applications `[05 Marks]`

**Web 2.0** refers to the second generation of Internet-based services emphasizing user collaboration, content sharing, and rich interactive experiences. It transformed the Web from a collection of static pages to a platform for interactive applications.

### Key Characteristics:
- Rich user interfaces (AJAX-based)
- User-generated content
- Social collaboration
- Mashups (combining data from multiple sources)

### Examples:

| Application | Category | Description |
|-------------|----------|-------------|
| **Facebook** | Social Networking | User profiles, content sharing, third-party app integration |
| **YouTube** | Video Sharing | User-uploaded videos, commenting, sharing |
| **Wikipedia** | Collaborative Knowledge | Community-edited encyclopedia |
| **Twitter** | Micro-blogging | Short-form content sharing and real-time updates |
| **Google Maps** | Mashup Platform | Interactive maps; APIs allow integration into other apps |
| **Flickr** | Photo Sharing | Upload, organize, share photos with tags and comments |
| **WordPress/Blogger** | Blogging | User-generated blog content |
| **LinkedIn** | Professional Networking | Professional connections, job searching |

### Web 2.0 and Cloud Computing:
Web 2.0 applications are major consumers of cloud computing infrastructure. Their need for scalability, availability, and global reach makes cloud computing an ideal delivery platform. Many Web 2.0 applications are themselves SaaS offerings.

---

## Q.2(a) — Explain SISD and MIMD Architecture of Parallel Computing `[05 Marks]`

Flynn's Classification categorizes computer architectures based on instruction and data streams:

### SISD (Single Instruction, Single Data):
- **One instruction stream** processed at a time on **one data stream**.
- Traditional sequential (von Neumann) computer model.
- One CPU, one control unit, one memory.
- Instructions executed one at a time in sequence.
- May include pipelining for limited speedup.
- **Example:** Classic single-core processors.

### MIMD (Multiple Instruction, Multiple Data):
- **Multiple processors** execute **different instructions** on **different data** simultaneously.
- Most general and powerful parallel architecture — most modern parallel systems are MIMD.
- Two sub-categories:
  - **Shared Memory (Multiprocessors):** All processors share a common address space. Easier to program but limited scalability. Example: SMP systems.
  - **Distributed Memory (Multicomputers):** Each processor has private memory. Communicate via message passing. Example: Clusters, cloud computing infrastructure.
- **Examples:** Multi-core processors, computing clusters, supercomputers.

---

## Q.2(b) — Explain Elements of Parallel Computing `[05 Marks]`

### Hardware Architectures (Flynn's Classification):
- **SISD:** Sequential — one instruction, one data stream
- **SIMD:** One instruction applied to multiple data streams (GPUs, vector processors)
- **MISD:** Multiple instructions on single data stream (rare; pipelining approximation)
- **MIMD:** Multiple instructions on multiple data streams (most powerful)

### Approaches to Parallel Programming:
1. **Data Parallelism:** Divide data into subsets; apply same operation on each subset simultaneously on different processors (divide-and-conquer).
2. **Process Parallelism:** Different processors execute different programs/tasks simultaneously on same or different data.
3. **Farmer-and-Worker Model:** Master process distributes tasks to worker processes. Workers execute and return results to master.

### Levels of Parallelism:

| Grain Size | Code Item | Parallelized By |
|-----------|-----------|-----------------|
| **Large** | Heavyweight process | Programmer |
| **Medium** | Function/Procedure | Programmer |
| **Fine** | Loop/Instruction block | Parallelizing compiler |
| **Very Fine** | Single instruction | Processor hardware |

### Laws of Caution:
- Adding more processors doesn't always improve performance proportionally.
- Communication overhead and sequential portions limit speedup (Amdahl's Law).

---

## Q.2 OR (a) — Explain Components of Distributed System `[05 Marks]`

### Definition (Tanenbaum):
> "A distributed system is a collection of independent computers that appears to its users as a single coherent system."

### Layered Architecture:

```
┌───────────────────┐
│   Applications    │ ← User-facing apps (web, enterprise)
├───────────────────┤
│   Middleware      │ ← Hides heterogeneity, provides uniform env
├───────────────────┤
│   Operating System│ ← IPC, process scheduling, resource mgmt
├───────────────────┤
│   Hardware        │ ← Network hardware, processors, storage
└───────────────────┘
```

1. **Hardware:** Computer and network hardware forming the physical infrastructure.
2. **Operating System:** Provides basic services — interprocess communication (IPC), process scheduling, resource management. Together with hardware, forms the **platform**.
3. **Middleware:** Leverages OS services to create a uniform environment for development and deployment. Hides heterogeneity of underlying systems. Provides programming frameworks, protocols, and data formats.
4. **Applications:** Built on top of middleware. Expose features through GUIs or web interfaces.

### Communication Models:
- **RPC (Remote Procedure Call):** Call procedures on remote machines as if local.
- **Message Passing:** Processes communicate by sending/receiving messages.
- **Distributed Objects:** Object-oriented RPC (CORBA, RMI, DCOM).
- **Web Services:** SOA-based communication using SOAP/REST.

---

## Q.2 OR (b) — Parallel vs. Distributed Computing `[05 Marks]`

| Aspect | Parallel Computing | Distributed Computing |
|--------|-------------------|----------------------|
| **Definition** | Multiple processors within a single system work on a problem simultaneously | Collection of independent computers appear as a single coherent system |
| **Coupling** | Tightly coupled | Loosely coupled |
| **Memory** | Shared memory (common) | Each computer has own local memory |
| **Communication** | Shared variables, high-speed bus | Message passing over network |
| **Goal** | Speed up computation | Resource sharing, reliability, scalability |
| **Hardware** | Homogeneous (same type) | Heterogeneous (different types) |
| **Location** | Same machine/rack | Geographically dispersed |
| **Failure Model** | Single point of failure | Partial failures — no single point of failure |
| **Scalability** | Limited by physical hardware | Highly scalable |
| **Examples** | Multi-core CPUs, GPUs, supercomputers | Internet, cloud computing, web services |

---

## Q.3(a) — Explain the Pros and Cons of Virtualization `[05 Marks]`

### Pros (Advantages):
1. **Managed Execution & Isolation:** Secure, controllable computing environments. Sandbox prevents harmful operations. Resource allocation simplified.
2. **Portability:** VM instances are files — easily transported and self-contained. Java: "compile once, run everywhere."
3. **Reduced Maintenance:** Fewer physical hosts to maintain; limited opportunity for guest to damage hardware.
4. **Efficient Resource Use:** Multiple systems share host resources securely. Enables server consolidation — reduces active servers, saves energy ("greening").
5. **Migration:** VMs can be moved between physical hosts (live migration without service interruption).

### Cons (Disadvantages):
1. **Performance Degradation:** Abstraction layer introduces latency. Causes: maintaining virtual processor state, privileged instruction emulation, paging support, console functions. In language VMs: binary translation and interpretation overhead.
2. **Inefficiency:** Some host features cannot be exposed through abstraction layer (e.g., advanced GPU capabilities).
3. **Security Threats:** New forms of phishing and malware. Examples: **BluePill** (targets AMD, moves OS into VM), **SubVirt** (gains control on reboot). Modified runtime environments can monitor sensitive memory.

---

## Q.3(b) — Characteristics of a Virtualized Environment `[05 Marks]`

Three major components: **Guest** (entity interacting with virtualization layer), **Host** (original environment), **Virtualization Layer/VMM** (recreates the environment).

### 1. Increased Security:
- Guest operations contained within VM boundaries.
- VMM controls and filters guest activity.
- Prevents harmful operations from reaching host.
- Example: Java applets sandboxed in JVM.

### 2. Managed Execution:
- **Sharing:** Multiple computing environments on same host — exploit powerful hardware fully.
- **Aggregation:** Group of hosts represented as single virtual host (cluster computing).
- **Emulation:** VM emulates a completely different environment from host.
- **Isolation:** Guests get completely separate environments — no interference.
- Additional: Performance tuning (QoS control), State capture (save/restore VM state).

### 3. Portability:
- VM instances represented as files — easily portable.
- Self-contained; only need VMM to run.
- Simplifies administration and migration.
- Reduces maintenance costs.

---

## Q.3 OR (a) — Desktop Model of Full Virtualization `[05 Marks]`

### Full Virtualization:
Full virtualization provides **complete emulation of underlying hardware** — guest OS runs **unmodified** as though on raw hardware. The VMM creates a virtual hardware environment identical to the physical one.

### Desktop Full Virtualization (VMware Workstation Model):

```
┌─────────────────────────────────────┐
│   User Applications (Unmodified)    │  Ring 3
├─────────────────────────────────────┤
│   Guest OS (Unmodified, VMM-unaware)│  Ring 1
├─────────────────────────────────────┤
│   Hypervisor (Binary Translation)   │  Ring 0
│   • Direct execution (non-sensitive)│
│   • Binary translation (sensitive)  │
│   • Instruction caching             │
├─────────────────────────────────────┤
│   Hardware (x86)                    │
└─────────────────────────────────────┘
```

### How it Works:
1. VMware application installed on host OS creates virtual machine instances.
2. A specific **driver** in the host OS deploys a VMM running in privileged mode.
3. **Non-sensitive instructions:** Executed directly on hardware (direct execution).
4. **Sensitive instructions:** Hardware trap triggers **binary translation** — offending instructions translated to equivalent safe instructions. Translated code is cached for reuse.
5. VMware also provides virtual memory (MMU emulation) and virtual I/O devices.

### Advantages: Guest OS runs unmodified; complete isolation.
### Disadvantages: Binary translation introduces performance overhead.

---

## Q.3 OR (b) — Taxonomy of Virtualized Techniques `[05 Marks]`

### Two Major Categories:

#### 1. Execution Virtualization:

**A. Process-Level (Application/Language VMs):**
- **Programming Language VMs:** Execute bytecode (Java VM, .NET CLR). Provide portability, sandboxing, managed execution.
- **Application-Level:** Run apps in emulated environments (Wine for Windows apps on Linux).

**B. System-Level (Hardware VMs):**
- **Full Virtualization:** Complete hardware emulation; guest OS unmodified (VMware).
- **Paravirtualization:** Modified guest OS with hypercalls; better performance (Xen).
- **Hardware-Assisted:** CPU provides virtualization support (Intel VT, AMD-V).
- **Partial Virtualization:** Incomplete emulation; not all features supported.
- **OS-Level:** Single kernel, multiple isolated user spaces (Docker, FreeBSD Jails).

#### 2. Other Types:
- **Storage Virtualization:** Abstract physical storage into logical representation (SANs).
- **Network Virtualization:** Aggregate physical networks into virtual networks (VLANs).
- **Desktop Virtualization:** Remote desktop environments accessible via client/server approach.
- **Application Server Virtualization:** Load-balanced application servers as single virtual server.

### Hypervisor Types:
| Type | Location | Example |
|------|----------|---------|
| **Type I (Native)** | Directly on hardware | Xen, VMware ESXi, Hyper-V |
| **Type II (Hosted)** | On top of host OS | VMware Workstation, VirtualBox |

---

## Q.4(a) — Explain Cloud Deployment Model `[05 Marks]`

### Four Types:

**1. Public Cloud:** Open to wider public via Internet. Multi-tenant. Pay-as-you-go. Example: AWS, Google AppEngine.

**2. Private Cloud:** Within private premises of organization. Uses existing infrastructure. Better security and control. Example: Eucalyptus, OpenNebula-based deployments.

**3. Hybrid Cloud:** Combination of private + public. Private for sensitive operations, public for peak loads (cloudbursting). Example: OpenNebula with Amazon EC2.

**4. Community Cloud:** Shared by organizations with common goals (mission, security, compliance). Multi-administrative domain. Example: Healthcare cloud, scientific computing cloud.

*(See detailed answer in Exam Prediction Guide Q13)*

---

## Q.4(b) — Explain Cloud Computing Architecture `[05 Marks]`

### Layered Architecture:

```
┌────────────────────────────────────┐
│  Cloud Applications (SaaS)        │  CRM, Social Networks, Google Apps
├────────────────────────────────────┤
│  User-Level Middleware (PaaS)     │  APIs, Frameworks, Web 2.0, Mashups
├────────────────────────────────────┤
│  Core Middleware                  │  QoS, SLA, Monitoring, Billing
├────────────────────────────────────┤
│  System Infrastructure (IaaS)    │  VMs, VM Management, Deployment
├────────────────────────────────────┤
│  Cloud Resources                  │  Datacenters, Clusters, Storage
└────────────────────────────────────┘
```

- **Bottom:** Physical resources managed by core middleware using virtualization.
- **Middle:** PaaS provides development environments; IaaS provides raw infrastructure.
- **Top:** SaaS delivers applications to end users via web browser.
- **XaaS (Everything-as-a-Service):** Services from different providers combined across the stack.

*(See detailed answer in Exam Prediction Guide Q3)*

---

## Q.4 OR (a) — Cloud Reference Model `[05 Marks]`

*(Same as Q.4(b) above — see Cloud Computing Architecture answer)*

The Cloud Reference Model organizes services into layers: IaaS (bottom), PaaS (middle), SaaS (top). Each layer builds upon the one below. IaaS provides virtual machines and storage; PaaS provides development platforms; SaaS provides end-user applications.

---

## Q.4 OR (b) — Explain Economics of Cloud `[05 Marks]`

### Cloud computing economics:
1. **Reduces capital costs** — no need to purchase hardware upfront.
2. **Eliminates depreciation** — no IT capital assets to depreciate.
3. **Replaces licensing with subscriptions** — OpEx instead of CapEx.
4. **Cuts maintenance costs** — provider handles hardware maintenance.

### Key Concepts:
- **CAPEX → OPEX shift:** Buy hardware (capital expense) → rent cloud resources (operational expense).
- **Pay-as-you-go (PAYG):** Only pay for resources consumed.
- **Economy of Scale:** Large providers (AWS, Google) offer lower prices through massive scale.
- **Elasticity = Savings:** Scale up for peak demand, scale down when idle — no wasted resources.

### Business Impact:
- **Start-ups:** Launch without large IT investment; grow infrastructure with revenue.
- **Enterprises:** Reduce IT department burden; focus on core business.
- **Variable Workloads:** Cloudbursting — temporarily lease public resources for peaks.

---

## Q.5 — MCQ Answers `[10 Marks]`

| # | Answer |
|---|--------|
| 1 | **(C)** Software-as-a-Service (SaaS) |
| 2 | **(D)** All of the mentioned (Security, Availability, Large Network Access) |
| 3 | **(D)** Cloud Computing |
| 4 | **(A)** Platform as a Service |
| 5 | **(C)** Providing services like storage, servers, database, networking, etc. |
| 6 | **(A)** Multiple-Instruction Multiple-Data |
| 7 | **(A)** Hardware as a Service |
| 8 | **(D)** Merged Cloud Servers |
| 9 | Public cloud = cloud owned by third-party provider, accessible to anyone over Internet. **Example: AWS** |
| 10 | **Amazon Web Services** |

---

*All answers based on "Mastering Cloud Computing" by Rajkumar Buyya.*
