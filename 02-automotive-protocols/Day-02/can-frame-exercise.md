# Day 2 — Synthetic CAN Frame Exercise

## Frame

```text
0x321#1122334455667788
```

## Analysis

- **Arbitration ID:** `0x321`
- **DLC:** `8` bytes in this textual notation
- **Payload:** `11 22 33 44 55 66 77 88`
- **Payload length:** 8 bytes
- **Identifier range:** `0x321` fits within the 11-bit standard CAN identifier range (`0x000`–`0x7FF`).
- **Payload meaning:** cannot be determined from the frame alone.

## Security Lesson

A CAN frame's identifier and payload do not automatically tell us what vehicle function they represent. Meaning must be established through authorized documentation, controlled experiments, signal databases, or systematic reverse engineering in a lab environment.

This frame is synthetic training data and is not associated with a real vehicle.

## Result

**Exercise completed:** Yes
