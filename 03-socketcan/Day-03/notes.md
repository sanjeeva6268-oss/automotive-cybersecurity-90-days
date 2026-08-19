# Day 3 — Notes

## Key Takeaways

1. SocketCAN provides a common Linux interface for CAN communication.
2. `can-utils` provides practical command-line utilities for interacting with CAN interfaces.
3. `vcan0` allows CAN experimentation without physical CAN hardware.
4. `candump` is useful for observing and logging CAN frames.
5. `cansend` generates a CAN frame on the selected interface.
6. `cansniffer` helps highlight changing bytes in CAN traffic.
7. Filtering reduces the amount of traffic that must be inspected.
8. A CAN ID/payload pair does not automatically reveal the semantic meaning of a signal.
9. SocketCAN knowledge connects Linux/networking skills with automotive security research.
10. Virtual CAN is the correct starting point for safe experimentation.

## Professional Workflow

```text
Linux networking
      ↓
SocketCAN
      ↓
can-utils
      ↓
Virtual CAN
      ↓
Synthetic traffic
      ↓
Capture + filter
      ↓
CAN analysis
      ↓
Reverse engineering
```

## Reflection

Day 3 moves the project from automotive protocol theory into practical vehicle-network tooling. The important lesson is that the first objective is controlled observation and reproducibility. Before attempting any security test, I should be able to create a known CAN environment, generate known frames, capture them, filter them, and explain exactly what the tooling is showing.

## Evidence To Add After Local Lab

- Terminal screenshot showing `vcan0`.
- `ip -details link show vcan0` output.
- `candump` capture.
- `cansend` commands and resulting frames.
- `cansniffer` observation.
- Synthetic `can-session.log`.

## Status

**Theory: Complete**  
**Hands-on lab: Pending local execution**
