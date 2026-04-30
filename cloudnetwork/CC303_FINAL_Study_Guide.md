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
# CC-303 Computer Networks — Exam Study Guide (Part 2)
## 🎯 Remaining High-Frequency Topics

---

# TOPIC 11: SATELLITE COMMUNICATION 🟡
**Asked: 2022, 2023, 2024**

## Q: Discuss Satellite Communication in detail.

A **communication satellite** is a microwave repeater in the sky. It contains **transponders** that receive signals from earth (uplink), amplify them, and rebroadcast at a different frequency (downlink). This avoids interference between incoming and outgoing signals — called **bent pipe** operation.

### Three Types of Orbits:

| Type | Altitude | Period | Latency (RTT) | Satellites for Global Coverage |
|------|----------|--------|---------------|-------------------------------|
| **GEO** | 35,800 km | 24 hours (stationary) | ~270 ms | 3 |
| **MEO** | 5,000–35,000 km | ~6 hours | 35–85 ms | 10 |
| **LEO** | 160–2,000 km | ~90 min | 1–7 ms | 50+ |

### GEO (Geostationary Earth Orbit):
- Appears **stationary** over one point on equator — no tracking needed
- Only ~180 satellites possible (2° spacing in 360° orbit)
- High latency (540 ms round trip)
- Large footprint — one satellite covers ~1/3 of Earth
- **VSAT (Very Small Aperture Terminal):** small 1m dish for end users; often uses a hub for relay
- Applications: TV broadcasting, weather, long-distance telephony

### LEO (Low Earth Orbit):
- Moves fast — needs constellation of satellites (e.g., Iridium uses 66 satellites)
- Very low latency (~1–7 ms)
- Cheaper to launch, lower power needed
- **Iridium:** 66 satellites in polar orbits, can relay calls satellite-to-satellite
- **Globalstar:** 48 satellites, bent-pipe design (switches on ground)

### Satellite Frequency Bands:

| Band | Downlink | Uplink | Bandwidth | Issue |
|------|----------|--------|-----------|-------|
| L | 1.5 GHz | 1.6 GHz | 15 MHz | Low BW, crowded |
| C | 4.0 GHz | 6.0 GHz | 500 MHz | Terrestrial interference |
| Ku | 11 GHz | 14 GHz | 500 MHz | Rain absorption |
| Ka | 20 GHz | 30 GHz | 3500 MHz | Rain, cost |

### Satellite vs Fiber:
- Satellite: fast deployment, broadcast capability, works anywhere
- Fiber: higher bandwidth, lower latency, more secure, cheaper per bit for high volume

### FDMA, TDMA, CDMA (for exam Q on satellite channelization):
- **FDMA:** Each user gets a different frequency band (like FDM)
- **TDMA:** Users share the same frequency but take turns in time slots (like TDM)
- **CDMA:** All users transmit simultaneously on all frequencies using unique codes — receiver uses the code to extract the desired signal

---

# TOPIC 12: ETHERNET 🟡
**Asked: 2021, 2022, 2024**

## Q: What is Ethernet? Explain its properties.

**Ethernet** (IEEE 802.3) is the most widely used LAN technology, invented by Bob Metcalfe and David Boggs at Xerox PARC (1976).

### Classic Ethernet:
- Uses a **single shared coaxial cable** (bus topology)
- Speeds: 10 Mbps (original), uses **Manchester encoding**
- All stations share the same medium — **broadcast network**
- Max segment: 500m (thick), 185m (thin); up to 4 repeaters (max 2.5 km total)

### Ethernet Frame Format:

| Field | Preamble | Dest Address | Source Address | Type/Length | Data | Pad | Checksum (CRC) |
|-------|----------|-------------|---------------|-------------|------|-----|----------------|
| Bytes | 8 | 6 | 6 | 2 | 0-1500 | 0-46 | 4 |

- **Preamble:** 7 bytes of 10101010 + 1 byte of 10101011 (Start of Frame) — for clock sync
- **Addresses:** 48-bit MAC addresses (globally unique, assigned by IEEE via OUI)
- **Min frame size:** 64 bytes (to ensure collision detection works for max cable length)
- **Max data:** 1500 bytes (MTU)
- **CRC-32** checksum for error detection

### CSMA/CD (Carrier Sense Multiple Access with Collision Detection):
1. **Carrier Sense:** Station listens to the channel before transmitting
2. **Multiple Access:** Multiple stations share the same medium
3. **Collision Detection:** If collision detected, station aborts and sends a **48-bit jam signal**
4. **Binary Exponential Backoff:** After collision i, wait a random number of slots from 0 to 2^i − 1. Max i=10 (frozen at 1023 slots). After 16 collisions → failure reported.
5. **Slot time:** 51.2 μs (time to detect collision on max-length cable at 10 Mbps)

### Modern Ethernet Evolution:

| Standard | Speed | Cable | Topology |
|----------|-------|-------|----------|
| 10BASE5 | 10 Mbps | Thick coax | Bus |
| 10BASE-T | 10 Mbps | Cat 3 UTP | Star (hub) |
| 100BASE-TX | 100 Mbps | Cat 5 UTP | Star (switch) |
| 1000BASE-T | 1 Gbps | Cat 5e/6 UTP | Star (switch) |
| 10GBASE-T | 10 Gbps | Cat 6a/7 | Star (switch) |

### Key Properties:
- Connectionless and unreliable at data link layer (no ACK)
- Uses 1-persistent CSMA/CD for channel access
- Switched Ethernet (modern): full-duplex, no collisions, each port is its own collision domain
- 10-Gigabit Ethernet: no CSMA/CD needed (only full-duplex, point-to-point)

---

# TOPIC 13: ALOHA PROTOCOL 🟢
**Asked: 2026**

## Q: Explain ALOHA in detail.

### Pure ALOHA:
- Users transmit **whenever they have data** — no coordination
- If collision occurs (frames overlap), both frames are destroyed
- Sender waits a **random time** and retransmits
- **Vulnerable period:** 2 × frame time (collision possible if any other frame starts within ±1 frame time)
- **Throughput:** S = Ge^(-2G), Maximum at G=0.5 → **S = 1/2e ≈ 18.4%**

### Slotted ALOHA:
- Time divided into **fixed slots** equal to one frame time
- Stations can only begin transmitting **at the start of a slot**
- **Vulnerable period:** 1 × frame time (halved)
- **Throughput:** S = Ge^(-G), Maximum at G=1 → **S = 1/e ≈ 36.8%**
- Double the throughput of Pure ALOHA

### Comparison:

| Feature | Pure ALOHA | Slotted ALOHA |
|---------|-----------|---------------|
| Time | Continuous | Discrete slots |
| Vulnerable period | 2T | T |
| Max throughput | 18.4% | 36.8% |
| Synchronization | Not needed | Required |

---

# TOPIC 14: FDDI 🟡
**Asked: 2022, 2023, 2024**

## Q: Explain FDDI with its properties and self-healing mechanism.

**FDDI (Fiber Distributed Data Interface)** is a high-speed (100 Mbps) LAN standard using **fiber optic** cable in a **dual ring** topology.

### Properties:
- **Speed:** 100 Mbps
- **Topology:** Dual counter-rotating rings (primary and secondary)
- **Medium:** Fiber optic (CDDI variant uses copper)
- **Access method:** Token passing (timed token protocol)
- **Max circumference:** 200 km (100 km per ring)
- **Max stations:** 500
- **Frame size:** Up to 4500 bytes

### How FDDI Works:
- A **token** circulates on the primary ring
- A station captures the token, sends its data frames, then releases the token
- Unlike Token Ring, a station can send **multiple frames** before releasing token (timed token)
- Data travels on the **primary ring** under normal conditions
- The **secondary ring** is a backup (standby)

### Self-Healing Mechanism:
- Under normal operation, only the **primary ring** carries data
- If a **cable break** or **station failure** occurs, the **dual ring wraps around** the failure point
- The secondary ring connects to the primary ring at the stations adjacent to the break, forming a **single larger ring** that bypasses the fault
- This is called **ring wrap** or **self-healing**
- If **multiple breaks** occur, the ring splits into separate segments
- **Dual Attachment Stations (DAS)** connect to both rings; **Single Attachment Stations (SAS)** connect only to primary via a concentrator

### FDDI vs Ethernet:

| Feature | FDDI | Ethernet |
|---------|------|---------|
| Speed | 100 Mbps | 10 Mbps–10 Gbps |
| Medium | Fiber optic | Copper/Fiber |
| Topology | Dual ring | Bus/Star |
| Access | Token passing | CSMA/CD |
| Fault tolerance | Self-healing | No built-in |

---

# TOPIC 15: NETWORK DEVICES 🔴
**Asked: 2021, 2022, 2023, 2024 — appears in MCQs AND essays**

## Q: Explain Repeater, Hub, Bridge, Switch, Router, and Gateway.

These devices operate at **different layers of the OSI model**:

### 1. Repeater (Physical Layer — Layer 1)
- **Analog device** that receives, amplifies/regenerates, and retransmits signals
- Extends cable length by boosting signal strength
- Does NOT examine frame addresses or content
- Also called a **regenerator**
- Classic Ethernet allowed up to **4 repeaters** (max 2.5 km)

### 2. Hub (Physical Layer — Layer 1)
- Multi-port repeater — **joins lines electrically**
- Frames arriving on any port are sent out on **all other ports** (broadcast)
- Collisions can occur (single collision domain)
- Does NOT examine addresses
- All ports must operate at **same speed**

### 3. Bridge (Data Link Layer — Layer 2)
- Connects two or more LANs; examines **MAC addresses** in frame headers
- Builds a **forwarding table** (learns which addresses are on which port)
- Forwards frames only to the port where destination is located → **reduces traffic**
- Each port is a **separate collision domain**
- Can connect different speeds (e.g., 10 Mbps and 100 Mbps) using buffering
- **Used to divide a network into segments**

### 4. Switch (Data Link Layer — Layer 2)
- Modern name for a **multi-port bridge**
- Each port has its own collision domain → **full-duplex, no collisions**
- Forwards frames based on MAC address table
- Much better performance than hubs
- Supports VLANs (Virtual LANs)

### 5. Router (Network Layer — Layer 3)
- Strips data link header, examines **IP address** in packet header
- Uses routing tables/algorithms to choose the **best path** to destination
- Connects **different network types** (e.g., Ethernet to WAN)
- Does NOT look at MAC addresses for forwarding decisions
- Has **two or more NIC cards** (connects to multiple networks)

### 6. Gateway (All Layers — up to Layer 7)
- Operates at **higher layers** (transport or application)
- **Transport gateway:** Translates between different transport protocols (e.g., TCP ↔ SCTP)
- **Application gateway:** Translates data formats (e.g., email gateway converting Internet mail to SMS)
- Most complex interconnection device

### Summary Table:

| Device | Layer | Addresses Used | Function |
|--------|-------|---------------|----------|
| Repeater | 1 (Physical) | None | Amplify signal |
| Hub | 1 (Physical) | None | Broadcast to all ports |
| Bridge | 2 (Data Link) | MAC (48-bit) | Forward by MAC address |
| Switch | 2 (Data Link) | MAC (48-bit) | Multi-port bridge |
| Router | 3 (Network) | IP (32/128-bit) | Route packets between networks |
| Gateway | 5-7 | Application data | Protocol/format translation |

---

# TOPIC 16: SIGNAL FUNDAMENTALS 🟢
**Asked: 2024, 2026**

## Q: Define Period, Frequency, Phase, Bandwidth, and Bit Rate.

| Term | Definition | Unit |
|------|-----------|------|
| **Period (T)** | Time to complete one cycle of a signal | Seconds (s) |
| **Frequency (f)** | Number of cycles per second; f = 1/T | Hertz (Hz) |
| **Phase** | Position of the waveform relative to time 0 | Degrees (°) or Radians |
| **Amplitude** | Maximum strength/height of the signal | Volts |
| **Bandwidth** | Range of frequencies a signal contains (f_max − f_min) | Hz |
| **Bit Rate** | Number of bits transmitted per second | bps |
| **Baud Rate** | Number of signal changes per second (symbols/sec) | Baud |
| **Attenuation** | Loss of signal strength as it travels through medium | dB |

### Key Formulas:
- **Nyquist (noiseless channel):** Max data rate = 2B × log₂(V) bps (B = bandwidth, V = signal levels)
- **Shannon (noisy channel):** Max data rate = B × log₂(1 + S/N) bps (S/N = signal-to-noise ratio)

---

# TOPIC 17: IPv4 ADDRESSING 🟢
**Asked: 2026**

## Q: Explain IPv4 Addressing and Class A, B, C.

- IPv4 address is **32 bits** (4 bytes), written in dotted decimal (e.g., 192.168.1.1)
- Divided into **Network ID** and **Host ID**

| Class | First Bits | Range | Network Bits | Host Bits | Networks | Hosts/Network |
|-------|-----------|-------|-------------|----------|----------|--------------|
| A | 0xxx | 0.0.0.0 – 127.255.255.255 | 8 | 24 | 128 | ~16 million |
| B | 10xx | 128.0.0.0 – 191.255.255.255 | 16 | 16 | 16,384 | ~65,000 |
| C | 110x | 192.0.0.0 – 223.255.255.255 | 24 | 8 | ~2 million | 254 |
| D | 1110 | 224.0.0.0 – 239.255.255.255 | — | — | Multicast | — |
| E | 1111 | 240.0.0.0 – 255.255.255.255 | — | — | Reserved | — |

---

# 📝 MCQ QUICK REFERENCE (Section II)

These facts appear repeatedly in MCQs across all papers:

| Fact | Answer |
|------|--------|
| TCP/IP has how many layers? | **4** |
| Which multiplexing uses time slots? | **TDM** |
| Which switching uses dedicated path? | **Circuit Switching** |
| Parity check detects? | **Single-bit errors** |
| CRC stands for? | **Cyclic Redundancy Check** |
| IPv4 address size? | **32 bits** |
| HTTP is used for? | **Web page transfer** |
| SMTP is used for? | **Sending email** |
| Byte stuffing is used in? | **Framing (Data Link Layer)** |
| In mesh, each device connected to? | **Every other device** |
| Frequency measured in? | **Hertz (Hz)** |
| Loss of signal strength? | **Attenuation** |
| Signals continuous in nature? | **Analog** |
| Completion of one cycle time? | **Period** |
| Optical fibers use __ for transmission? | **Light** |
| Standard for wireless LAN? | **IEEE 802.11** |
| Operates at all 7 layers? | **Gateway** |
| LAN is a __ owned network? | **Privately** |
| De Jure standards apply because of? | **Regulations** |
| Half-duplex means? | **Both ways, not simultaneously** |
| Receiver sends __ on error? | **NAK** |
| Basic hardware of WAN? | **Packet switch (Router)** |
| PRI interface ISDN? | **23 B-channels, 1 D-channel** |
| Repeater also called? | **Regenerator** |
| LED stands for? | **Light Emitting Diode** |
| UTP stands for? | **Unshielded Twisted Pair** |
| WDM stands for? | **Wavelength Division Multiplexing** |
| BPS stands for? | **Bits Per Second** |
| Checksum also called? | **Hashsum** |
| Wi-Fi stands for? | **Wireless Fidelity** |
| Syntax defines? | **Structure/format of data** |
| TCP stands for? | **Transmission Control Protocol** |
| UHF stands for? | **Ultra-High Frequency** |
| Bridge does what? | **Divides network into segments** |
| Modem is needed for? | **Dial-up telephone connection** |
| Bridge has own processor + 2 NICs? | **(That's actually a Bridge)** |
| ISDN stands for? | **Integrated Services Digital Network** |
| Bluetooth standard? | **IEEE 802.15** |
| Message switching = ? technique | **Store and forward** |
| Handoff means? | **Signal handoff from old to new channel** |
| Infrared = ? communication | **Wireless** |
| CSMA/CD helps Ethernet recover from? | **Collision errors** |
| Most common transmission medium? | **UTP (Unshielded Twisted Pair)** |
| Delta Modulation shows? | **Difference between current and previous amplitude** |
| Router forwards packets across different networks? | **True** |
| Repeater = regenerator? | **True** |
| Two frequency bands for earth to satellite? | **True (uplink + downlink)** |
| Messages broken into smaller units called? | **Packets (not Circuit)** |

---

# 🕐 6-HOUR STUDY PLAN

| Time | Topics | Priority |
|------|--------|----------|
| **7:00–8:00** | OSI Model + TCP/IP Model + Comparison | 🔴 |
| **8:00–9:00** | Topologies + Transmission Media (Guided) | 🔴 |
| **9:00–10:00** | Multiplexing (FDM, TDM, WDM) + Switching | 🔴 |
| **10:00–11:00** | Error Detection (Parity, CRC) + Error Recovery (Stop-Wait, Sliding Window, Go-Back-N) | 🔴 |
| **11:00–12:00** | Ethernet + CSMA/CD + FDDI + Network Devices + ALOHA | 🟡 |
| **12:00–1:00** | Modulation + Communication Modes + Satellite + MCQ drill + IPv4 | 🟡🟢 |

**Good luck on your exam! 🚀**
