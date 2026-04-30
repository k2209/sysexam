# CC-303 Computer Networks — Exam Study Guide (Part 1)
## 🎯 Based on Tanenbaum's Computer Networks + 5 Years of Papers

> **Priority Key:** 🔴 = Asked Every Year | 🟡 = Asked 3-4 Times | 🟢 = Asked 1-2 Times

---

# TOPIC 1: OSI MODEL 🔴
**Asked: 2021, 2022, 2023, 2024, 2026 — EVERY YEAR (7-10 marks)**

## Q: Explain the OSI Reference Model in detail with layer functions.

The **OSI (Open Systems Interconnection)** model is a 7-layer framework by ISO for understanding how data moves across a network. Each layer serves the layer above it and is served by the layer below it.

### The 7 Layers (Bottom to Top):

| Layer | Name | Function | PDU | Key Protocols/Devices |
|-------|------|----------|-----|----------------------|
| 1 | **Physical** | Transmits raw bits over a physical medium. Deals with voltages, pin layout, cabling, modulation | Bits | Hubs, Repeaters |
| 2 | **Data Link** | Frames data, provides error detection/correction, flow control, MAC addressing | Frames | Bridges, Switches, Ethernet |
| 3 | **Network** | Routing & logical addressing (IP). Determines best path for packets | Packets | Routers, IP |
| 4 | **Transport** | End-to-end delivery, segmentation, flow control, error recovery | Segments | TCP, UDP |
| 5 | **Session** | Establishes, manages, terminates sessions between applications | Data | NetBIOS |
| 6 | **Presentation** | Data translation, encryption, compression (e.g., JPEG, ASCII, SSL) | Data | SSL/TLS |
| 7 | **Application** | Interface for user applications to access network services | Data | HTTP, FTP, SMTP, DNS |

### Key Design Principles of OSI:
1. Each layer performs a well-defined function
2. Layer boundaries minimize information flow across interfaces
3. Enough layers to keep distinct functions separate, but not so many as to be unwieldy
4. Services define what a layer does; protocols define how it does it

### Service Primitives (from Tanenbaum):
- **LISTEN** – Wait for an incoming connection
- **CONNECT** – Establish a connection
- **ACCEPT** – Accept an incoming connection
- **RECEIVE** – Wait for an incoming message
- **SEND** – Send a message
- **DISCONNECT** – Terminate a connection

---

# TOPIC 2: TCP/IP MODEL 🔴
**Asked: 2021, 2022, 2023, 2024, 2026**

## Q: Explain the TCP/IP Reference Model in detail.

The **TCP/IP model** was developed by the U.S. Department of Defense (ARPANET). It has **4 layers** and is the practical model used on the Internet.

### The 4 Layers:

| Layer | Name | Function | Equivalent OSI Layers |
|-------|------|----------|-----------------------|
| 1 | **Link (Host-to-Network)** | Handles physical transmission, framing, access to medium | Physical + Data Link |
| 2 | **Internet** | Defines IP packet format and routing. Uses IP protocol. Permits hosts to inject packets and have them travel independently (datagram) | Network |
| 3 | **Transport** | End-to-end communication. Two protocols: **TCP** (reliable, connection-oriented) and **UDP** (unreliable, connectionless) | Transport |
| 4 | **Application** | Contains all higher-level protocols: HTTP, FTP, SMTP, DNS, Telnet | Session + Presentation + Application |

### Key Features:
- Designed for **survivability**: if some routers/links fail, existing connections continue to work
- **Connectionless Internet layer**: each packet is routed independently (no pre-established path)
- TCP provides: sequencing, flow control, error recovery, congestion control
- UDP provides: fast, low-overhead delivery for applications like DNS, video streaming

### OSI vs TCP/IP Comparison:

| Feature | OSI Model | TCP/IP Model |
|---------|-----------|-------------|
| Layers | 7 | 4 |
| Developed by | ISO | DoD/ARPANET |
| Approach | Theoretical (model first) | Practical (protocols first) |
| Session/Presentation | Separate layers | Merged into Application |
| Network Layer | Connection + connectionless | Connectionless (IP) |
| Transport Layer | Connection-oriented only | Both TCP and UDP |
| Usage | Reference/teaching | Actual Internet |

---

# TOPIC 3: NETWORK TOPOLOGIES 🔴
**Asked: 2021, 2022, 2023, 2024, 2026 — EVERY YEAR**

## Q: What is Network Topology? Explain types with diagrams.

**Network Topology** is the geometric arrangement of how devices (nodes) are connected in a network. It affects performance, reliability, and cost.

### 1. Mesh Topology
- **Every device has a dedicated point-to-point link to every other device**
- For n devices: **n(n-1)/2** links needed (full mesh)
- ✅ Robust — fault tolerant, privacy, easy fault identification
- ❌ Very expensive — too many cables and I/O ports
- Used in: Backbone connections, military

### 2. Star Topology
- **All devices connect to a central hub/switch**
- Each device has a dedicated point-to-point link to the hub
- ✅ Easy to install, add devices, and troubleshoot; one link failure doesn't affect others
- ❌ Central hub is single point of failure; more cabling than bus/ring
- Used in: Modern LANs (Ethernet with switches)

### 3. Bus Topology
- **All devices attach to a single backbone cable**
- Uses taps and drop lines to connect devices
- Signal travels in both directions; terminators at both ends absorb signal
- ✅ Easy to install, less cabling
- ❌ Difficult to add devices, fault diagnosis is hard, cable break stops entire network
- Used in: Classic Ethernet (10BASE5, 10BASE2)

### 4. Ring Topology
- **Each device connects to exactly two neighbors, forming a circle**
- Signal travels in one direction; each device has a repeater
- A token circulates; a device can send data only when it has the token
- ✅ Easy to install, orderly access
- ❌ One break can disable entire network (unless dual ring)
- Used in: Token Ring, FDDI

### 5. Hybrid Topology
- Combination of two or more topologies (e.g., Star-Bus, Star-Ring)
- Used in: Most real-world enterprise networks

---

# TOPIC 4: MULTIPLEXING (FDM, TDM, WDM) 🔴
**Asked: 2021, 2022, 2023, 2024, 2026 — EVERY YEAR**

## Q: What is Multiplexing? Explain FDM, TDM, and WDM.

**Multiplexing** is a technique that allows multiple signals to share a single communication link, increasing efficiency.

### 1. Frequency Division Multiplexing (FDM)
- The **bandwidth of a link is divided into frequency bands**
- Each signal is modulated onto a **different carrier frequency**
- **Guard bands** separate adjacent channels to prevent interference
- All signals are transmitted **simultaneously** on different frequencies
- **Example:** Radio broadcasting (each station gets a frequency), cable TV, ADSL
- Analog technique

### 2. Time Division Multiplexing (TDM)
- **Each signal gets the full bandwidth but only for a fixed time slot**
- Time is divided into frames; each frame has one slot per input
- Two types:
  - **Synchronous TDM**: Fixed time slots assigned in round-robin (slots may go empty if source has no data)
  - **Statistical (Asynchronous) TDM**: Slots are dynamically allocated only to active sources — more efficient
- **Digital technique**; each connection gets a turn
- **Example:** T1 line (24 channels × 8 bits = 192 bits + 1 framing bit = 193 bits per frame, 8000 frames/sec = 1.544 Mbps)

### 3. Wavelength Division Multiplexing (WDM)
- **FDM applied to optical fiber**
- Multiple light beams of **different wavelengths (colors)** travel through a single fiber simultaneously
- Each wavelength carries an independent data channel
- Uses a prism or diffraction grating to combine/separate wavelengths
- **Dense WDM (DWDM)**: more channels, tighter spacing — up to 160+ channels per fiber
- **Example:** Long-distance fiber optic backbone networks

### Comparison:

| Feature | FDM | TDM | WDM |
|---------|-----|-----|-----|
| Division by | Frequency | Time | Wavelength (light color) |
| Medium | Analog links | Digital links | Optical fiber |
| Simultaneous? | Yes | No (takes turns) | Yes |
| Guard needed? | Guard bands | Sync bits | Guard wavelengths |

---

# TOPIC 5: GUIDED TRANSMISSION MEDIA 🔴
**Asked: 2021, 2022, 2023, 2024, 2026**

## Q: What is Transmission Media? Explain Guided Media types.

**Transmission media** are the physical paths between transmitter and receiver. **Guided media** use a physical conductor to guide signals.

### 1. Twisted Pair Cable
- **Two insulated copper wires twisted helically** (like DNA)
- Twisting reduces electromagnetic interference (crosstalk)
- Signal carried as **difference in voltage** between the two wires
- **Types:**
  - **UTP (Unshielded Twisted Pair):** No shielding, cheaper. Categories: Cat 3, Cat 5, Cat 5e, Cat 6, Cat 7
  - **STP (Shielded Twisted Pair):** Metal foil/braid shielding around pairs. Better noise immunity but expensive
- Cat 5: up to 100 Mbps; Cat 6: up to 10 Gbps
- **Duplex types:** Full-duplex (simultaneous both ways), Half-duplex (one at a time), Simplex (one way only)
- **Used in:** Telephone lines, Ethernet LANs (most common LAN medium)

### 2. Coaxial Cable
- **Structure:** Central copper core → insulator → braided metal shield → plastic sheath
- Better shielding & higher bandwidth than twisted pair
- **Two types:** 50-ohm (digital, Ethernet) and 75-ohm (analog, cable TV)
- Bandwidth up to several GHz
- Less susceptible to interference than twisted pair
- **Used in:** Cable TV, older Ethernet (10BASE5, 10BASE2), metropolitan area networks

### 3. Fiber Optic Cable
- Transmits data as **pulses of light** through thin glass/plastic fiber
- Principle: **Total Internal Reflection** — light above critical angle is trapped inside the fiber core
- **Components:** Light source (LED/Laser) → Fiber → Detector (photodiode)
- **Types:**
  - **Multimode fiber:** Wider core (~50μm), multiple light paths (modes), used for shorter distances
  - **Single-mode fiber:** Very narrow core (~8μm), one light path, used for long distances (100+ km)
- **Advantages:** Enormous bandwidth (50 Tbps potential), low attenuation, immune to electromagnetic interference, lightweight, secure (hard to tap)
- **Disadvantages:** Expensive, fragile, requires skilled installation
- **Used in:** Long-haul backbone networks, FttH (Fiber to the Home), high-speed LANs

### Comparison Table:

| Feature | Twisted Pair | Coaxial | Fiber Optic |
|---------|-------------|---------|-------------|
| Bandwidth | Low-Moderate | Moderate-High | Very High |
| Distance | Short (~100m for LAN) | Medium | Very Long |
| Cost | Cheapest | Moderate | Most Expensive |
| EMI Immunity | Low | Moderate | Complete |
| Installation | Easy | Moderate | Difficult |

---

# TOPIC 6: MODULATION TECHNIQUES 🟡
**Asked: 2021, 2022, 2023**

## Q: What are Modulation Techniques? Explain ASK, FSK, PSK.

**Modulation** is the process of converting digital data into analog signals for transmission. A carrier signal's property (amplitude, frequency, or phase) is varied according to the data.

### 1. Amplitude Shift Keying (ASK)
- **Amplitude of carrier is varied** to represent 0s and 1s
- Binary 1 = full amplitude carrier; Binary 0 = zero amplitude (or reduced)
- Simple but **susceptible to noise** (noise directly affects amplitude)
- Used in: Fiber optic transmission (on/off keying)

### 2. Frequency Shift Keying (FSK)
- **Frequency of carrier is varied**
- Binary 1 = higher frequency; Binary 0 = lower frequency
- More robust against noise than ASK
- Used in: Early modems, radio transmission

### 3. Phase Shift Keying (PSK)
- **Phase of carrier is varied**
- Binary 0 = 0° phase; Binary 1 = 180° phase shift
- More efficient and less error-prone than ASK and FSK
- **QPSK** uses 4 phase shifts (0°, 90°, 180°, 270°) to encode 2 bits per symbol
- Used in: Wi-Fi, satellite communication

### 4. QAM (Quadrature Amplitude Modulation)
- Combines **both amplitude and phase** modulation
- Multiple signal points on a constellation diagram
- **16-QAM** = 4 bits/symbol, **64-QAM** = 6 bits/symbol
- Used in: ADSL, cable modems, Wi-Fi (802.11)

---

# TOPIC 7: COMMUNICATION MODES 🟡
**Asked: 2021, 2022, 2023, 2024**

## Q: Explain Simplex, Half-Duplex, and Full-Duplex Communication.

| Mode | Direction | Analogy | Example |
|------|-----------|---------|---------|
| **Simplex** | One-way only | One-way street | TV broadcast, keyboard to CPU |
| **Half-Duplex** | Both ways, but one at a time | Single-track railway | Walkie-talkie, old hub Ethernet |
| **Full-Duplex** | Both ways simultaneously | Two-lane road | Telephone, modern Ethernet |

## Q: Explain Serial and Parallel Communication.

- **Parallel:** Multiple bits sent simultaneously on separate wires. Fast but expensive and limited distance (clock skew). Example: Old printer cables
- **Serial:** One bit at a time on a single wire. Slower but reliable over long distances. Example: USB, Ethernet

## Q: Explain Synchronous and Asynchronous Communication.

- **Synchronous:** Data sent in continuous blocks. Sender & receiver share a common clock. More efficient. Example: HDLC, Ethernet
- **Asynchronous:** Data sent character by character with start/stop bits. No shared clock. Simpler. Example: RS-232, old modems

---

# TOPIC 8: ERROR DETECTION & CORRECTION 🔴
**Asked: 2021, 2022, 2023, 2024, 2026**

## Q: Explain Parity Check with example.

**Parity check** appends a single parity bit to make the total number of 1-bits even (even parity) or odd (odd parity).

- **Even parity example:** Data = 1011010 → count of 1s = 4 (even) → parity bit = 0 → Sent: 10110100
- **Odd parity example:** Data = 1011010 → parity bit = 1 → Sent: 10110101
- **Detects:** Single-bit errors (Hamming distance = 2)
- **Cannot detect:** Even number of bit errors
- **Interleaving** can extend parity to detect burst errors

## Q: Explain CRC (Cyclic Redundancy Check).

CRC is the most powerful error-detecting code used at the data link layer.

### How CRC Works:
1. Sender and receiver agree on a **generator polynomial G(x)** (e.g., x⁴ + x + 1 = 10011)
2. Let r = degree of G(x). Append **r zero bits** to the data frame
3. **Divide** the extended frame by G(x) using **modulo-2 division** (XOR)
4. **Subtract** (XOR) the remainder from the extended frame → this is the transmitted frame T(x)
5. Receiver divides received frame by G(x). If **remainder = 0**, no error detected

### Example:
- Frame: 1101011111, Generator: 10011 (degree 4)
- Append 4 zeros: 11010111110000
- Divide by 10011 using XOR → Remainder: 1110
- Transmitted frame: **11010111111110**

### CRC Properties:
- Detects **all single-bit errors**
- Detects **all double-bit errors** (with proper G(x))
- Detects **all odd number of bit errors** (if G(x) has x+1 as factor)
- Detects **all burst errors of length ≤ r**
- CRC-32 (used in Ethernet/IEEE 802): detects all bursts of length 32 or less

## Q: Explain Checksum.

- Group of check bits computed from message data
- **Internet checksum:** 16-bit one's complement sum of all 16-bit words in the message
- Sender computes checksum and appends it; receiver recomputes — if sum = 0, no error
- Simpler than CRC but weaker error detection

---

# TOPIC 9: ERROR RECOVERY / SLIDING WINDOW 🔴
**Asked: 2021, 2022, 2023, 2024, 2026**

## Q: Explain Stop-and-Wait Protocol.

- Sender sends **one frame** and then **waits** for ACK
- If ACK received → send next frame
- If timeout (no ACK) → retransmit the same frame
- Uses **1-bit sequence number** (0 or 1) to detect duplicates
- Also called **ARQ (Automatic Repeat reQuest)**
- **Simple but inefficient** — sender is idle while waiting, wastes channel capacity on long links

## Q: Explain Sliding Window Protocol.

A family of protocols where the sender can send **multiple frames before needing ACK**.

### Key Concepts:
- **Sender maintains a sending window:** set of sequence numbers it is allowed to send
- **Receiver maintains a receiving window:** set of sequence numbers it can accept
- As ACKs arrive, windows **slide forward**
- Window size of 1 = Stop-and-Wait
- **Piggybacking:** ACK is attached to the next outgoing data frame (saves bandwidth)

### Advantages:
- Much better link utilization than Stop-and-Wait
- Bidirectional data flow
- Piggybacking reduces overhead

## Q: Explain Go-Back-N Protocol.

- Sender can send up to **N frames** without waiting for ACK (window size = N)
- Receiver only accepts frames **in order** — if frame i is lost, all subsequent frames (i+1, i+2, ...) are **discarded** even if received correctly
- On timeout for frame i, sender **retransmits frame i and all subsequent frames**
- Receiver window size = **1** (accepts only next expected frame)
- Simpler receiver logic but **wastes bandwidth** on retransmissions

## Q: Explain Selective Repeat Protocol.

- Sender can send up to N frames; receiver **buffers out-of-order frames**
- Only the **specific damaged/lost frame** is retransmitted
- Receiver window size > 1
- More complex but **more efficient** than Go-Back-N
- Receiver must have buffer space for out-of-order frames

### Comparison:

| Feature | Stop-and-Wait | Go-Back-N | Selective Repeat |
|---------|--------------|-----------|-----------------|
| Window (Sender) | 1 | N | N |
| Window (Receiver) | 1 | 1 | N |
| Retransmit | 1 frame | All from error | Only error frame |
| Efficiency | Low | Medium | High |
| Complexity | Simple | Medium | Complex |

---

# TOPIC 10: SWITCHING TECHNIQUES 🟡
**Asked: 2022, 2023, 2024, 2026**

## Q: Explain Circuit Switching and Packet Switching.

### Circuit Switching:
- A **dedicated physical path** is established before communication begins
- Path remains reserved for the **entire duration** of the communication
- Three phases: **Connection setup → Data transfer → Disconnection**
- **No congestion** during transfer (bandwidth reserved), but bandwidth is **wasted** if idle
- **Constant delay** (only propagation delay once connected)
- **Example:** Traditional telephone system (PSTN)

### Packet Switching:
- Data is broken into **packets** that are sent independently
- Each packet may take a **different route** to the destination
- Uses **store-and-forward**: each router receives entire packet, checks it, then forwards
- **No dedicated path** — bandwidth shared among users
- **More efficient** (no wasted bandwidth) but variable delay (**queuing delay**)
- **Two approaches:**

| Feature | Datagram | Virtual Circuit |
|---------|----------|----------------|
| Setup | No setup needed | Connection setup required |
| Routing | Each packet routed independently | Fixed path for session |
| Order | Packets may arrive out of order | In-order delivery |
| Failure | Packets rerouted around failures | Path must be re-established |
| Example | IP (Internet) | ATM, X.25 |

### Circuit vs Packet Switching Summary:

| Feature | Circuit Switching | Packet Switching |
|---------|------------------|-----------------|
| Path | Dedicated | Shared |
| Bandwidth | Reserved (may waste) | Shared (efficient) |
| Delay | Fixed, low | Variable (queuing) |
| Congestion | At setup time | During transmission |
| Fault tolerance | Poor (path fails) | Good (rerouting) |
| Best for | Voice calls | Data/Internet |
