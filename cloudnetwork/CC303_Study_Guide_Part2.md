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
