# Day 2 — Notes and Reflection

## What I learned

- Automotive networks are composed of multiple buses rather than one universal network.
- CAN is a major ECU communication bus and uses CANH/CANL differential signaling.
- CAN frames contain an arbitration ID, control information, DLC, and data payload.
- CAN identifiers and payloads do not by themselves reveal the semantic meaning of a signal.
- OBD-II is a diagnostic access connector; it should not be treated as synonymous with CAN.
- ISO-TP enables larger messages to be transported over CAN and is important for diagnostics.
- LIN is a lower-cost, master-controlled bus commonly used for simpler functions.
- FlexRay provides deterministic, time-scheduled communication for timing-sensitive systems.
- Automotive Ethernet introduces high-bandwidth and IP-networking concepts into vehicle architectures.
- Gateways and segmentation are central to automotive cybersecurity because they control communication between network zones.

## Day 1 → Day 2 Connection

Day 1 asked:

```text
Which interfaces can reach the internal vehicle network?
```

Day 2 adds:

```text
Which network is behind that interface?
How does the gateway route traffic?
What protocol carries the communication?
```

Therefore:

```text
Attack Surface
      ↓
Receiver
      ↓
Gateway / Trust Boundary
      ↓
Vehicle Bus
      ↓
ECU
```

## Day 2 → Day 3 Connection

Day 2 establishes what CAN is. Day 3 will turn that knowledge into a controlled Linux lab using SocketCAN and `vcan0`.

## Reflection

The main lesson is that automotive cybersecurity is not only about knowing CAN commands. The important skill is understanding the architecture: external interface → gateway → bus → ECU → function. Protocol knowledge becomes useful when connected to that architecture.
