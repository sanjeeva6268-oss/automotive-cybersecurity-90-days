# Day 2 — Automotive Bus Protocols

## Source

Primary source: *The Car Hacker's Handbook*, Chapter 2 — **Bus Protocols**, Open Garages online edition. The chapter covers CAN, OBD-II, ISO-TP, CANopen, GMLAN, SAE J1850, LIN, MOST, FlexRay, and Automotive Ethernet. citehttps://opengarages.org/handbook/ebook/

## Objective

Understand how ECUs communicate over automotive networks and why vehicles use multiple bus technologies.

## 1. Automotive Bus Concept

A vehicle may contain multiple networks because different functions have different requirements for bandwidth, timing, cost, wiring, and reliability.

```text
                     Vehicle Gateway
                           |
          +----------------+----------------+
          |                |                |
        HS-CAN            LIN       Automotive Ethernet
          |                |                |
      Powertrain          Body          IVI / ADAS
        ECUs              ECUs            systems
```

The exact architecture varies by vehicle.

## 2. CAN — Controller Area Network

CAN is a major automotive communication protocol. The handbook describes classic CAN as a two-wire differential bus using **CANH** and **CANL**, with frames broadcast to controllers sharing the bus. citehttps://opengarages.org/handbook/ebook/

```text
CANH =====================================
CANL =====================================
       |          |          |          |
      ECU        ECU        ECU        ECU
```

### CAN frame basics

| Field | Purpose |
|---|---|
| Arbitration ID | Message identifier and arbitration priority |
| IDE | Identifier-format indication |
| DLC | Data length indication |
| Data | Payload |

Standard CAN uses an 11-bit identifier; extended CAN uses a larger identifier. Classic CAN data carries up to 8 bytes. citehttps://opengarages.org/handbook/ebook/

Example synthetic frame:

```text
0x321#1122334455667788
```

This is training data only; it is not associated with a real vehicle.

## 3. OBD-II

OBD-II is a standardized diagnostic connector. The handbook identifies **pin 6 = CANH** and **pin 14 = CANL** for commonly used high-speed CAN and notes that not every vehicle bus is exposed through OBD-II. citehttps://opengarages.org/handbook/ebook/

Security question: what networks and diagnostic functions are reachable through the vehicle gateway behind the connector?

## 4. ISO-TP

ISO-TP (ISO 15765-2) segments messages across multiple CAN frames so larger diagnostic messages can be transported beyond the single-frame CAN payload limit. It is particularly important for diagnostics and later UDS work. citehttps://opengarages.org/handbook/ebook/

```text
Large message → ISO-TP → CAN frame + CAN frame + CAN frame
```

## 5. Other Protocols

### CANopen
Application-layer protocol built on CAN; more common in industrial environments than ordinary automotive networks.

### GMLAN
A General Motors CAN-based implementation discussed in the handbook, including low-speed single-wire and high-speed dual-wire variants.

### SAE J1850
Legacy automotive communication technology with PWM and VPW variants.

### LIN
Low-cost complement to CAN. The handbook describes LIN as single-wire, master-controlled communication with up to approximately **20 Kbps**. citehttps://opengarages.org/handbook/ebook/

```text
LIN Master
    |
 +--+---------+--+
 |            |  |
Slave 1    Slave 2 Slave 3
```

### MOST
Media-oriented networking associated primarily with infotainment/media systems.

### FlexRay
High-speed deterministic networking for timing-sensitive applications. The handbook describes speeds up to approximately **10 Mbps**, with bus/star topologies and time-division communication. citehttps://opengarages.org/handbook/ebook/

### Automotive Ethernet
Ethernet-based networking increasingly important for high-bandwidth vehicle systems. The cybersecurity model expands toward IP networking, switches, segmentation, and gateway controls. citehttps://opengarages.org/handbook/ebook/

## 6. Protocol Comparison

| Protocol | Medium | Approx. speed | Communication model | Typical role |
|---|---|---:|---|---|
| CAN | Differential pair | Up to ~1 Mbps in common classic deployments | Multi-node / arbitration | ECU control |
| LIN | Single wire | Up to ~20 Kbps | Master/slave | Low-cost body functions |
| FlexRay | Twisted pair | Up to ~10 Mbps | Time-scheduled | Deterministic systems |
| MOST | Optical/electrical variants | Version-dependent | Network/ring | Infotainment |
| Automotive Ethernet | Ethernet PHY | Implementation-dependent | Switched | High-bandwidth systems |
| SAE J1850 | Single/differential variants | Legacy | Bus | Legacy vehicles |

Values are simplified study references; exact implementations vary.

## 7. Cybersecurity Architecture

The key Day 2 lesson is **network segmentation**.

```text
External Interfaces
       |
   [Gateway]
    /  |  \
   /   |   \
 CAN  LIN  Ethernet
  |    |      |
Power Body   IVI/ADAS
ECUs  ECUs   systems
```

For every bus ask:

1. Which interfaces can reach it?
2. Which gateway controls that path?
3. Which messages are accepted?
4. Which ECU functions depend on them?
5. What happens when unexpected traffic arrives?

## 8. Safe Virtual CAN Lab

Use a Linux virtual CAN interface rather than a real vehicle:

```bash
sudo modprobe can
sudo modprobe vcan
sudo ip link add dev vcan0 type vcan
sudo ip link set up vcan0
ip -details link show vcan0
```

Concept:

```text
Linux → SocketCAN → vcan0 → synthetic CAN traffic
```

This will become the basis of the SocketCAN work in Day 3.

## 9. Exercises

### Exercise A — Protocol Identification

For CAN, LIN, FlexRay, MOST, J1850, and Automotive Ethernet record: medium, speed, topology/model, use case, and security concern.

### Exercise B — Synthetic CAN Frame

For `0x321#1122334455667788`, identify:

- Arbitration ID
- DLC
- Payload byte count
- Whether the identifier value fits the standard 11-bit range
- What can and cannot be inferred about payload meaning

### Exercise C — Architecture

Draw a vehicle containing one external interface, one gateway, CAN, LIN, Ethernet, and at least three ECUs. Mark trust boundaries.

## Interview Questions

1. What is CAN?
2. What are CANH and CANL?
3. Why use differential signaling?
4. What is an arbitration ID?
5. What is DLC?
6. Standard vs extended CAN?
7. Why is CAN broadcast-oriented?
8. What is ISO-TP?
9. CAN vs LIN?
10. What is FlexRay used for?
11. Why is Automotive Ethernet important?
12. Why are gateways security-critical?
13. Is OBD-II the same as CAN?
14. Can every vehicle bus be reached through OBD-II?
15. Why must protocol analysis consider the whole vehicle architecture?

## Deliverables

- [x] Chapter 2 study notes
- [x] CAN fundamentals
- [x] OBD-II fundamentals
- [x] ISO-TP fundamentals
- [x] LIN/FlexRay/Ethernet overview
- [x] Protocol comparison
- [x] Security architecture analysis
- [ ] Synthetic CAN-frame exercise evidence
- [ ] Virtual CAN lab evidence
- [ ] Architecture diagram

## Safety

Use synthetic traffic, virtual CAN, simulators, or authorized laboratory equipment. Do not transmit frames onto a real vehicle network without explicit authorization and a controlled environment.

## Status

**Day 2: In Progress — theory deliverable committed; lab evidence pending.**
