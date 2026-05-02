# ☁️ Cloud Computing — Exam Prediction Guide (BCA Sem VI)
> **Subject Code:** DSC-C-BCA-362 | **Textbook:** Mastering Cloud Computing — Rajkumar Buyya
> **Strategy:** Questions repeated across 2021, 2023, 2026 papers are HIGHEST priority.

---

## 🔴 PRIORITY MAP (Repeat Count)

| Topic | 2021 | 2023 | 2026 | Faculty | Priority |
|-------|------|------|------|---------|----------|
| Characteristics of Cloud Computing | ✅ | ✅ | ✅ | ✅ | 🔴🔴🔴 |
| Cloud Computing Architecture / Reference Model | ✅ | ✅ | ✅ | ✅ | 🔴🔴🔴 |
| Types/Deployment Models (Public, Private, Hybrid, Community) | ✅ | ✅ | ✅ | ✅ | 🔴🔴🔴 |
| Characteristics of Virtualized Environments | ✅ | ✅ | ✅ | ✅ | 🔴🔴🔴 |
| Pros and Cons of Virtualization | ✅ | ✅ | ✅ | ✅ | 🔴🔴🔴 |
| Taxonomy of Virtualization Techniques | — | ✅ | ✅ | ✅ | 🔴🔴 |
| SISD / MIMD Architecture | ✅ | — | ✅ | ✅ | 🔴🔴 |
| Parallel vs. Distributed Computing | — | ✅ | ✅ | ✅ | 🔴🔴 |
| Elements of Distributed Computing | — | ✅ | ✅ | ✅ | 🔴🔴 |
| Platform-as-a-Service (PaaS) | ✅ | — | — | ✅ | 🟡 |
| Cloud Computing Platforms (AWS, AppEngine, Azure) | ✅ | ✅ | — | ✅ | 🟡 |
| Economics of the Cloud | — | ✅ | ✅ | ✅ | 🟡 |
| Vision of Cloud Computing | — | ✅ | — | ✅ | 🟡 |
| Eras of Computing | — | ✅ | — | ✅ | 🟡 |
| System Architectural Styles | ✅ | — | — | ✅ | 🟡 |

---

# UNIT 1 — Cloud Computing Fundamentals

---

## Q1. Explain Characteristics and Benefits of Cloud Computing 🔴🔴🔴
**(Appeared: 2021, 2023, 2026, Faculty-marked important)**

### Characteristics of Cloud Computing:

1. **On-Demand Self-Service:** Users can provision computing resources (servers, storage, network) automatically without requiring human interaction with service providers.

2. **Broad Network Access:** Services are available over the network (Internet) and accessed through standard mechanisms by heterogeneous platforms (mobile phones, tablets, laptops, workstations).

3. **Resource Pooling:** Provider's computing resources are pooled to serve multiple consumers using a multi-tenant model. Physical and virtual resources are dynamically assigned and reassigned according to demand. The customer generally has no control or knowledge of the exact location of the resources.

4. **Rapid Elasticity:** Capabilities can be elastically provisioned and released to scale rapidly outward and inward commensurate with demand. To the consumer, the capabilities appear to be unlimited and can be purchased in any quantity at any time.

5. **Measured Service:** Cloud systems automatically control and optimize resource use by leveraging a metering capability. Resource usage can be monitored, controlled, and reported, providing transparency for both provider and consumer (pay-per-use model).

### Benefits:
- **Reduced Capital Costs:** No need for large up-front hardware purchases; pay-as-you-go model.
- **Scalability:** Scale up or down on demand to handle varying workloads.
- **Accessibility:** Services available from anywhere via Internet.
- **Reduced Maintenance:** Provider handles hardware maintenance and upgrades.
- **Business Agility:** Rapidly deploy new applications and services.
- **Disaster Recovery:** Data stored redundantly across multiple locations.

---

## Q2. Explain the Vision of Cloud Computing 🟡
**(Appeared: 2023, Faculty)**

Cloud computing is a **utility-oriented and Internet-centric way of delivering IT services on demand**. These services cover the entire computing stack: from hardware infrastructure packaged as virtual machines to software services such as development platforms and distributed applications.

### Key Vision Elements:

1. **Everything as a Service (XaaS):** The entire IT computing stack—infrastructure, platform, and applications—can be composed by relying on cloud computing services. This is the vision of "Everything as a Service."

2. **Utility Computing Model:** Computing resources consumed like utilities (electricity, water). Users pay only for what they use, eliminating large capital investments.

3. **On-Demand Delivery:** IT resources delivered when needed, in the quantity needed.

4. **Internet-Centric:** Services delivered over the Internet, accessible from anywhere.

5. **Convergence of Technologies:** Cloud computing emerged from the convergence of:
   - **Service-Oriented Computing (SOC):** Web services and SOA provide interoperability.
   - **Web 2.0:** Rich, interactive web applications (AJAX, mashups) become the front-end.
   - **Virtualization:** Provides isolation, customization, and resource optimization.
   - **Grid Computing / Utility Computing:** Harnesses distributed resources on demand.

---

## Q3. Explain Cloud Computing Reference Model 🔴🔴🔴
**(Appeared: 2021, 2022, 2023, 2025, 2026 — MOST REPEATED)**

The Cloud Computing Reference Model organizes cloud services into a layered architecture:

```
┌─────────────────────────────────────┐
│   Cloud Applications (SaaS)        │ ← Social Computing, CRM, Google Apps
├─────────────────────────────────────┤
│   User-Level Middleware (PaaS)     │ ← Web 2.0, Mashups, Programming APIs
├─────────────────────────────────────┤
│   Core Middleware                  │ ← QoS, SLA, Monitoring, Billing
├─────────────────────────────────────┤
│   System Infrastructure (IaaS)    │ ← VMs, VM Management, Deployment
├─────────────────────────────────────┤
│   Cloud Resources (Hardware)       │ ← Datacenters, Clusters, Storage
└─────────────────────────────────────┘
```

### Layers Explained:

1. **Cloud Resources (Bottom Layer):** Physical infrastructure — datacenters with hundreds/thousands of nodes. Can be heterogeneous (clusters, networked PCs).

2. **System Infrastructure / Core Middleware (IaaS):** Manages the physical infrastructure. Virtualization technologies (hypervisors) manage the pool of resources and expose them as VMs. Provides runtime environment customization, application isolation, sandboxing, and QoS. Includes QoS negotiation, admission control, execution management, monitoring, metering, accounting, and billing.

3. **User-Level Middleware (PaaS):** Cloud programming environments and tools. Provides development platform for applications using Web-based interfaces, command-line tools, and frameworks for concurrent/distributed programming. Users develop applications using exposed APIs.

4. **Cloud Applications (SaaS):** Services delivered at the application level — Web-based applications that rely on the cloud. Examples: SalesForce.com (CRM), Google Apps (office automation), social networking.

### Three Service Models:

| Model | What is Delivered | Examples |
|-------|-------------------|----------|
| **IaaS** | Virtualized hardware & storage | Amazon EC2, GoGrid |
| **PaaS** | Development platform | Google AppEngine, Microsoft Azure |
| **SaaS** | Web applications | SalesForce.com, Google Apps |

---

## Q4. What are IaaS, PaaS, and SaaS? 🟡

### Infrastructure-as-a-Service (IaaS):
- Delivers **customizable infrastructure on demand** — virtual machines, storage, networking.
- Technology: Hardware virtualization (hypervisors manage pools of resources).
- Benefits: Workload partitioning, application isolation, sandboxing, hardware tuning.
- Customer gets full control of OS and software stack on virtual machines.
- **Examples:** Amazon EC2, GoGrid, Rackspace.

### Platform-as-a-Service (PaaS):
- Provides a **development and deployment platform** for running applications in the cloud.
- Automates deploying apps to infrastructure, scaling, provisioning databases and load balancers.
- Essential characteristics: Runtime framework, Abstraction, Automation, Cloud services.
- Major concern: **Vendor lock-in** — apps may become dependent on provider's specific APIs.
- **Examples:** Google AppEngine, Microsoft Azure, Heroku.

### Software-as-a-Service (SaaS):
- Provides **access to applications** through the Internet as a Web-based service.
- Users "rent" or "subscribe to" applications from a central provider.
- Key feature: **Multitenancy** — single application shared across multiple users.
- No installation needed; accessible via web browser.
- **Examples:** SalesForce.com (CRM), Google Docs, Zoho Office.

---

# UNIT 2 — Parallel and Distributed Computing

---

## Q5. Explain SISD and MIMD Architecture of Parallel Computing 🔴🔴
**(Appeared: 2021, 2026, Faculty)**

Flynn's Classification categorizes computer architectures by instruction and data streams:

### SISD (Single Instruction, Single Data):
- **One instruction stream** operates on **one data stream** at a time.
- Represents the traditional **sequential (von Neumann) computer**.
- One CPU executes one instruction at a time on one piece of data.
- Instructions are executed sequentially (may include pipelining).
- **Example:** Traditional single-core processors.

```
Instruction Pool → [CU] → [PU] → Data Pool
     (IS)                  (DS)
```

### MIMD (Multiple Instruction, Multiple Data):
- **Multiple processors** execute **different instructions** on **different data** simultaneously.
- Most general and powerful parallel architecture.
- Each processor has its own control unit and can execute a separate instruction stream.
- Two sub-types based on memory:
  - **Shared Memory (Multiprocessors):** All processors access a common memory. Examples: SMP (Symmetric Multiprocessors).
  - **Distributed Memory (Multicomputers):** Each processor has its own local memory. Processors communicate via message passing. Examples: Clusters, MPP systems.
- **Examples:** Modern multicore CPUs, clusters, distributed systems.

```
IS1 → [CU1] → [PU1] → DS1
IS2 → [CU2] → [PU2] → DS2
...
ISn → [CUn] → [PUn] → DSn
```

### Other Flynn Categories (brief):
- **SIMD:** Single instruction stream, multiple data streams — same instruction applied to multiple data elements (vector processors, GPUs).
- **MISD:** Multiple instruction streams, single data stream — rarely used; pipelining is an approximation.

---

## Q6. Parallel vs. Distributed Computing 🔴🔴
**(Appeared: 2022, 2023, 2026, Faculty)**

| Aspect | Parallel Computing | Distributed Computing |
|--------|-------------------|----------------------|
| **Definition** | Multiple processing elements within a single system working on a problem simultaneously | Collection of independent computers that appear as a single coherent system |
| **Hardware** | Tightly coupled; shared memory or interconnected processors | Loosely coupled; independent computers connected via network |
| **Memory** | Shared memory (common) or distributed memory | Each computer has its own local memory |
| **Communication** | Shared variables / shared memory / high-speed bus | Message passing over network (TCP/IP, RPC) |
| **Goal** | Speed up computation of a single problem | Resource sharing, reliability, geographic distribution |
| **Homogeneity** | Typically homogeneous hardware | Heterogeneous hardware and software |
| **Location** | Usually in same machine/rack | Geographically dispersed |
| **Failure** | Single point of failure possible | Partial failures; no single point of failure |
| **Examples** | Multicore CPUs, GPUs, supercomputers | Internet, cloud computing, web services |
| **Scalability** | Limited by physical hardware | Highly scalable by adding more machines |

---

## Q7. Explain Elements of Distributed Computing 🔴🔴
**(Appeared: 2023, 2026, Faculty)**

### Definition (Tanenbaum):
> "A distributed system is a collection of independent computers that appears to its users as a single coherent system."

### Components of a Distributed System (Layered View):

```
┌──────────────────────────┐
│    Applications          │ ← Social networks, enterprise apps
├──────────────────────────┤
│    Middleware             │ ← Frameworks for distributed programming
├──────────────────────────┤
│    Operating System       │ ← IPC primitives, process management
├──────────────────────────┤
│    Hardware               │ ← Networking and parallel hardware
└──────────────────────────┘
```

1. **Hardware Layer:** Computer and network hardware — the physical infrastructure. Directly managed by the OS.

2. **Operating System Layer:** Provides basic services — IPC (interprocess communication), process scheduling and management, resource management (file system, local devices). Together with hardware, these form the **platform**.

3. **Middleware Layer:** Leverages OS services to build a **uniform environment** for development and deployment of distributed applications. Provides programming paradigms, develops protocols, data formats, and frameworks. Hides heterogeneity of bottom layers.

4. **Applications Layer:** Designed to use the middleware. Expose features via GUIs accessible locally or through the Internet via Web browsers.

### Architectural Styles:
- **Software Architectural Styles:** Data-centered, Data-flow, Virtual machine, Call & return, Independent components.
- **System Architectural Styles:** Client/Server, Peer-to-Peer.

### Client/Server Model:
- Two components: **server** (processes requests) and **client** (issues requests).
- Communication through network — client sends request, server returns response.
- Types: Thin-client (processing on server) vs. Fat-client (processing on client).
- Tiers: Two-tier, Three-tier, N-tier architectures.

### Peer-to-Peer Model:
- Symmetric architecture — all components (peers) play the same role.
- Each peer acts as **both client and server**.
- Suitable for highly decentralized, scalable architectures.
- Examples: BitTorrent, Gnutella, Skype.

---

## Q8. Elements of Parallel Computing 🟡

### Hardware Architectures (Flynn's Classification):
- **SISD:** Single instruction, single data — sequential processing.
- **SIMD:** Single instruction, multiple data — vector processors, GPUs.
- **MISD:** Multiple instruction, single data — rare, pipelining approximation.
- **MIMD:** Multiple instruction, multiple data — most general parallel form.

### Approaches to Parallel Programming:
1. **Data Parallelism:** Divide-and-conquer — split data into sets, each processed on different PEs using same instruction (SIMD-suitable).
2. **Process Parallelism:** Multiple distinct activities processed on multiple processors.
3. **Farmer-and-Worker Model:** Master processor assigns jobs to slave PEs; slaves report results back to master.

### Levels of Parallelism:

| Grain Size | Code Item | Parallelized By |
|-----------|-----------|-----------------|
| Large | Separate heavyweight process | Programmer |
| Medium | Function or procedure | Programmer |
| Fine | Loop or instruction block | Parallelizing compiler |
| Very Fine | Instruction | Processor |

---

# UNIT 3 — Virtualization

---

## Q9. Characteristics of Virtualized Environments 🔴🔴🔴
**(Appeared: 2020, 2021, 2023, 2026 — HIGHEST REPEAT)**

In a virtualized environment, there are three major components: **Guest** (system interacting with virtualization layer), **Host** (original environment), and **Virtualization Layer** (recreates the environment).

### 1. Increased Security:
- Virtual machine represents an emulated environment; all guest operations are performed against the VM.
- VMM can control and filter guest activity, preventing harmful operations.
- Host resources can be hidden or protected from the guest.
- Example: Java applets run in a sandboxed JVM with limited OS access.

### 2. Managed Execution:
Four key features enabled:

- **Sharing:** Create separate computing environments within the same host. Fully exploit capabilities of a powerful host (important in virtualized data centers to reduce active servers).
- **Aggregation:** Opposite of sharing — group of separate hosts tied together and represented as a single virtual host (e.g., cluster management software).
- **Emulation:** Virtualization layer can emulate a completely different environment from the host. Useful for testing across different platforms and running legacy software.
- **Isolation:** Guests get completely separate environments. Multiple guests run on the same host without interfering with each other. VM filters activity and prevents harmful operations against the host.

Additional capabilities: **Performance tuning** (control QoS by tuning virtual resource properties), **State capture** (save/restore VM state for migration).

### 3. Portability:
- Virtual machine instances represented by files — easily transported.
- Self-contained; no dependencies besides VMM.
- Java programs: "compile once, run everywhere."
- Enables **VM migration** in server consolidation scenarios.

---

## Q10. Pros and Cons of Virtualization 🔴🔴🔴
**(Appeared: 2021, 2022, 2023, 2026 — HIGHEST REPEAT)**

### Advantages:

1. **Managed Execution and Isolation:** Build secure, controllable computing environments. Virtual environment configured as sandbox prevents harmful operations.
2. **Resource Allocation:** Fine-tuning of resources simplified — important for server consolidation and QoS.
3. **Portability:** VM instances are files — easily transported and self-contained. Java: "compiled once, run everywhere."
4. **Reduced Maintenance Costs:** Fewer physical hosts to maintain. Limited opportunity for guest to damage underlying hardware.
5. **Efficient Resource Use:** Multiple systems coexist and share host resources securely. Enables **server consolidation** — dynamically adjust active physical resources based on current load, saving energy.

### Disadvantages:

1. **Performance Degradation:** Abstraction layer between guest and host causes increased latencies. Caused by: maintaining virtual processor status, support of privileged instructions, support of paging within VM, console functions. In programming-level VMs (Java, .NET): binary translation and interpretation slow execution.

2. **Inefficiency and Degraded User Experience:** Some host features cannot be exposed by abstraction layer (e.g., advanced GPU features). In programming VMs, some OS features become inaccessible without specific libraries.

3. **Security Holes and New Threats:** New forms of phishing — malicious programs emulate hosts transparently. Malware can preload before OS and act as thin VMM (examples: **BluePill**, **SubVirt**). Modified runtime environments can access sensitive information.

---

## Q11. Taxonomy of Virtualization Techniques 🔴🔴
**(Appeared: 2022, 2023, 2026, Faculty)**

### Classification by What is Virtualized:
1. **Execution Virtualization** (most popular — further divided below)
2. **Storage Virtualization** — decouples physical storage from logical representation (SANs).
3. **Network Virtualization** — creates virtual networks (VLANs, VPNs).

### Execution Virtualization — Two Major Categories:

#### A. Process-Level (runs on top of existing OS):
- **High-Level Language VMs (Programming Language-Level):** Execute byte code (Java VM, .NET CLR). Provide portability, managed execution, sandboxing.
- **Application-Level Virtualization:** Run apps in environments that don't natively support them (Wine, CrossOver).

#### B. System-Level (runs directly on hardware):
- **Hardware-Assisted Virtualization:** Hardware provides architectural support for VMM (Intel VT, AMD-V).
- **Full Virtualization:** Complete emulation of hardware — guest OS runs unmodified. VMM emulates entire underlying hardware (VMware).
- **Paravirtualization:** Modified guest OS — exposes software interface slightly different from host. Performance-critical operations executed directly on host. Guest must be modified with **hypercalls** (Xen).
- **Partial Virtualization:** Incomplete hardware emulation — not all OS features supported (e.g., address space virtualization).
- **OS-Level Virtualization:** Single OS kernel creates multiple isolated user-space instances. No hypervisor — OS kernel isolates instances. Examples: FreeBSD Jails, Solaris Zones, Docker containers.

### Hypervisor Types:
- **Type I (Native/Bare-Metal):** Runs directly on hardware. Takes place of OS. Example: Xen, VMware ESXi.
- **Type II (Hosted):** Runs on top of host OS. Example: VMware Workstation, VirtualBox.

---

## Q12. Virtualization and Cloud Computing 🟡
**(Appeared: 2023)**

Virtualization plays a critical role in cloud computing by providing:

- **Customization:** Configurable computing environments delivered on demand.
- **Security & Isolation:** Workload partitioning, application sandboxing.
- **Manageability:** Virtual machines as controllable, portable units.

### Role in Cloud Service Models:
- **IaaS:** Hardware virtualization enables provisioning of VMs on demand (hypervisors manage resource pools).
- **PaaS:** Programming-language virtualization (Java VM, .NET) provides portable runtime environments.

### Server Consolidation:
- Aggregating multiple VMs onto fewer physical servers.
- Reduces active servers, power consumption, and cooling costs.
- **VM Migration:** Moving VM instances between physical hosts (live migration = no service interruption).

### Desktop Virtualization in Cloud:
- Cloud recreates the entire computing stack on demand.
- Virtual desktop hosted on provider infrastructure, accessed via thin client over Internet.

---

# UNIT 4 — Cloud Architecture & Deployment

---

## Q13. Types of Clouds / Cloud Deployment Models 🔴🔴🔴
**(Appeared: 2021, 2022, 2023, 2026 — HIGHEST REPEAT)**

### 1. Public Cloud:
- **Open to the wider public** — services available to anyone, from anywhere, through the Internet.
- Distributed system (one or more datacenters), serving multiple users.
- Key feature: **Multitenancy** — multiple users served with isolated virtual environments.
- QoS management, monitoring, billing, and usage history are fundamental.
- Pay-as-you-go pricing model.
- **Advantages:** No capital investment, elastic scaling, handles peak loads.
- **Disadvantages:** Security concerns (loss of control), data location/jurisdiction issues.
- **Examples:** Amazon EC2, Google AppEngine, SalesForce.com.

### 2. Private Cloud:
- Implemented **within the private premises** of an institution; accessible only to members.
- Uses existing IT infrastructure (datacenter, cluster, desktop grid).
- **Advantages:** Keeps operations in-house, security (sensitive data stays within premises), better resource utilization, lower testing costs.
- **Disadvantages:** Limited scalability compared to public clouds, requires capital investment.
- Key Advantages per Forrester Report:
  - Customer information protection
  - Infrastructure ensuring SLAs
  - Compliance with standard procedures
- **Technology Stack:** VM technologies (Xen, KVM, VMware) + management software (Eucalyptus, OpenNebula, VMware vCloud).

### 3. Hybrid Cloud (Heterogeneous Cloud):
- **Combination of private and public** cloud — private cloud augmented with public cloud resources.
- Enterprises keep sensitive operations in private cloud, burst to public cloud for peak loads.
- **Dynamic Provisioning / Cloudbursting:** Temporarily lease public resources for exceeding capacity demand, then release.
- **Advantages:** Best of both worlds — security of private + scalability of public.
- **Disadvantages:** Complex scheduling, budget optimization required.
- **Examples:** OpenNebula with Amazon EC2 integration.

### 4. Community Cloud:
- **Shared by several organizations** supporting a specific community with shared concerns (mission, security, policy, compliance).
- Multi-administrative domain — different organizations contribute resources.
- May be managed by the organizations or a third party; on-premise or off-premise.
- **Benefits:** Openness, community-based scaling, graceful failures (no single point of failure), environmental sustainability.
- **Candidate Sectors:** Media industry, Healthcare, Energy, Public sector, Scientific research.

---

## Q14. Cloud Computing Architecture 🔴🔴🔴
**(Appeared: 2021, 2022, 2023, 2026)**

*See Q3 (Cloud Reference Model) above — this is the same question phrased differently.*

**Additional Points:**

### IaaS Architecture Components:
```
┌─────────────────────────────┐
│  Web-Based Management UI   │ ← Web Services, Portals, REST API
├─────────────────────────────┤
│  Infrastructure Mgmt SW    │ ← Pricing/Billing, QoS/SLA, Scheduling
│                             │    Provisioning, Monitoring, Reservation
│                             │    VM Image Repository, VM Pool Mgmt
├─────────────────────────────┤
│  Physical Infrastructure   │ ← Datacenter, Cluster, Desktop Grid
└─────────────────────────────┘
```

### PaaS Architecture Components:
```
┌─────────────────────────────┐
│  Web-Based Interface        │ ← Web Services, Portals, REST API
├─────────────────────────────┤
│  PaaS Core Middleware       │ ← Programming API/Libraries
│                             │    Elasticity & Scaling, Runtime
│                             │    Application Mgmt, User Mgmt
│                             │    Resources Mgmt, QoS/SLA/Billing
├─────────────────────────────┤
│  Physical Infrastructure   │ ← IaaS Providers
└─────────────────────────────┘
```

---

## Q15. Economics of the Cloud 🟡
**(Appeared: 2023, 2026)**

### Cloud computing allows:
1. **Reducing capital costs** associated with IT infrastructure.
2. **Eliminating depreciation/lifetime costs** associated with IT capital assets.
3. **Replacing software licensing** with subscriptions.
4. **Cutting maintenance and administrative costs** of IT resources.

### Key Economic Concepts:
- **Capital Cost:** One-time expense purchasing assets for long-term use (hardware, software).
- **Operational Cost:** Ongoing expenses for running business (cloud subscription fees).
- **Cloud shifts CAPEX → OPEX:** Instead of buying servers, you rent them monthly.
- **Pay-as-you-go (PAYG):** Pay only for resources consumed — no waste.
- **Economy of Scale:** Large providers (AWS, Google) can offer lower prices because they operate at massive scale.
- **Elasticity = Cost Efficiency:** Scale up during peak demand, scale down during low demand — no idle resources.

### Benefits for Different Organizations:
- **Start-ups:** No large up-front IT investment; grow infrastructure with revenue.
- **Enterprises:** Reduce IT department size; shift maintenance burden to provider.
- **Variable Workloads:** Only pay for peak capacity when needed (cloudbursting).

---

# 📌 MCQ QUICK-FIRE REVISION

| Fact | Answer |
|------|--------|
| X in XaaS stands for | **Everything** |
| HaaS is also known as | **IaaS** |
| CSP stands for | **Cloud Service Provider** |
| Farmer-and-worker = | **Job distribution model** |
| Most refined/restrictive service model | **SaaS** |
| Cloud shared between organizations with common goal | **Community Cloud** |
| API = | **Application Programming Interface** |
| Most important concern in cloud computing | **Security** |
| "Cloud" represents | **Internet** |
| AWS = | **Amazon Web Services** |
| PaaS = | **Platform as a Service** |
| MIMD = | **Multiple Instruction Multiple Data** |
| Virtualization hypervisor provides | **Isolation and Partitioning** |
| Full virtualization possible in | **Type I hypervisor** |
| Vendor lock-in associated with | **PaaS** |
| Private cloud allows access within | **An organization** |
| Not a type of cloud server | **Merged Cloud Server** |

---

*Best of luck! Focus on the 🔴🔴🔴 topics first — they're almost guaranteed to appear.*
